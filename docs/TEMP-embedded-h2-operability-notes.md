# TEMP — Glowroot embedded: operabilità H2, UI storage, query e filtri trace

> **Stato:** documento **temporaneo** (appunti di sessione).  
> **Data:** 2026-07-31  
> **Contesto:** installazioni embedded on‑prem (clienti senza server collector dedicato).  
> **Esempio prod tipico:** Windows Server 2016, 16 GB RAM, 4 CPU, Java 8, Tomcat, `-Xms512m -Xmx10240m`, Glowroot embedded `0.14.2`, agent sulla stessa JVM.

Eliminare o promuovere in wiki/issue quando le PR corrispondenti sono aperte/mergeate.

---

## 1. Upgrade embedded — cosa sostituire

Riferimento ufficiale: [Agent Upgrade](https://github.com/glowroot/glowroot/wiki/Agent-Upgrade) (da 0.9.1+).

1. Fermare la JVM  
2. Scompattare il nuovo `glowroot-*-dist.zip` **sopra** la cartella esistente (sostituisce `glowroot.jar`, `LICENSE`, `NOTICE`, `lib/*`)  
3. Riavviare la JVM  

**Lasciare intatti:** `data/`, `config.json` / `admin.json`, `glowroot.properties`, (opz.) `logs/`.  
`tmp/` si può svuotare. Non serve ripristinare tutto da zero.

La dist ufficiale contiene essenzialmente `glowroot.jar` + `lib/` (collector embedded / HTTPS / logstash opzionali).

---

## 2. Perché embedded a 30–40 GB diventa lento (anche con SSD)

Non è (principalmente) il disco. Limiti strutturali:

| Fattore | Dettaglio |
|--------|-----------|
| Cache H2 default | `glowroot.internal.h2.cacheSize` = **8192 KB (8 MB)** — su DB da decine di GB hit rate ≈ 0 |
| Lock singolo | Una connection H2 + `synchronized` globale su read/write UI e capture |
| DELETE ≠ shrink | Il reaper cancella a chunk da 100; il file non rimpicciolisce senza **Compact/Defrag** |
| Volume tipico | Spesso `full_query_text`, metadata `trace` / aggregate in H2; i `*.capped.db` sono già hard‑capped |

Issue storiche collegate: #755 (H2 grows), #763 (compact), #749 (OOM load H2), backlog gauge/aggregati (#1035 e simili).

**Verdetto prodotto:** embedded è pensato per footprint leggero sulla stessa JVM, **non** per DB da 40 GB. Nel modello commerciale “installato dal cliente senza central”, il cliff è un problema di delivery, non di preferenza architetturale.

---

## 3. Cache H2 — pro/contro e proposta UI

### Oggi
- Solo `-Dglowroot.internal.h2.cacheSize` (KB), fissata nell’URL JDBC all’apertura (`DataSource`).  
- Forzando `8192` si **disattiva** il comportamento H2 più ragionevole (all’open, cache limitata a circa metà heap se non override esplicito).  
- H2 permette `SET CACHE_SIZE` a runtime.

### Pro di alzarla
Meno I/O, query/UI un po’ più snelle, lock tenuto meno a lungo.

### Contro
Mangia heap della **stessa JVM dell’app**; su 30–40 GB non risolve (anche 512 MB è una frazione del file); non riduce la size su disco.

### Ambiente prod tipico (16 GB RAM, Xmx 10 GB)
Il margine OS/native è già stretto (~6 GB). Non usare “10% di Xmx” (= ~1 GB).

| Impostazione | MB | Giudizio su box 16 GB / Xmx 10g |
|--------------|-----|----------------------------------|
| Auto conservativo | 64–128 | punto di partenza |
| Sweet spot tipico | 128–256 | se GC/RSS lo reggono |
| Attenzione | 256–512 | giallo |
| No | ≥512–1024 | rischio swap/GC/OOM sull’app |

### Proposta: parametrico da UI + soglie % + warning
- Scelta: `Auto` | `MB fissi` | `% di -Xmx`  
- Mostrare sempre valore effettivo calcolato e heap rilevato  
- Warning all’operatore oltre soglia (stessa JVM, GC, OOM; su DB enormi la cache aiuta poco → retention/Compact)  
- Hard clamp consigliato: es. `min(config, 3–5% Xmx, 256 MB)` per preset prod shared‑JVM  
- **Non** scalare la cache linearmente con la size del file H2  

---

## 4. UI Storage — grafico spazio H2

Già esiste (embedded): **Administration → Storage** → Analyze H2 disk space  
API: `h2DataFileSize` + lista tabelle (`name`, `bytes`, `rows`) via `disk_space_used`.  
Bottoni: Defrag / Compact (in codice attuale entrambi finiscono su `shutdown compact` — non promettere due effetti diversi finché non si distinguono).

### MVP proposto
Donut / stacked bar:

| Fetta | Calcolo | Significato |
|-------|---------|-------------|
| File H2 su disco | size file | ciò che vede l’OS |
| Dati vivi | `sum(disk_space_used(table))` | ancora referenziato |
| Recuperabile con Compact | `fileSize − sum(table)` | buco interno post‑DELETE |

Sotto: top tabelle; CTA “Compact consigliato (~N GB)”.  
Fase 2: stima “recuperabile abbassando retention” (non è free space — etichettare bene).  
I `*.capped.db` in un secondo anello, non mischiati col reclaim H2.

---

## 5. Piano PR (ordine)

1. **[#1188](https://github.com/glowroot/glowroot/pull/1188)** — Deployment profile Dev/Prod (embedded): retention/capped/profiling più magri. Ship così.  
2. **PR follow‑up** — cache H2 parametrica (Auto/MB/% + warning) + grafico Storage (file / live / reclaimable); opz. Prod setta anche default cache conservativo.  
3. **PR successiva** — review read‑path / query UI embedded (non “riscrivere tutto”), guidata da schermate lente + EXPLAIN dove possibile.

Tema unificato: *embedded must stay usable for long‑lived on‑prem installs without a dedicated collector*.

---

## 6. Query UI candidate da rivedere (priorità)

Path tipici: `agent/embedded/.../repo/AggregateDao.java`, `TraceDao.java`, `TracePointQueryBuilder.java`; UI in `TransactionCommonService`, `TracePointJsonService`, `ErrorJsonService`, `ReportJsonService`.

### Alta
1. **Queries / Service calls / Profiles** — 1 SQL sui `*_capped_id` + **N letture capped** sotto lock (`mergeQueriesInto` / `mergeServiceCallsInto` / `mergeProfilesInto`).  
2. **Slow / Error points** — range `trace` + `ORDER BY duration_nanos DESC`; filtri `upper(...) LIKE` / attribute → no index.  
3. **Error messages** — `GROUP BY error_message` (+ LIKE).  
4. **Slow/Error count** — spesso seconda scansione sullo stesso range dei points.

### Media
5. Transaction list / summary su `aggregate_tn_rollup_*`  
6. Overview / percentiles / throughput e report su range lunghi / rollup troppo fine  
7. Filtro attribute: JOIN `trace_attribute`, indice solo su `trace_id`  
8. Admin **Analyze trace counts** — `GROUP BY` su tutta `trace`

### Bassa / ops
9. Analyze H2 disk space (`disk_space_used` per tabella)  
10. Reaper `deleteBefore` (compete col lock; non è “query UI”)  
11. Lookup by id — già ok, non candidate

Pattern: N+1 capped, `upper(col) LIKE`, sort su duration, doppio count+points, rollup level troppo basso.

---

## 7. Come profilare / dimostrare il problema

### Ideale
Copia `data/` → offline viewer stessa versione → Analyze + EXPLAIN.  
**Vincolo reale:** spesso **impossibile copiare** 30–40 GB off‑host.

### Senza copia (piano pratico)
1. **Prove gratis:** size file H2 + capped; screenshot Storage/retention; DevTools Network (URL, range, ms) su Queries / Slow traces / Errors; log backlog (`not storing an aggregate…` / gauge backlog); correlazione “quando era 5 GB ok, a 30+ GB lento”.  
2. **Maintenance breve:** solo **Analyze H2 disk space** (no EXPLAIN live); screenshot top tabelle.  
3. **Confronti relativi live (impatto limitato):** stesso click su range 1h vs 7g vs 30g; Overview vs Queries; Slow traces con/senza LIKE/attribute.  
4. **Non fare in prod live:** `h2 console` sullo stesso `data` mentre Tomcat gira; Analyze/Compact in punta; secondo processo sullo stesso path (lock).

Offline viewer (se si riesce almeno uno snapshot locale):

```bat
java -jar glowroot.jar
java -jar glowroot.jar h2 console
```

Frase chiave per issue: *Measurements in place / short maintenance Analyze; cannot copy production DB off-host; latency correlates with H2 size, not disk speed.*

---

## 8. Filtri trace — entries e caso Encounter / CHA VQR

### Cosa si può filtrare (Traces)
Durata, **headline**, **error message**, **user**, **custom attribute** (nome + valore).

### Cosa **non** si può
LIKE / contains sul testo delle **entries** (es.  
`log info: e.a.h.c.e.a.s.EncounterVqrFacade - Time to execute CHA VQR : 7494 ms`).  
Le entries stanno nei capped DB come blob, non sono colonne H2 ricercabili.

### Esempio reale (2026-07-31)
- Transaction type: `Web`  
- Transaction name: `/H2O_HH/rest/fhir/Encounter`  
- Duration: ~31607 ms  
- Breakdown: quasi tutto **jdbc query** (~31469 ms, 6 query); http client minore; aux thread ~30038 ms **waited**  
- User: `ParametriSessione@…` (inutile per filtro)  
- Query string FHIR con `class=…|CHA`, ward, location, `_include`, ecc.

**Come cercare “CHA lente” oggi:**
1. Filtra transaction = `/H2O_HH/rest/fhir/Encounter` + duration alta (es. > 5–10 s)  
2. Apri i trace e Ctrl+F nelle entries su `CHA VQR`  
3. Oppure tab **Queries** aggregata della transaction (SQL), non il testo del log  

**Per filtrarlo in prodotto in futuro:** custom attribute (es. `fhir.class=CHA`, `chaVqrMs=…`) o headline più parlante; altrimenti correlazione da log app.

---

## 9. Checklist issue / PR (bozza)

### Follow‑up a #1188 (cache + grafico)
- [ ] H2 cache in Admin/Storage (e/o Deployment profile): Auto / MB / %  
- [ ] Warning soglie + clamp sicuro su shared JVM  
- [ ] Chart: file vs live vs compact‑reclaimable  
- [ ] Top tabelle; CTA Compact  
- [ ] Prod preset aggiornato con default cache conservativo  

### PR query UI
- [ ] Misure su schermate: Queries, Slow traces, Errors  
- [ ] Priorità: merge capped N+1, TracePoint + LIKE/attribute  
- [ ] Non scope: rewrite overview base, path by‑id  

### Evidenza ops (senza copia)
- [ ] Size H2 + capped + retention  
- [ ] Timing Network per pagina/range  
- [ ] Analyze H2 in maintenance (se possibile)  
- [ ] Log backlog se presenti  

---

## 10. Riferimenti codice (repo locale)

- `agent/dist/dist.xml` — contenuto dist  
- `agent/embedded/.../util/DataSource.java` — cache, lock, deleteBefore, compact  
- `agent/embedded/.../repo/AggregateDao.java` — merge queries/profiles  
- `agent/embedded/.../repo/TraceDao.java` / `TracePointQueryBuilder.java` — slow/error points  
- `agent/embedded/.../ToolMain.java` — `h2 console` / offline viewer  
- `ui/app/views/admin/storage.html` — Analyze / Compact / Defrag  
- `ui/app/views/transaction/traces.html` — filtri trace  
- PR: https://github.com/glowroot/glowroot/pull/1188  

---

*Fine documento temporaneo.*
