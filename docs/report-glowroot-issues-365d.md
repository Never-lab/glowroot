# Glowroot issue health report — now vs 365 days ago

**Repo:** [glowroot/glowroot](https://github.com/glowroot/glowroot)  
**Generated:** 2026-09-04 12:00 UTC  
**Reference (T0):** 2025-09-04 (365 days before generation date)  
**Previous window:** 2024-09-04 → 2025-09-04

Sources: GitHub Search/REST API (`gh`), OpenDigger static metrics (backdrop).

---

## Executive summary

| Signal | 365d ago (T0) | Now | Change |
|--------|---------------|-----|--------|
| Open issues | **281** | **75** | **-206** (-73.3%) |
| Closed / total (stock) | 69.9% | 92.3% | — |
| Issues created (365d window) | 33 | 42 | +9 |
| Issues closed (365d window) | 3 | 248 | +245 |
| PRs merged (365d) | 2 | 6 | +4 |
| Commits (365d) | 34 | 185 | +151 |

**Reading:** Over the last year Glowroot shows heavy **backlog closure** (closed ≫ created in the last 365d), with open stock moving from **281 → 75**. That is consistent with revival/triage, not only new inflow.

---

## 1. Stock: open / closed at two points in time

| | At T0 (2025-09-04) | Now (2026-09-04) |
|--|--|--|
| Open issues | 281 | 75 |
| Closed issues (existed by then) | 654 | 902 |
| Total issues (existed by then) | 935 | 977 |
| % closed | 69.9% | 92.3% |

**Method (open at T0):**  
`is:open created:<=T0` **+** `is:closed created:<=T0 closed:>T0`  
(= issues that existed and were still open on T0).

GitHub `open_issues_count` on the repo object is **88** (includes open PRs) — not used as the issue-only open figure.

---

## 2. Flow: created vs closed by window

| Window | Created | Closed | Created − Closed |
|--------|---------|--------|------------------|
| Last 365d (2025-09-04 → 2026-09-04) | 42 | 248 | -206 |
| Previous 365d (2024-09-04 → 2025-09-04) | 33 | 3 | 30 |

Negative net in the last year means **more issues were closed than opened** (backlog burn-down and/or mass triage).

---

## 3. Time to close (days)

Sample = issues with `closed:` in each window (GitHub Search, up to ~1000 cap).

| | Last 365d closes | Prev 365d closes |
|--|--|--|
| Sample size | 248 | 3 |
| Median days open → close | 1838.6d | 14.0d |
| Mean | 1644.8d | 83.0d |
| ~p90 | 2545.3d | 14.0d |

**Caveat:** Closing ancient issues during revival **inflates** median/mean time-to-close for the last 365d. Prefer the flow table + open-stock change for “health now”.

---

## 4. Age of currently open issues

| | Open now (sample n=75) |
|--|--|
| Median age | 1025.9d |
| Mean age | 1318.9d |
| ~p90 age | 2747.1d |

---

## 5. First response time (hours)

First **non-author** comment (bots skipped). Sample up to 35 issues **created** in each window; issues with zero replies are excluded from the distribution.

| | Created last 365d | Created prev 365d |
|--|--|--|
| Sample with a reply | 15 | 32 |
| Median hours to first reply | 0.9d (21.6 h) | 346.3d (8311.0 h) |
| Mean hours | 364.8 | 7995.1 |
| ~p90 hours | 771.0 | 14674.9 |

---

## 6. Pull requests & commits (context)

| | Prev 365d | Last 365d |
|--|--|--|
| PRs created | 6 | 38 |
| PRs merged | 2 | 6 |
| Commits | 34 | 185 |
| Open PRs now | 13 | — |

Repo metadata now: **1356** stars, **337** forks, last push **2026-09-03T20:06:42Z**.

---

## 7. Labels on open issues (now)

- `enhancement`: 35
- `bug`: 19
- `awaiting feedback`: 10
- `plugin`: 10
- `ui`: 5
- `discussion`: 4
- `wishlist`: 2
- `regression`: 1

---

## 8. OpenDigger backdrop (calendar years; series lags)

| Metric | 2023 | 2024 |
|--------|------|------|
| issues_new | 57 | 48 |
| issues_closed | 28 | 18 |
| issue_age avg (days) | 173 | None |
| OpenRank | 45.15 | 29.59 |

OpenDigger files: `https://oss.x-lab.info/open_digger/github/glowroot/glowroot/{metric}.json`

---

## 9. Limitations

1. GitHub Search returns max **1000** hits per query — timing samples may be truncated on busy windows.
2. “Open at T0” is reconstructed from search, not a live snapshot archive.
3. First-response ignores maintainers who only use review/PR or emoji; counts comments only.
4. Mass closure of old issues makes **time-to-close** look worse even when ops health improves.
5. OpenDigger yearly data is not a substitute for the T0/now GitHub comparison above.

---

## 10. Bottom line

- **Open stock:** 281 → 75 (-206).
- **Last year flow:** 42 created vs 248 closed (prev year: 33 / 3).
- **Development signal:** commits 34 → 185; merged PRs 2 → 6.
- For “are we responsive?”, trust **first-response sample** and **open age** more than raw time-to-close during a triage spike.
