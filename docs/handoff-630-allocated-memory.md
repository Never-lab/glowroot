# Handoff — #630 JVM Thread Stats / Allocated memory

**Branch:** `docs/handoff-630-allocated-memory-wiki`  
**Issue:** https://github.com/glowroot/glowroot/issues/630  
**Status:** design agreed — **do not implement yet** (handoff only)  
**Date:** 2026-09-04

---

## Goal

Clarify what **Allocated memory** means in JVM Thread Stats (cumulative allocation, not heap residency), document it on the Glowroot wiki, and add small UI cues + wiki links (same idea as Administration → Storage → wiki footer).

---

## Issue triage (already done in conversation)

| Fact | Detail |
|------|--------|
| Type | Originally a **documentation / Q&A** question (2019), not a missing metric |
| Official answer (2020) | @trask confirmed @iherasymenko: value is **cumulative** bytes allocated during the transaction/trace, measured from the beginning of the trace — not a heap high-water mark |
| Why GB/TB confuse users | JVM `getThreadAllocatedBytes` counts bytes allocated over time; GC’d memory still counts toward the total, so the number can exceed heap size |
| Code today | `ThreadStatsComponent.getAllocatedBytesInternal()` = current − starting via `com.sun.management.ThreadMXBean.getThreadAllocatedBytes` |
| UI today | Label is only `Allocated memory:` — no “cumulative”, no help/wiki link (`trace.hbs`, `gt-thread-stats.html`) |
| Wiki today | [[Transaction configuration]] mentions optional thread stats briefly; **does not** explain Allocated memory. [[Transaction tabs]] has no thread-stats section |
| Never-lab 2026 comment | Framed as enhancement wishlist — **incorrect**; the metric already exists. Residual work is UX + wiki |

**Verdict from analysis:** closable as answered once wiki + UI land; until then keep open or leave this handoff branch for the follow-up PR.

---

## Agreed design (approved direction)

### 1. Wiki content

Add a section under existing wiki page **[Transaction tabs](https://github.com/glowroot/glowroot/wiki/Transaction-tabs)** (preferred over a brand-new page — less sprawl).

Suggested section title: **JVM Thread Stats / Allocated memory**

Content to cover:

1. **Allocated memory** = cumulative bytes allocated by the thread(s) for that transaction/trace since it started (delta of `ThreadMXBean.getThreadAllocatedBytes`).
2. It is **not** “how much heap is currently held” and **not** a peak resident heap size.
3. Numbers larger than the process heap (e.g. many GB or TB on long traces) can be **normal** when lots of objects are allocated and then GC’d.
4. On an **active** (still running) trace, the value is from trace start to “now”.
5. On aggregates (Average tab), values are totals / averages over the selected window (same cumulative meaning per transaction, then aggregated as today).
6. Optional: point to Configuration → Transactions → “Capture JVM thread stats”.
7. Cross-link from [[Transaction configuration]] “Thread stats” subsection to this section (one sentence + link).

Wiki Home: no new top-level entry required if the section lives under Transaction tabs (already linked from Home).

### 2. UI changes (small)

Same pattern as Storage wiki footer:

```text
For more information about JVM thread stats, see the Glowroot wiki.
→ https://github.com/glowroot/glowroot/wiki/Transaction-tabs
```

(Use an `#` anchor if the wiki section gets a stable heading slug.)

| Location | Change |
|----------|--------|
| Open trace — `ui/app/hbs/trace.hbs` | Rename label to `Allocated memory (cumulative)`; after main + aux JVM Thread Stats blocks, add a short wiki footer (Storage-style) |
| Average tab — `ui/app/template/gt-thread-stats.html` (used from `transaction/average.html`) | Same label + same wiki footer |

**Repetition is intentional:** show the link in **both** places so users who only open traces or only look at Average still find the wiki.

Reference for Storage footer pattern:

- `ui/app/views/admin/storage.html` (`.gt-storage-wiki-footer`)
- `ui/app/styles/config.scss` (`.gt-storage-wiki-footer`)

Reuse or lightly generalize the CSS class (e.g. keep a shared `.gt-wiki-footer` or duplicate the small Storage footer styles next to thread stats — prefer minimal diff).

Also consider a one-line tweak under Configuration → Transactions checkbox help (`ui/app/views/config/transaction.html`) — **optional / out of minimal scope** unless done in the same PR.

### 3. Out of scope

- Changing how allocated bytes are calculated
- New gauges / new metrics
- New wiki page unless Transaction tabs feels overcrowded at edit time
- Closing #630 before wiki + UI are actually done (wiki may need write access on `glowroot/glowroot` wiki)

---

## Implementation checklist (for later — not this branch)

- [ ] Edit GitHub wiki `Transaction-tabs.md` (section + optional link from `Transaction-configuration.md`)
- [ ] UI: label `(cumulative)` in `trace.hbs` + `gt-thread-stats.html`
- [ ] UI: wiki footer under thread stats on open trace and Average
- [ ] CSS: Storage-like footer styling (minimal)
- [ ] Manual check: open a slow/active trace with thread stats enabled; open Transactions → Average
- [ ] Comment on #630 with wiki URL + UI note; close as completed / answered
- [ ] Optional: update `skills/glowroot-ops/wiki-index.md` if that index lists Transaction tabs subsections

---

## Key file pointers

| Path | Role |
|------|------|
| `agent/core/.../ThreadStatsComponent.java` | Delta allocated bytes |
| `agent/core/.../ThreadAllocatedBytes.java` | MXBean wrapper |
| `ui/app/hbs/trace.hbs` | Open-trace JVM Thread Stats |
| `ui/app/template/gt-thread-stats.html` | Aggregate Average thread stats |
| `ui/app/views/transaction/average.html` | Hosts `gt-thread-stats` |
| `ui/app/views/admin/storage.html` | Precedent wiki footer |
| Wiki: `Transaction-tabs` | Target doc page |
| Wiki: `Transaction-configuration` | Cross-link “Thread stats” |

---

## Conversation decisions (short)

1. Prefer **wiki + UI link** over closing as “answered only”.
2. Prefer link under **open transaction (trace)** thread stats — where #630 confusion appears.
3. Also put the same link on **Average** thread stats — repetitive but visible.
4. Prefer documenting under **Transaction tabs** rather than a brand-new wiki page (unless content grows).
5. **This branch:** handoff only — **no development** yet.

---

## Suggested PR title (when implementing later)

`docs(ui): clarify cumulative Allocated memory + wiki link (#630)`
