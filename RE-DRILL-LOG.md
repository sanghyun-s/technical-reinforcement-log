# Re-Drill Log

Repetition is the evidence. This file tracks **repeated implementation** of prior problems and
patterns — the mentor's method made concrete: *understand it once (with help if needed), then
rebuild it the next day with less help.*

The goal is not to inflate a problem count. It's to prove I can **return to a pattern and
rebuild it.** Detailed writeups live in [`leetcode/archive/`](./leetcode/archive/); this file
is the visible record of coming back to them.

---

## The method (5 steps)

1. **First pass** — understand structure + code. AI, answer, hints, or a course are all fine.
2. **Next-day recall** — rebuild the same problem (or its pattern) without looking. Imperfect
   is fine; *where I got stuck* is the useful data.
3. **Pattern re-drill** — pull a same-pattern problem from the 98-archive and redo it.
4. **Explanation** — write how I'd explain the pattern, not just the code.
5. **Interview conversion** — compress into one defensible sentence → INTERVIEW-SENTENCES.md.

---

## Spacing (loose, momentum-first)

Not a rigid algorithm — a nudge. After a recall attempt, grade it and schedule the next touch:

| Recall grade | Meaning | Next touch |
|---|---|---|
| **Strong** | Rebuilt clean, no peek | space out: ~1w → 3w → retire |
| **Shaky** | Got it, slow or peeked | ~3–4 days |
| **Stuck** | Couldn't rebuild | ~2 days, and note what blocked me |

Miss a date? No penalty. The queue is a suggestion; consistency beats precision.

---

## Active log

| Date | Problem / Pattern | Source | Mode | Result | Next step |
|---|---|---|---|---|---|
| 2026-07-21 | LC 0001 Two Sum | Prior archive | Pattern Re-Drill | **Strong** — hash-map + brute force from memory, ordering correct | Retire soon; try an unseen hash-map problem instead |
| 2026-07-21 | LC 0014 Longest Common Prefix | Prior archive | Reference Recall | Ported for comparison; not yet rebuilt cold | Next-day recall owed |
| 2026-07-19 | LC 3925 Concatenate | New (this repo) | First-Pass Assisted | Understood; 3 variants written | Next-day recall: rebuild once, cleaner |
| 2026-07-19 | LC 1165 Single-Row Keyboard | New (this repo) | First-Pass Assisted | Understood; fixed set-vs-dict comprehension | Next-day recall owed |
| 2026-07-24 | LC 360 Sort Transformed Array | New (this repo) | First-Pass Assisted | Understood parabola + two-pointer | Next-day recall: unify the two branches, one loop |

---

## Pattern rotation from the archive

The 98-problem archive grouped by family (see [LEARNING-DASHBOARD](./LEARNING-DASHBOARD.md)).
Re-drill pulls from whichever family feels rustiest, not in order.

**Next up to re-touch:** Two Pointers · Trees (DFS/BFS) · SQL — the three largest families,
so the most worth keeping warm.
