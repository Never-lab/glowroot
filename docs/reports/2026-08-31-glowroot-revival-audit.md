# Glowroot — Audit di revival e popolarità

| Campo | Valore |
|---|---|
| Data | 2026-08-31 |
| Autore | Nicholas Antinori (`Never-lab` / `JustKaneki`) |
| Repo analizzato | [glowroot/glowroot](https://github.com/glowroot/glowroot) |
| Fork di lavoro | [Never-lab/glowroot](https://github.com/Never-lab/glowroot) |
| Versione HEAD locale | `0.14.8-beta.5-SNAPSHOT` |
| Scopo | Stato reale del progetto + piano concreto per aumentare adozione e visibilità |

---

## TL;DR brutale

Glowroot **non è un progetto morto**. Il codice si muove, la CI passa, le release escono, Java 25 è supportato. Il problema è un altro: **il prodotto è tecnicamente vivo ma commercialmente e socialmente invisibile**.

- **~1.356 star** vs SkyWalking ~24.900 e Pinpoint ~13.860 — ordine di grandezza 10–18× indietro.
- **Il sito pubblico (`glowroot.org`) mostra ancora la 0.14.0** — sei anni di distanza dal messaggio GitHub.
- **La demo pubblica (`demo.glowroot.org`) risponde HTTP 522** — down almeno da gennaio 2025 ([#1102](https://github.com/glowroot/glowroot/issues/1102)).
- **Maven Central è abbandonato**: ultimo artifact `org.glowroot:glowroot-agent` = `0.14.0-beta.3` (2022). Chi cerca su Maven non trova nulla di recente.
- **Central collector quasi ignorato**: release `v0.14.7` → agent zip ~33.358 download cumulativi, central zip ~241 (**rapporto ~138:1**).
- **Backlog issue marcio**: su 190 issue aperte campionate, **130 (>68%) hanno più di 3 anni**; solo **9 label** GitHub; triage quasi assente.
- **Governance fantasma**: `pom.xml` elenca solo Trask Stalnaker come developer; Trask non committa più su `main`; maintainer de facto = Sylvere Richard (`nowheresly`) + commit recenti anche da te (`JustKaneki`).
- **7 PR aperte upstream, tutte tue** — bottleneck di review/merge, non di produzione codice.
- **Stack UI EOL**: AngularJS 1.7.9 (EOL 2022), Bower (deprecated), Grunt. Funziona, ma è un segnale visibile di “2018”.
- **Zero OpenTelemetry** nel codebase. Nel 2026 questo esclude Glowroot da molti shortlist architetturali.

**Verdetto:** il vantaggio competitivo reale (embedded, basso overhead, `-javaagent` in 5 minuti) **esiste ancora** ed è sottovalutato dal mercato perché onboarding, marketing, demo e ecosistema sono fermi o assenti. Non serve riscrivere Glowroot in OTel-native per crescere — serve **rendere provabile e trovabile** ciò che già funziona, poi colmare i gap ecosistema uno per uno.

---

## Metodologia

Dati raccolti il **2026-08-31** da:

| Fonte | Cosa |
|---|---|
| GitHub API | stars, fork, issue, PR, release, contributor, discussions, CI |
| Repo locale | `/home/nicholas/Scrivania/Lavoro/Glowroot` — struttura moduli, UI stack, test count, Dockerfile |
| HTTP probe | `glowroot.org`, `demo.glowroot.org` |
| Maven Search API | presenza artifact `org.glowroot` |
| Confronto | SkyWalking, Pinpoint, Scouter su GitHub stars |

**Nota sul clone locale:** il ref `upstream/main` in locale ha solo 34 commit (ref corrotto/shallow). I dati storici commit usano **GitHub API**, non `git log` locale.

---

## 1. Numeri di riferimento

### 1.1 GitHub (2026-08-31)

| Metrica | Glowroot | SkyWalking | Pinpoint | Scouter |
|---|---:|---:|---:|---:|
| Stars | 1.356 | 24.936 | 13.860 | 2.181 |
| Fork | 337 | — | — | — |
| Open issues (counter repo) | 204 | — | — | — |
| Issue aperte campionate | 190 | — | — | — |
| Watchers | 47 | — | — | — |
| Contributor (API) | 30 | — | — | — |
| Discussions totali | 59 | — | — | — |
| PR aperte | 14 | — | — | — |
| License | Apache-2.0 | Apache-2.0 | Apache-2.0 | Apache-2.0 |
| Created | 2014-01-29 | — | — | — |
| Last push | 2026-08-30 | — | — | — |

### 1.2 Download release GitHub (cumulativi, non unici)

| Tag | Tipo | Data | Agent zip DL | Central zip DL |
|---|---|---|---:|---:|
| v0.14.8-beta.4 | pre-release | 2026-08-02 | 371 | 15 |
| v0.14.8-beta.3 | pre-release | 2026-06-08 | 373 | 30 |
| v0.14.7 | **stable** | 2026-05-11 | 33.358 | 241 |
| v0.14.6 | stable | 2026-03-24 | 22.254 | — |
| v0.14.5 | stable | 2026-03-08 | 1.793 | — |
| v0.14.4 | stable | 2025-05-25 | 131.752 | — |
| v0.14.2 | stable | 2024-04-07 | 251.303 | — |
| v0.14.0 | stable | 2023-05-14 | 268.537 | — |

**Lettura onesta:**

- I counter GitHub sono cumulativi e gonfiati da CI/mirror, ma il **trend relativo** conta: la 0.14.4 ha fatto numeri enormi (probabilmente link rotto altrove + upgrade wave), la 0.14.7 ne ha molti meno in 3 mesi.
- Le beta 0.14.8 hanno ~370 download agent ciascuna — utenti coraggiosi, non massa.
- **Central è un prodotto di nicchia dentro una nicchia.** Investire solo sull'agent embedded ha senso commerciale; ignorare Central però limita il posizionamento “multi-JVM enterprise”.

### 1.3 Codebase (locale)

| Metrica | Valore |
|---|---|
| File `.java` | 1.523 |
| Linee Java (totale `wc -l`) | ~8.904 (esclusi target; cifra bassa perché molto codice è generato/shaded altrove nel build completo) |
| Plugin agent | 27 |
| File test `*Test.java` / `*IT.java` | 269 |
| File integration-tests | 71 |
| File benchmark JMH | 18 |
| Moduli Maven (top-level + agent) | 54 `pom.xml` |
| Agent dist zip (build locale) | ~41 MB |
| Versione in sviluppo | `0.14.8-beta.5-SNAPSHOT` |

### 1.4 Stack UI (da `ui/bower.json`, `ui/package.json`)

| Componente | Versione | Stato |
|---|---|---|
| AngularJS | 1.7.9 | **EOL** (LTS terminato 2022-01-01) |
| angular-ui-router | 1.0.20 | legacy |
| Bootstrap | fork `trask/bootstrap#glowroot-0.13.1` | fork privato |
| Bower | sì | **deprecated** dal 2017 |
| Grunt | build pipeline | legacy |
| Node deps | grunt, bower, … | nessun framework moderno |

---

## 2. Audit tecnico

### 2.1 Cosa funziona davvero

| Area | Evidenza | Giudizio |
|---|---|---|
| CI | Matrix Java 8/17/21/25 × testGroup 1–4 × shaded/unshaded × javaagent/local harness | **Robusto** — raro in OSS di questa età |
| Java moderno | Release note: Java 25, JEP 512 main, virtual threads (#1125 in PR), ASM 9.10.1 | **Competitivo** per agent Java |
| Architettura agent | 27 plugin, shading `org.glowroot.agent.shaded.*`, IT harness dedicato | **Solido**, estensibile |
| Embedded mode | H2 locale, UI in-process, zero infra | **Killer feature** — il vero differenziatore |
| Benchmark | Modulo `agent/benchmarks` con JMH | Presente ma **non pubblicizzato** |
| Docker Central | `central/Dockerfile`, JDK 25, OpenShift-friendly permissions | Esiste; **non è la storia di onboarding principale** |

### 2.2 Problemi tecnici che frenano adozione

| Problema | Evidenza | Impatto |
|---|---|---|
| **Nessun OTel** | grep `OpenTelemetry` nel repo = 0 match | Escluso da architetture 2026 “OTel-first” |
| **Maven Central fermo** | `glowroot-agent` @ `0.14.0-beta.3`, timestamp 2022 | Chi usa Maven/Gradle dependency lookup non trova Glowroot |
| **Due agent sullo stesso JVM** | README: “Usually no” | Normale per bytecode agents, ma blocca migrazioni da altri APM |
| **Central richiede Cassandra** | wiki + Dockerfile + issue DNS/reconnect (#1151) | Barriera ops alta; spiega il rapporto 138:1 download |
| **Issue gravi recenti aperte** | #1162 ClassLoader leak Metaspace OOM; #1180 CPU/GC regression 0.14.4→0.14.7; #1165 flamegraph line numbers | Fiducia produzione scossa anche con release attive |
| **Checker Framework CI disabilitato** | `.github/workflows/build.yml` — job `checker` commentato | Qualità statica non enforced in CI |
| **No Dependabot** | `.github/dependabot.yml` assente | CVE nelle dipendenze: responsabilità manuale |
| **Perpetual beta** | 0.14.8-beta.4 latest tag; stable 0.14.7 di maggio | Team conservativi rimandano upgrade |
| **SLF4J 1.7 + Logback 1.2** nel parent POM | `slf4j.version` 1.7.36, `logback.version` 1.2.13 | SLF4J 2.x support aggiunto nelle note release ma stack parent legacy |

### 2.3 Commit recenti upstream (GitHub API, 2026-07/08)

Autori effettivi sul branch `main`:

| Autore GitHub | Nome | Commit recenti (esempi) |
|---|---|---|
| `JustKaneki` | (email: `nicholasantinori8@gmail.com`) | trace filters, woven LocalVariableTable, JUL NPE, health check |
| `Sylvere Richard` | nowheresly | readiness probe Cassandra, Infinispan update |
| `Never-lab` | Nicholas Antinori | PR mergeate (#1185, #1189, #1203) |

**Trasparenza:** `JustKaneki` e `Never-lab` sono lo stesso operatore (Nicholas). I commit diretti su `main` passano da `JustKaneki`; le PR passano da `Never-lab`. Per un osservatore esterno sembra più gente di quanta ce ne sia — o confusione di identità.

`trask` (4.057 commit storici): **nessun commit recente su `main`**. Il bus factor nominale (Trask) ≠ bus factor reale (Sylvere + Nicholas).

---

## 3. Audit prodotto e UX

### 3.1 First impression — fallisce

| Touchpoint | Stato 2026-08-31 | Effetto |
|---|---|---|
| `glowroot.org` | HTTP 200; pagina mostra download **`glowroot-0.14.0-dist.zip`** | Visitatore conclude “abbandonato” |
| `demo.glowroot.org` | HTTP **522** (Cloudflare origin timeout) | Zero try-before-download |
| GitHub README | Migliorato su branch `docs/readme-product-first` (**non mergeato** su upstream `main` al momento audit) | Upstream README già decente ma demo link rotto |
| Wayback demo | Snapshot 2023-03-25 | Insufficiente per UI moderna e feature 0.14.x |
| Twitter `@glowroot` | linkato nel README | Canale legacy, attività non verificata |

**Conclusione:** un developer che scopre Glowroot oggi ha **tre segnali negativi consecutivi** (sito vecchio → demo morta → beta tag) prima ancora di leggere il codice.

### 3.2 Proposta di valore — ancora valida ma non difesa

Glowroot vince dove:

1. **Un team Java** (non polyglot) con **5–50 servizi**.
2. **Non vuole** Elasticsearch + HBase + OAP cluster + Grafana.
3. **Deve vedere** SQL lento, trace, flame graph **oggi**, non dopo un progetto osservabilità.

Glowroot perde dove:

1. Serve **distributed tracing cross-service** nativo (SkyWalking, Jaeger, Tempo).
2. Architettura **OpenTelemetry-native** obbligatoria.
3. **Kubernetes-first** con operator, Helm, sidecar pattern documentato.
4. **Metriche in Prometheus/Grafana** come standard di team.

### 3.3 Feature gap vs aspettative mercato 2026

| Feature | Stato Glowroot | Priorità per popolarità |
|---|---|---|
| OpenTelemetry export (OTLP) | Assente | Alta (ingresso nelle shortlist) |
| Prometheus metrics scrape | Quasi assente (issue #503 StatsD, #503 vecchia) | Alta |
| Spring Boot starter | Assente | **Altissima** (70%+ nuovi progetti Java) |
| Helm chart / K8s guide | Wiki parziale, no chart ufficiale | Media-alta |
| Docker one-liner demo agent | Assente (solo Central Dockerfile) | Alta |
| Grafana dashboard | Assente | Media |
| Log correlation | Limitata (logger plugin, no Loki/ELK story) | Media |
| Service map / topology | Richiesto (#1140, ago 2025) | Media |
| Dark mode UI | — | Bassa (percezione, non funzione) |

---

## 4. Audit distribuzione e discovery

### 4.1 Canali di distribuzione attuali

| Canale | Stato | Giudizio |
|---|---|---|
| GitHub Releases zip | **Primario**, funzionante | OK ma friction alta vs Maven/Docker |
| Maven Central | **Fermo al 2022** | Grave |
| Docker Hub org `glowroot` | 2 repo (API) | Esiste per Central; non è onboarding agent |
| Wiki GitHub | Estesa, utile | Buona per chi già adopera; non SEO |
| Google Group | Linkato, attività non verificata | Legacy |
| Blog / release notes | Solo GitHub Releases | Insufficiente per discovery esterna |
| Articoli comparativi 2026 | Glowroot citato come “lightweight Java” | Presenza **passiva**, non guidata |

### 4.2 SEO e citabilità

Glowroot compare in articoli “Best self-hosted APM 2026” come opzione **Java-only, lowest overhead**. Non compare nelle liste **OpenTelemetry / cloud-native**. Due ecosistemi diversi — il secondo cresce più veloce.

**Star history:** crescita lenta e piatta rispetto a SkyWalking. Senza demo + contenuti + integrazioni ecosistema, le star non decollano da sole.

---

## 5. Audit community e governance

### 5.1 Contributor

| Login | Commit (API) | Ruolo reale |
|---|---:|---|
| trask | 4.057 | Fondatore, **inattivo** su main |
| nowheresly (Sylvere Richard) | 366 | **Maintainer de facto**, merge/release |
| Never-lab (Nicholas) | 31 | Contributor attivo, PR ad alto volume |
| github-actions[bot] | 47 | Release automation |
| Altri 26 | 1–15 ciascuno | Sporadici |

**Bus factor effettivo: 2.** Se Sylvere smette, il progetto torna orphan nonostante 1.356 star.

### 5.2 Issue tracker — stato disastroso (numeri, non opinioni)

| Metrica | Valore |
|---|---|
| Issue aperte campionate | 190 |
| Età > 3 anni | 130 (68,4%) |
| Età 1–3 anni | 54 (28,4%) |
| Età < 1 anno | 6 (3,2%) |
| Label disponibili | **9** (`bug`, `enhancement`, `regression`, `ui`, `plugin`, `wishlist`, `dependencies`, `discussion`, `awaiting feedback`) |
| Issue senza commenti (campione) | 0 |

**Keyword nelle issue aperte (titolo):**

| Keyword | Count |
|---|---:|
| central | 17 |
| ui | 10 |
| cassandra | 8 |
| h2 | 4 |
| spring boot | 3 |
| docker | 3 |
| kubernetes | 2 |
| prometheus | 2 |
| virtual thread | 1 |
| embedded | 1 |

**Issue recenti (< 1 anno) — segnale qualità:**

| # | Data | Titolo |
|---|---|---|
| 1183 | 2026-06-29 | 0.14.6 not starting with Apache Procrun Windows Service |
| 1180 | 2026-06-17 | CPU load and GC pressure after 0.14.4 → 0.14.7 |
| 1165 | 2026-02-27 | Flamegraph same function at two line numbers |
| 1162 | 2026-02-20 | ClassLoader leak Metaspace OOM |
| 1151 | 2025-09-18 | Cannot reconnect to Cassandra when DNS changes |

### 5.3 Discussions

| Categoria | Count |
|---|---:|
| General | 27 |
| Q&A | 27 |
| Ideas | 5 |
| **Totale** | **59** |

59 discussioni totali in un progetto di 12 anni = **canale sottoutilizzato**. Il README recente (branch non mergeato) reindirizza Q&A → Discussions — direzione giusta, ma serve risposta attiva.

### 5.4 Governance formale — assente

| Documento | Presente |
|---|---|
| CODEOWNERS | No |
| GOVERNANCE.md | No |
| ROADMAP.md | No |
| MAINTAINERS.md | No |
| SECURITY.md | Non verificato in audit |
| Dependabot | No |

`pom.xml` `<developers>`: solo Trask Stalnaker, email 2011-era. **Non riflette la realtà 2026.**

### 5.5 PR backlog — collo di bottiglia umano

**PR mergeate da `Never-lab` (upstream):** 3 (#1185, #1189, #1203) — luglio 2026.

**PR aperte da `Never-lab` (upstream, 2026-08-31):** 7

| # | Titolo | Data |
|---|---|---|
| 1220 | Embedded scale stress harness + H2 index/SQL hardening | 2026-08-03 |
| 1217 | Central delete-agent-meta CLI + Admin Storage UI | 2026-08-02 |
| 1214 | Central JVM otherwise cascade | 2026-08-01 |
| 1191 | Fix thread profiling on JDK virtual threads (#1125) | 2026-07-20 |
| 1190 | Modernize README product-first | 2026-07-20 |
| 1188 | Embedded Deployment profile presets | 2026-07-18 |
| 1186 | UI enrich: transaction charts, JVM pages | 2026-07-18 |

**Lettura:** stai producendo più di quanto il maintainer riesca a revieware/mergeare. Popolarità del progetto **non può crescere** se il codice utile resta in PR aperte per settimane.

---

## 6. Confronto competitivo onesto

### 6.1 Matrice posizionamento

| Criterio | Glowroot | SkyWalking | Pinpoint |
|---|---|---|---|
| Setup minimo | **1 zip + javaagent** | Cluster OAP + storage | Collector + HBase |
| Overhead claim | **microsecondi** (benchmark interni) | ~3% | ~3% |
| Java depth | **Alta** (weaving, 27 plugin) | Alta | **Molto alta** |
| Polyglot | No | Sì | Java/PHP focus |
| OTel | No | Sì (nativo) | No |
| Stars | 1.3k | 25k | 14k |
| Foundation / governance | Contributor-driven | **Apache** | Naver OSS |
| UI | Built-in, dated | Built-in, modern-ish | Built-in, dated |
| Self-hosted costo ops | **Basso** (embedded) | Medio-alto | Alto |

### 6.2 Nicchia difendibile

Glowroot **non deve** competere head-to-head con SkyWalking. Deve **dominare**:

> “APM Java self-hosted per team piccoli/medi che vogliono risultati in 5 minuti senza un team platform.”

Questa nicchia esiste. Articoli 2026 la citano esplicitamente. Nessuno la sta **presidiando** con demo, starter, e case study.

### 6.3 Nicchia persa (se non agisce)

- Team che partono greenfield con **OTel + Grafana** nel 2026 non considereranno mai Glowroot.
- Team che restano Java monolith / Tomcat / Spring Boot tradizionale **sì** — se lo trovano.

---

## 7. Cosa NON è il problema

Per evitare lavoro inutile:

| Falso problema | Realtà |
|---|---|
| “Serve rewrite in Rust/Go” | No. Il valore è nel weaving Java e nei plugin. |
| “Serve UI React domani” | No. Serve demo funzionante domani. UI moderna = fase 3. |
| “Serve battere SkyWalking in star” | No. Target realistico: 2.500 star in 12 mesi (+84%). |
| “Il codice è abbandonato” | Falso. CI verde, release 2026, Java 25. |
| “Central va prioritizzato” | Solo se target enterprise multi-JVM. I numeri dicono embedded first. |

---

## 8. Piano d'azione — workstream atomici

Ogni workstream ha: **obiettivo**, **output misurabile**, **owner suggerito**, **effort**, **dipendenze**.

---

### WS-1: Demo pubblica

| Campo | Valore |
|---|---|
| Obiettivo | Try-before-download in < 60 secondi |
| Output | URL live con app Spring Boot instrumentata + dati sintetici (`UiSandboxMain`-like) |
| Opzioni | (A) Ripristinare `demo.glowroot.org` (B) Fly.io/Render (C) Docker `docker run glowroot/demo` |
| Owner | Sylvere (infra DNS) + Nicholas (app demo) |
| Effort | 2–5 giorni |
| Metrica | Uptime 99%; issue #1102 chiusa |
| Bloccante per | ogni altra iniziativa marketing |

**Minimo accettabile se infra non disponibile:** video/GIF 90s nel README + Compose file che avvia sandbox locale con un comando.

---

### WS-2: Allineamento sito e documentazione pubblica

| Campo | Valore |
|---|---|
| Obiettivo | `glowroot.org` coerente con GitHub Releases |
| Output | Download link → latest stable; sezione “0.14.x highlights”; link demo |
| Owner | Sylvere (DNS/S3) o chi ha accesso AWS |
| Effort | 1 giorno |
| Metrica | Sito referenzia ≥ 0.14.7 |

**Nel fork (subito):** merge PR #1190 README + wiki sync.

---

### WS-3: Release stable 0.14.8

| Campo | Valore |
|---|---|
| Obiettivo | Uscire dal loop beta percepito |
| Output | Tag `v0.14.8` stable, changelog, announcement Discussion |
| Criteri uscita | CI verde; nessun blocker P0 aperto; smoke test embedded + central |
| Owner | Sylvere (release manager) |
| Effort | 3–7 giorni (test + triage) |
| Metrica | `releases/latest` = stable, non beta |

---

### WS-4: Spring Boot starter

| Campo | Valore |
|---|---|
| Obiettivo | `-javaagent` discoverable da ecosistema Spring |
| Output | `org.glowroot:glowroot-spring-boot-starter` o doc + Gradle/Maven snippet ufficiale |
| Owner | Nicholas (già conosce codebase) |
| Effort | 3–10 giorni |
| Metrica | Mention in Spring ecosystem; download Maven misurabile |
| Dipendenze | WS-3 (versione stable) per non pubblicare beta |

---

### WS-5: Export metriche (Prometheus / StatsD)

| Campo | Valore |
|---|---|
| Obiettivo | Integrazione con stack osservabilità esistente senza sostituire Glowroot UI |
| Output | Endpoint `/metrics` Prometheus o exporter StatsD (issue #503 esiste) |
| Owner | Contributor + review Sylvere |
| Effort | 1–2 settimane |
| Metrica | Dashboard Grafana template in repo |

---

### WS-6: Issue triage e hygiene

| Campo | Valore |
|---|---|
| Obiettivo | Backlog leggibile |
| Output | Chiudere stale >3y senza riproduzione; label `P0/P1/good-first-issue`; template issue |
| Owner | Nicholas (già `docs/1197-issue-knowledge-map`) + Sylvere |
| Effort | 2–3 giorni iniziali, poi 1h/settimana |
| Metrica | Open issues < 100; < 40% older than 3y |

---

### WS-7: Governance minima

| Campo | Valore |
|---|---|
| Obiettivo | Chiarire chi decide e chi risponde |
| Output | `MAINTAINERS.md` (Sylvere + Nicholas); aggiornare `pom.xml` developers; `CODEOWNERS` |
| Owner | Sylvere (accettazione) + Nicholas (PR) |
| Effort | 2 ore |
| Metrica | PR review SLA dichiarato (es. 7 giorni) |

---

### WS-8: Contenuti e discovery

| Campo | Valore |
|---|---|
| Obiettivo | Essere citati attivamente, non solo passivamente |
| Output | (1) Benchmark JMH pubblicati (2) Case study produzione Afea (3) Post r/java, Dev.to (4) PR su awesome-java |
| Owner | Nicholas |
| Effort | 1 settimana spread nel tempo |
| Metrica | 3+ backlink da articoli/blog; +200 star in 6 mesi |

**Hook contenuto pronti oggi:** “Java 25 support”, “virtual threads profiling”, “embedded APM senza Cassandra”.

---

### WS-9: OpenTelemetry bridge (medio termine)

| Campo | Valore |
|---|---|
| Obiettivo | Non restare fuori dalle architetture OTel |
| Output | Export trace OTLP (read-only); doc “Glowroot UI + OTel backend” |
| Owner | Team (feature grande) |
| Effort | 4–8 settimane |
| Metrica | Issue “OTel” chiuse; mention in OTel registry/docs |
| Nota | **Non** è prerequisito per WS-1/2/3. È prerequisito per crescita oltre la nicchia Java tradizionale. |

---

### WS-10: Sblocco PR backlog (Nicholas)

| Campo | Valore |
|---|---|
| Obiettivo | Codice mergeato > codice proposto |
| Azione | Prioritizzare 3 PR max: #1190 (README), #1191 (virtual threads), #1188 (deployment presets) |
| Split | PR grandi (#1186 UI, #1220 stress) → spezzare in PR reviewabili < 400 righe |
| Owner | Nicholas (split) + Sylvere (review) |
| Metrica | PR aperte < 3 |

---

## 9. Priorità — ordine di esecuzione

```
Fase 0 (settimana 1)     WS-10  Sbloccare PR backlog
                         WS-2   README merge + sito (se accesso)
                         WS-1   Demo minima (Compose o live)

Fase 1 (settimana 2–4)   WS-3   Stable 0.14.8
                         WS-6   Issue triage
                         WS-7   MAINTAINERS.md

Fase 2 (mese 2–3)        WS-4   Spring Boot starter
                         WS-5   Prometheus export
                         WS-8   Contenuti + benchmark pubblici

Fase 3 (mese 4–12)       WS-9   OTel bridge
                         UI modernization (workstream non dettagliato — solo dopo demo+starter)
```

---

## 10. Metriche di successo (12 mesi)

| Metrica | Baseline 2026-08-31 | Target 2027-08-31 | Come misurare |
|---|---:|---:|---|
| GitHub stars | 1.356 | 2.500+ | GitHub |
| Download stable release (primi 90gg) | ~33k cumul. (0.14.7 totale) | > 5k primi 90gg nuova stable | GitHub API assets |
| Demo uptime | 0% | 99% | Uptime probe |
| Open issues | ~190 | < 100 | GitHub |
| Issue > 3 anni (% ) | 68% | < 30% | Script |
| PR aperte Never-lab | 7 | ≤ 2 | GitHub |
| Contributor attivi/anno | 2–3 | ≥ 8 | GitHub |
| Maven Central latest | 0.14.0-beta.3 | ≥ stable corrente | search.maven.org |
| Menzioni blog comparativi | passiva | 3+ articoli con case study | manuale |

---

## 11. Ruolo Nicholas / Never-lab — situazione attuale

### Cosa hai già fatto (upstream, verificato)

- 3 PR mergeate (bugfix ad alta qualità): exported traces, Jackson nesting, WeakRef queue.
- 7 PR aperte con feature docs/UI/infra — **valore alto, merge zero da 18–45 giorni**.
- Commit diretti su `main` come `JustKaneki` (trace filters, health check, JUL NPE).
- Branch fork `docs/readme-product-first` con README product-first e star history.

### Cosa NON conviene fare (onesto)

- Aprire altre PR grandi finché WS-10 non sblocca il backlog.
- Investire mesi in UI rewrite prima di demo + stable release.
- Presentarsi come “maintainer” senza accordo esplicito con Sylvere — rischio friction community.

### Cosa conviene fare

1. **Accordo esplicito** con Sylvere su ruolo (co-maintainer? docs/release helper?).
2. **Spezzare** #1186 e #1220.
3. **Portare** case study Afea (JVM/SQL analysis che già fai) → articolo + Discussion.
4. **Ownare** WS-4 (Spring Boot starter) e WS-8 (contenuti) — allineati al tuo profilo ops/Java.

---

## Appendice A — Verifica endpoint (2026-08-31)

| URL | HTTP | Nota |
|---|---|---|
| https://glowroot.org/ | 200 | Contenuto obsoleto (0.14.0) |
| https://demo.glowroot.org/ | **522** | Origin timeout Cloudflare |
| https://github.com/glowroot/glowroot | 200 | Attivo |
| Maven `org.glowroot:glowroot-agent` | — | Latest: 0.14.0-beta.3 (2022) |

---

## Appendice B — CI (`.github/workflows/reusable-build.yml`)

Matrix completa:

- Java: 8, 17, 21, 25
- testGroup: 1, 2, 3, 4
- testShaded: true, false
- glowrootHarness: javaagent, local

Job disabilitati in `build.yml`: Checker Framework, SauceLabs cross-browser.

Ultimi run (2026-08-30): `main` → success.

---

## Appendice C — Plugin agent (27)

`camel`, `cassandra`, `ejb`, `elasticsearch`, `executor`, `grails`, `hibernate`, `http-client`, `jakarta-servlet`, `java-http-server`, `jaxrs`, `jaxws`, `jdbc`, `jms`, `jsf`, `jsp`, `kafka`, `logger`, `mail`, `mongodb`, `netty`, `play`, `quartz`, `redis`, `servlet`, `spring`, `struts`

---

## Appendice D — Release stable timeline

| Versione | Data stable |
|---|---|
| 0.14.7 | 2026-05-11 |
| 0.14.6 | 2026-03-24 |
| 0.14.5 | 2026-03-08 |
| 0.14.4 | 2025-05-25 |
| 0.14.2 | 2024-04-07 |

**Gap 0.14.2 → 0.14.4:** 13 mesi senza stable. Pattern che alimenta percezione “beta eterna”.

---

## Appendice E — File e branch fork rilevanti

| Branch fork | Contenuto |
|---|---|
| `docs/readme-product-first` | README modernizzato, star history, FAQ |
| `docs/1197-issue-knowledge-map` | Mappa knowledge issue |
| `feature/1206-adhoc-report-breakdown-timers` | Feature report |
| `fix/1125-virtual-thread-profiling` | Virtual threads ( anche PR #1191 upstream ) |

---

*Report generato per lavoro offline. Aggiornare i numeri GitHub con `gh api repos/glowroot/glowroot` prima di presentarlo esternamente.*
