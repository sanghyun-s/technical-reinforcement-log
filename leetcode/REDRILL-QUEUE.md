# Re-drill Queue — the prior 100

This is the **dynamic** half of the re-drill system. The rich five-layer writeups live in
[`leetcode-study`](https://github.com/sanghyun-s/leetcode-study) and never change — they're the
answer key. This file changes every time I drill, because it tracks the only thing that moves:
**can I still reproduce this cold, and when should I check again.**

The number that matters here is not "100 solved last spring." It's **how many I can still
solve cold this summer** — and the gap between those two is the honest retention signal.

---

## The interval rule

Re-solve **from memory first**, then open the old writeup to grade. Grade → next due date:

| Grade | Meaning | Next due |
|---|---|---|
| **Strong** | Reproduced clean, no peek | advance ladder: 7d → 21d → 45d → **retire** |
| **Shaky** | Got it, but slow / peeked / minor bug | hold ladder, redrill in **4d** |
| **Failed** | Couldn't reproduce cold | reset to 7d, redrill in **2d**, + write what slipped |
| **Ported only** | Reference brought over, never re-solved cold | **due now (owed)** |

Interview value: "I can show you spaced-repetition retention data across my prior set" is a
claim almost no candidate can make. This file *is* that claim.

---

## Due now / overdue — the daily driver

Work this list top-down. Owed = ported reference exists but never re-solved cold.

| # | Problem | Pattern | State | Old writeup |
|---|---|---|---|---|
| 0014 | Longest Common Prefix | String | Ported — cold re-drill owed | [link](https://github.com/sanghyun-s/leetcode-study) |

---

## Scheduled — retained, not yet due

| Next due | # | Problem | Pattern | Last grade | Interval |
|---|---|---|---|---|---|
| 2026-07-28 | 0001 | Two Sum | Hash map | Strong | 7d (step 1) |

---

## Attempt history — append-only, never edited

The changing record. Every drill adds a row; nothing is overwritten. This is where the trend
lives.

| Date | # | Problem | Mode | Grade | What slipped / note |
|---|---|---|---|---|---|
| 2026-07-21 | 0001 | Two Sum | cold | Strong | Both brute-force and hash-map from memory; check-before-store ordering correct |
| 2026-07-21 | 0014 | Longest Common Prefix | port | — | Reference ported for comparison; cold solve still owed. Idiom flags noted: prefer `startswith` over `find(...)==0`, `prefix[:-1]` over `prefix[0:len-1]`, `if not strs` over `len(strs)==0` |

---

## Retired — reproduced strong through the full ladder

Nothing yet. A problem lands here after a Strong grade at the 45d step. Retired ≠ forgotten;
it means proven durable enough to stop scheduling.

| # | Problem | Pattern | Retired on |
|---|---|---|---|
| — | — | — | — |

---

## Rollup (for PROGRESS.md)

| Metric | Value |
|---|---|
| Tracked | 2 / 100 |
| Passed cold (Strong or Shaky) | 1 |
| Ported-only, owed | 1 |
| Retired | 0 |
