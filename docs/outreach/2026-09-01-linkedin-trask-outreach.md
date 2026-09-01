# Outreach Glowroot — LinkedIn, post release, mail a Trask

| Campo | Valore |
|---|---|
| Data | 2026-09-01 |
| Autore | Nicholas Antinori (`Never-lab`) |
| Contesto | Note da sessione Cursor — revival, visibilità, accesso `glowroot.org` |
| Branch | `docs/revival-audit-2026-08-31` |

---

## 1. Strategia LinkedIn

Glowroot non compete con Datadog/New Relic: è più vicino a **Pinpoint**, **Apache SkyWalking** (lato Java agent), **Scouter** — tool **leggeri, open source, Java-first**.

### Sequenza consigliata (4 settimane)

| Settimana | Azione |
|-----------|--------|
| 1–2 | Follow pagine/persone sotto; commenta 2–3 post/settimana su APM/Java |
| 3 | Post tecnico sul profilo: screenshot UI, confronto overhead, o “come ho attivato Glowroot in 5 min” |
| Release day | Post release IT + EN |
| +2–3 giorni | Rispondi a ogni commento; valuta GitHub Discussions / Show HN |

### Regole

1. **Seguire e interagire** 2–4 settimane prima del post release.
2. **Non spammare** i gruppi: molti vietano autopromozione.
3. **Posizionare Glowroot** come alternativa semplice a stack pesanti, non come “il nuovo Datadog”.

### Checklist pre-post

- [ ] `glowroot.org` aggiornato alla nuova versione
- [ ] Demo funzionante (o istruzioni Docker chiare)
- [ ] Release notes su GitHub
- [ ] README con quick start in 5 minuti

---

## 2. Pagine company da seguire

| Pagina | Perché |
|--------|--------|
| [SigNoz](https://www.linkedin.com/company/signozio) | OSS observability, post frequenti |
| [The Apache Software Foundation](https://www.linkedin.com/company/the-apache-software-foundation) | SkyWalking e altri OSS |
| [CNCF](https://www.linkedin.com/company/cloud-native-computing-foundation) | OpenTelemetry, Jaeger |
| [OpenTelemetry](https://www.linkedin.com/company/opentelemetry) | Standard observability |
| [Pinpoint APM](https://www.linkedin.com/company/pinpoint-apm) | APM Java OSS competitor |
| [Grafana Labs](https://www.linkedin.com/company/grafana-labs) | Metriche/traces, audience DevOps |
| [Honeycomb](https://www.linkedin.com/company/honeycomb) | Observability moderna |
| [Chronosphere](https://www.linkedin.com/company/chronosphere) | Metriche/tracing enterprise |
| [Last9](https://www.linkedin.com/company/last9) | Blog/post su OTel e APM |
| [GrUSP](https://www.linkedin.com/company/grusp) | Community tech italiana (DevOps, backend) |

Cerca su LinkedIn anche: `Apache SkyWalking`, `Jaeger`, `Micrometer`, `Java at Microsoft`.

---

## 3. Persone da seguire

| Persona | Profilo | Perché |
|---------|---------|--------|
| Trask Stalnaker | [linkedin.com/in/traskstalnaker](https://www.linkedin.com/in/traskstalnaker) | Autore Glowroot, OTel Java |
| Pranay Prateek | [linkedin.com/in/pranay01](https://www.linkedin.com/in/pranay01) | Co-founder SigNoz |
| Yuri Shkuro | [linkedin.com/in/yurishkuro](https://www.linkedin.com/in/yurishkuro) | Creatore Jaeger, co-founder OTel |
| Woonduk Kang | [linkedin.com/in/woonduk-kang-664157116](https://www.linkedin.com/in/woonduk-kang-664157116) | Founder Pinpoint APM |
| Eric Huang | [linkedin.com/in/butterbright](https://www.linkedin.com/in/butterbright) | Committer Apache SkyWalking |
| Severin Neumann | [linkedin.com/in/severinneumann](https://www.linkedin.com/in/severinneumann) | OTel Governance |
| Austin Parker | [linkedin.com/in/austinlparker](https://www.linkedin.com/in/austinlparker) | OTel community, Observability Day |
| Saurabh Kumar | cerca `sksingh1983` su LinkedIn | Post comparativi OSS APM |

**Italia:** speaker/organizer [Incontro DevOps Italia](https://www.incontrodevops.it/) (GrUSP); profili che postano `#devops` `#java` `#sre` in italiano.

---

## 4. Gruppi LinkedIn

| Gruppo | Note |
|--------|------|
| **Java Developers Group** (~200k) | [javapgmr.net](http://javapgmr.net/) — anti-spam, solo valore |
| **Full Stack Java Developer Community** | [linkedin.com/groups/9062940](https://www.linkedin.com/groups/9062940/) |
| **Java Developer Zone** | [linkedin.com/company/java-developer-zone](https://www.linkedin.com/company/java-developer-zone) ~64k follower |
| Cerca: `Performance Engineering`, `SRE`, `DevOps`, `Microservices` | Leggi regole prima di postare |

**Italia (non LinkedIn, stesso pubblico):** [DevOps Italia Telegram](https://t.me/devopsitalia), [CloudOpsItalia](https://github.com/cloudopsitalia).

### Hashtag

`#Java` `#APM` `#Observability` `#OpenSource` `#DevOps` `#SRE` `#Performance` `#JVM` `#Microservices` `#Glowroot`

In italiano: `#SviluppoSoftware` `#Backend` `#JavaItalia`

---

## 5. Bozze post release

Sostituire `[VERSIONE]`, `[LINK_RELEASE]`, `[LINK_GITHUB]`, `[LINK_DEMO]`.

### Italiano

```
Sto lavorando al revival di Glowroot — un APM open source per Java che forse non conosci ancora.

Cosa lo rende diverso dagli stack enterprise pesanti?
→ si attiva con un semplice -javaagent
→ overhead molto basso in produzione
→ UI integrata per trace, query lente e metriche JVM
→ modalità embedded (tutto in un JAR) o central collector per più agent

Dopo un periodo di poca visibilità, la community sta riportando il progetto in vita: supporto Java recente, CI verde, release [VERSIONE] in arrivo.

Se gestisci applicazioni Java in produzione e cerchi qualcosa di leggero senza vendor lock-in, vale la pena dare un'occhiata.

🔗 Release: [LINK_RELEASE]
🔗 GitHub: [LINK_GITHUB]

Conoscevate già Glowroot? Lo usate in produzione o preferite altri tool (Pinpoint, SkyWalking, OTel)? Mi interessa il vostro feedback.

#Java #APM #OpenSource #Observability #DevOps #JVM #Glowroot
```

### Inglese

```
Glowroot is a lightweight, open-source Java APM that deserves more attention than it gets.

If you've ever needed production visibility without spinning up a full observability stack, this might be what you're looking for:

→ Attach with -javaagent — no code changes
→ Very low runtime overhead
→ Built-in UI for slow traces, SQL queries, and JVM metrics
→ Embedded mode (single JAR + H2) or Central collector for multiple agents

The project has been quietly maintained for years. A community revival is now underway — recent Java versions supported, CI green, and release [VERSIONE] coming soon.

Worth a look if you run Java in production and want something simple, self-hosted, and Apache 2.0 licensed.

🔗 Release: [LINK_RELEASE]
🔗 GitHub: [LINK_GITHUB]

Have you used Glowroot? What do you use for Java APM today — Pinpoint, SkyWalking, OpenTelemetry, or something else? Curious to hear real-world experiences.

#Java #APM #OpenSource #Observability #DevOps #SRE #JVM #Glowroot
```

### Variante corta storytelling

**IT:**
```
Oggi molti team Java scelgono tra Datadog (costoso) e OpenTelemetry (potente ma da assemblare).

Nel mezzo c'è Glowroot: APM open source, un JAR, pochi minuti per avere trace e metriche. Lo sto aiutando a tornare visibile — release [VERSIONE] a breve.

Se vi interessa, link in commento 👇
```

**EN:**
```
Most Java teams today pick between expensive SaaS APM and DIY OpenTelemetry stacks.

Glowroot sits in the middle: open-source, single JAR, traces and JVM metrics in minutes. I'm helping revive the project — [VERSIONE] release coming soon.

Link in comments if you want to try it 👇
```

---

## 6. Mail a Trask — contesto thread esistente

### Scambio precedente (2026-08-30 / 31)

**Nicholas → Trask (dom 30 ago):** richiesta Triage/Write su `glowroot/glowroot`; accordo con `@nowheresly` in discussion #1195; scope limitato a label, close support, merge docs/bugfix.

**Nicholas → Trask (lun 31 ago, follow-up):** 3 bugfix merged, 7 PR in attesa; es. #1190 bloccata.

**Trask → Sylvere + Nicholas (lun 31 ago, 19:57):**
> Thanks for the follow-up, sorry I missed your earlier email.
> I just gave @nowheresly long-overdue admin permission to the repo, so he should be able to do this now.

**Nicholas → Trask + Sylvere (lun 31 ago, 20:05):** ringraziamento; coordino Triage/Write con Sylvere.

### Nuova richiesta: `glowroot.org` / `glowroot-site`

**Problema attuale (2026-09-01):**

| Asset | Stato |
|-------|--------|
| `glowroot.org` | Mostra ancora `glowroot-0.14.0-dist.zip` (Releases a v0.14.7) |
| `demo.glowroot.org` | HTTP 522 — [#1102](https://github.com/glowroot/glowroot/issues/1102) |
| [glowroot/glowroot-site](https://github.com/glowroot/glowroot-site) | Ultimo push settembre 2024 |

**Repo sito:** https://github.com/glowroot/glowroot-site

**PR correlate sul main repo:** #1225 (Docker demo try-before-download), #1190 (README).

---

## 7. Mail da inviare (reply al thread esistente)

**Oggetto:** `glowroot.org / glowroot-site — admin access?`

**Inviare in reply** al thread con Sylvere in CC.

```
Hi Trask,

Following up on our thread from Monday — thanks again for unblocking Sylvere on the main repo. I'm coordinating Triage/Write with him on that side.

Separate ask, if you have a minute: glowroot.org still shows glowroot-0.14.0-dist.zip, while Releases is at v0.14.7 (0.14.8 betas in progress). demo.glowroot.org has been returning 522 for a while (#1102). The glowroot-site repo (https://github.com/glowroot/glowroot-site) hasn't been updated since Sept 2024.

I'm not asking to change direction — same site, same messaging. Just bring the public face in line with what's actually shipping: correct download links, pointer to GitHub Releases, and ideally get a working demo back (I have a Docker-based try-before-download in PR #1225 if that helps).

Would you be able to grant me admin (or maintain) on glowroot-site, plus whatever hosting/DNS access is needed for glowroot.org and demo.glowroot.org? Happy to work via PRs so you can review before anything goes live. Scope stays limited to site/demo upkeep only.

If that's easier to route through Sylvere now that he has admin on the main repo, just point me there — otherwise I'd appreciate whatever access you can spare when you have time.

Thanks,
Nicholas
GitHub: Never-lab
```

### Versione corta (alternativa)

```
Hi Trask,

Quick separate ask from the permissions thread: glowroot.org is still on 0.14.0 and demo.glowroot.org is down (522, #1102). glowroot-site hasn't been touched since late 2024.

Could I get admin on https://github.com/glowroot/glowroot-site (and hosting/DNS for glowroot.org if that's separate)? I'd only update download links, point to current Releases, and try to restore the demo — PRs for review, no branding or direction changes.

Happy to coordinate with Sylvere if that's the right path.

Thanks,
Nicholas
```

### Cosa chiedere esattamente

1. GitHub: ruolo **Admin** o **Maintain** su `glowroot/glowroot-site`
2. Hosting: accesso **Cloudflare** (sito + demo), o credenziali deploy attuale

### Follow-up

Se nessuna risposta in 10–14 giorni: breve bump su LinkedIn o reply al thread (“just bumping this — no rush”).

---

## 8. Bozza mail iniziale (archivio — tono più lungo, non inviata)

Prima bozza più estesa, sostituita dalla versione allineata al tono del thread con Trask:

```
Hi Trask,

I hope this finds you well. My name is Nicholas Antinori — I'm contributing to Glowroot under the GitHub account Never-lab.

[... richiesta admin glowroot-site, aggiornamento 0.14.0 → current release, demo 522, scope limitato ...]

Best regards,
Nicholas Antinori
```

Usare la versione in §7 per l'invio effettivo.
