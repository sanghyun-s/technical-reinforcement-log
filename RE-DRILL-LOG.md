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
| **Shaky** | Got it, slow or peeked / needed guidance | ~3–4 days |
| **Stuck** | Couldn't rebuild | ~2 days, and note what blocked me |

Miss a date? No penalty. The queue is a suggestion; consistency beats precision.

---

## Active log

| Date | Problem / Pattern | Source | Mode | Result | Next step |
|---|---|---|---|---|---|
| 2026-07-30 | LC 0226 Invert Binary Tree | Prior archive | Reference Recall | 4 traversals (recursion ×2, DFS-stack, BFS-queue); recursive core cold | Reproduce stack-DFS + queue-BFS unaided |
| 2026-07-30 | LC 0349 Intersection of Two Arrays | Prior archive | Reference Recall | 4 set approaches; reached for two-pointer, set-intersect is the answer | Next-day unaided pass |
| 2026-07-26 | LC 0938 Range Sum of BST | Prior archive | Reference Recall | Reconstructed with archive open — BST pruning clear | Next-day **unaided** pass (cold) |
| 2026-07-26 | LC 1038 Greater Sum Tree | Prior archive | Reference Recall | Rebuilt O(n²) collect-and-re-sum; led into 538's O(n) fix | Rebuild the O(n) reverse-inorder unaided |
| 2026-07-26 | LC 1874 Minimize Product Sum | Prior archive | Reference Recall | Greedy (rearrangement ineq.) + counting sort; both correct | Next-day unaided pass |
| 2026-07-26 | LC 2824 Count Pairs Sum < Target (≡ 1679) | Prior archive | Pattern Re-Drill | **Shaky** — both approaches, but guided | Next-day unaided pass |
| 2026-07-21 | LC 0001 Two Sum | Prior archive | Pattern Re-Drill | **Strong** — from memory, ordering correct | Retire soon; try an unseen hash-map problem |
| 2026-07-21 | LC 0014 Longest Common Prefix | Prior archive | Reference Recall | Ported for comparison; not rebuilt cold | Next-day recall owed |

---

## Pattern rotation from the archive

The 98-problem archive grouped by family (see [LEARNING-DASHBOARD](./LEARNING-DASHBOARD.md)).
Re-drill pulls from whichever family feels rustiest, not in order.

**Recently touched:** Two Pointers (2824) · Trees/BST (938, 1038→538) · Greedy (1874).
**Next up:** SQL — the largest family still cold.

---

## Recurring cross-problem lessons

Patterns worth more than any single solve — the reusable *decisions*:

- **"Bounded values → counting sort is available but fragile"** — met on 2974, 2824, 1874.
  Faster (O(n) vs O(n log n)) only because `nums[i] ≤ 100`; breaks on unbounded input.
- **"The thing that should move/shrink didn't"** — merge-sort pointer, quicksort recursion,
  538's `noe` pointer typo. A variable that must change each iteration silently doesn't.
- **"O(n²) → O(n) is usually: stop recomputing what you could carry"** — 538 reverse-inorder
  carries a running total vs 1038 re-summing per node.
