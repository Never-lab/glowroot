# Glowroot wiki coverage report — what’s covered vs what’s missing

**Generated:** 2026-09-04  
**Wiki:** https://github.com/glowroot/glowroot/wiki  
**Baseline for “since June”:** 2026-06-01 → now (activity really started **2026-07-31**; June had 0 wiki commits)

Sources: live `glowroot.wiki` git history, `Home` / `_Sidebar`, UI routes (`ui/app/scripts/routes.js`, admin/config nav), product surfaces in-repo (`SECURITY.md`, JVM views, integrations).

---

## 1. Snapshot

| | ~1 Jun 2026 | Now |
|--|--|--|
| Markdown pages | **15** | **41** |
| Approx. size | ~42 KB | ~112 KB |
| Commits (Jun→Sep) | — | **80** (all Nicholas Antinori) |
| Shape | Install + a few ops pages + release bots | Full **ops / UI / limits / contributor** tree |

**Verdict:** Wiki went from “bare install docs” to a usable **operator handbook**. Coverage is strong on *how to run Glowroot* and *what not to expect*; still thin or missing on *admin integrations*, *JVM tools*, and *new product surfaces*.

---

## 2. What is already well covered

### Deploy & ops (strong)
| Topic | Page(s) | Notes |
|--|--|--|
| Embedded vs Central | [[Choosing Embedded vs Central]] | Clear decision table |
| Agent install (both modes) | Agent Installation (Embedded / Central) | Substantial |
| Central install / Docker / cluster / upgrade | Central Collector * | Cluster page is deep |
| Cassandra client | [[Cassandra Client Configuration]] | Present |
| Kubernetes patterns | [[Kubernetes]] | Explicit “no official Helm” |
| Multi-JVM / `agent.id` | [[Multiple JVMs and agent.id]] | Present |
| Storage (H2 + Cassandra TTL) | [[Administration-Storage]] | One of the richest pages (~7.3 KB) |
| Web bind / session basics | [[Administration-Web]] | Present |
| TLS / cert tips | [[Private key and X.509…]] | Present |
| Troubleshooting playbook | [[Troubleshooting Tips]] | Agent↔Central, downloads, UI bind, etc. |

### Product expectations (strong — high leverage)
| Topic | Page(s) |
|--|--|
| Non-features | [[What Glowroot does not do]] |
| Plugin gaps | [[Plugin coverage gaps]] |
| Plugins inventory + config | [[Plugins]] |
| Custom instrumentation | [[Instrumentation]], [[Batch application instrumentation]] |
| HTTP JSON / export limits | [[HTTP API and export]] |

### UI / config (good skeleton)
| Topic | Page(s) | Depth |
|--|--|--|
| Transaction tabs | [[Transaction tabs]] | Medium |
| Transaction config | [[Transaction configuration]] | Medium (profiling/thread stats only brief) |
| Alerts / incidents | [[Alerts and incidents]] | Medium (SMTP mentioned; channels incomplete) |
| Agents / rollups | [[Agents and rollups]] | Medium |
| Gauges / JMX | [[Gauges and JMX]] | Medium |
| Errors tab | [[Errors tab]] | Medium |
| UI Defaults | [[UI Defaults]] | **Thin** |
| Advanced / config.json | [[Advanced and config.json]] | Medium |
| Synthetic monitors | [[Synthetic monitors]] | **Thin** |
| Ad-hoc reports | [[Ad-hoc reports]] | **Thin** |
| Users / roles | [[Users and Roles]] | Medium |

### Contributors (good entry)
[[For contributors]], [[Agent data path]], [[Embedded vs Central in code]], [[Plugin and Pointcut basics]]

---

## 3. Thin pages (exist but need expansion)

These are on Home/Sidebar but under ~1.5 KB — easy next edits:

| Page | Why expand |
|--|--|
| [[UI Defaults]] | Default tx type / percentiles / gauges — common “UI feels empty” confusion |
| [[Synthetic monitors]] | Central-only; ping vs Java monitors; auth/proxy/SSL options |
| [[Ad-hoc reports]] | What dimensions exist; Central vs embedded differences |
| [[Agent Upgrade]] | Breaking changes, JDK requirements, properties migrations |
| [[Central Collector with Docker]] | Compose examples, volumes, ports 4000/8181/9042 |

---

## 4. Missing vs product UI (gap map)

Mapped from **Configuration**, **Administration**, **JVM**, **Incidents/Reports** nav.

### Administration — missing or only one-liners

| UI area | Wiki status | Priority |
|--|--|--|
| **SMTP** | Only a short paragraph under Alerts | **P0** — dedicated page |
| **PagerDuty** | Name-drop only | **P0** |
| **Slack** webhooks | **Missing** | **P0** |
| **HTTP proxy** (admin outbound) | Almost missing (agent proxy explicitly “none”) | **P1** |
| **LDAP** | **Missing** | **P1** |
| **Healthchecks.io** | **Missing** | **P2** |
| **admin.json** | Not documented as page (Advanced covers agent `config.json`) | **P2** |
| Users / Roles | Present | OK |
| Web / Storage | Present | OK |

### Configuration — incomplete

| UI area | Wiki status | Priority |
|--|--|--|
| General (agent display name, etc.) | **Missing** dedicated page | **P2** |
| JVM config (masking / thread dumps permissions?) | **Missing** | **P2** |
| Plugin **per-plugin** deep dives | Inventory in [[Plugins]]; few recipes | **P2** (Servlet session attrs, JDBC leaks) |
| Instrumentation recipes | Basics exist; few end-to-end examples | **P2** |

### JVM tools (almost uncovered)

UI has full **JVM** section; wiki barely mentions tools beyond gauges:

| UI | Wiki | Priority |
|--|--|--|
| Gauge charts | Covered in [[Gauges and JMX]] | OK |
| Thread dump | **Missing** | **P1** |
| jstack | **Missing** | **P2** |
| Heap dump / histogram | **Missing** | **P1** |
| Force GC | **Missing** | **P3** |
| MBean tree | Brief in Gauges page | OK-ish |
| System properties | **Missing** | **P3** |
| Environment | **Missing** | **P3** |
| Capabilities | **Missing** (explains N/A thread stats) | **P1** |

### Transaction / trace UI gaps

| Topic | Wiki | Priority |
|--|--|--|
| Flame graphs | **Missing** | **P2** |
| Profile tab deep dive | Only short in Transaction tabs | **P2** |
| **Allocated memory (cumulative)** thread stats | **Missing** (handoff #630) | **P1** — agreed design |
| Queries / Service Calls semantics | Partially in non-features + gaps | OK |
| Incidents list UI (vs alert config) | Thin inside Alerts page | **P2** |

---

## 5. Missing vs recent product / revival work

Topics that exist in repo / issues / PR stream but **not** as wiki pages:

| Topic | Why it matters | Priority |
|--|--|--|
| **SECURITY.md / reporting** | Now in upstream `SECURITY.md`; wiki silent | **P0** |
| **Official Helm chart** (if/when merged) | Wiki still says “no official chart” — keep accurate | **P0** when chart lands |
| **Spring Boot starter** | Revival work; only sparse mentions | **P1** |
| **Local Docker demo / facsimile** (`-Dglowroot.demo`) | Onboarding for contributors/testers | **P2** |
| **UiSandbox** dual ports (4000/4001) | Dev workflow in CLAUDE.md only | **P2** (contributor section) |
| **Java 21 / 25** support matrix | Troubleshooting mentions class-file errors; no matrix page | **P1** |
| Virtual threads / profiling caveats | Open issues area; wiki silent | **P2** |
| Issue triage policy (obsolete → close, retest) | Process used in revival; not documented | **P2** |
| glowroot-ops / AI assistant pack | Skills in repo; optional wiki pointer | **P3** |

---

## 6. Suggested coverage backlog (ordered)

### P0 — close support loops fast
1. **Administration → Notifications** page: SMTP + test mail, PagerDuty keys, Slack webhooks, what “one mail on open / one on resolve” means (ties to closed #586).
2. **Security reporting** page → link `SECURITY.md` + supported versions posture.
3. Keep [[Kubernetes]] / Home honest when **official Helm** ships (or stays unofficial).

### P1 — match what users open the UI for
4. **JVM tools** short page (thread dump, heap dump/histogram, capabilities, when thread CPU/alloc is N/A).
5. **Transaction thread stats / Allocated memory (cumulative)** — section under [[Transaction tabs]] + UI wiki footers (#630 handoff).
6. **LDAP + HTTP proxy** admin pages (or one “Admin integrations” page).
7. **JDK support matrix** (8 / 11 / 17 / 21 / 25 agent vs central).
8. Expand thin: Synthetic, Ad-hoc reports, Agent Upgrade, Docker Central.

### P2 — deepen existing skeleton
9. Flame graph + Profile interpretation.
10. Plugin recipes (Servlet `captureSessionAttributes`, JDBC leak, Executor detach).
11. Spring Boot starter + demo Docker.
12. Incidents UI walkthrough; alert scheduling explicitly “not planned” → link #708 if kept open.
13. Contributor: UiSandbox / dual UI ports.

### P3 — nice to have
14. Environment / system properties / Force GC.
15. admin.json mirror of Advanced.
16. Healthchecks.io.
17. Process page: how we triage obsolete issues.

---

## 7. Coverage scorecard (subjective)

| Area | Score | Comment |
|--|--|--|
| Install / Central / K8s | **8/10** | Best area |
| Storage / Web | **8/10** | Storage is flagship |
| Limits / non-features / plugins | **8/10** | High value for issue deflection |
| Transaction UI | **6/10** | Tabs exist; flame/alloc/profile thin |
| Alerts / notifications | **4/10** | Config yes; SMTP/Slack/PD weak |
| JVM toolbox | **2/10** | Biggest UI↔wiki mismatch |
| Security / compliance | **2/10** | SECURITY.md not linked |
| Identity (LDAP/SSO) | **2/10** | LDAP missing; no OIDC story |
| New revival features | **3/10** | Helm/starter/demo lag wiki |

**Overall handbook:** ~**6.5/10** for operators starting out; ~**4/10** for “I clicked every Admin/JVM menu item.”

---

## 8. What we talk about today (theme)

Current wiki voice is:

1. **How do I run it?** (Embedded/Central/K8s/Cassandra)
2. **Why is this tab empty?** (plugins, non-features, gaps)
3. **How do I not lose data / blow disk?** (Storage)
4. **How do I change the code?** (contributor four-pager)

Still under-told:

5. **How do I get paged?** (SMTP/Slack/PD)
6. **What do these JVM buttons do?**
7. **Is this secure / how do I report vulns?**
8. **What does Allocated memory mean?** (#630)

That matches the metrics story: revival invested in **docs that stop wrong issues** (expected behavior) more than **docs for every UI screen**.

---

## 9. Related artifacts

- Issue health (365d): `docs/report-glowroot-issues-365d.md`
- Allocated-memory handoff: `docs/handoff-630-allocated-memory.md`
- Live wiki Home: https://github.com/glowroot/glowroot/wiki

---

## 10. Bottom line

**Covered well:** install, Central/Cassandra/K8s patterns, storage, troubleshooting, “what Glowroot doesn’t do,” plugins/gaps, contributor on-ramp.

**Still missing (highest impact):** notification admin (SMTP/PagerDuty/Slack), security reporting page, JVM tools page, allocated-memory explanation, LDAP/proxy, JDK matrix, expand thin UI pages (Synthetic / Ad-hoc / Upgrade / Docker).

Next wiki sprint that pays off fastest: **P0 notifications + security**, then **P1 JVM tools + #630 allocated memory section**.
