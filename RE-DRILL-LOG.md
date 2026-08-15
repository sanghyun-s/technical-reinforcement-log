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
| 2026-08-15 | LC 1411 Ways to Paint N×3 Grid | Prior archive | Reference Recall | **Solid** on 2-state/full DP; matrix expo conceptual | A1/A2 unaided; A3 re-explain the recurrence→matrix idea |
| 2026-08-15 | LC 1255 Max Score Words | Prior archive | Reference Recall | **Solid** — bitmask ⇄ backtracking equivalence clear | Reproduce one paradigm unaided |
| 2026-08-15 | LC 3368 First Letter Capitalization (SQL) | Prior archive | Reference Recall | **Solid** — recursive CTE + numbers-table split; warms coldest family | Reproduce numbers-table version unaided |
| 2026-08-11 | LC 1265 Print Immutable LL in Reverse | Prior archive | Reference Recall | **Solid** on recursion/stack/brute; √n decomposition heavy | A1–3 unaided; A4 conceptual re-explain |
| 2026-08-11 | LC 1365 Smaller Numbers Than Current | Prior archive | Reference Recall | **Solid** — 3-tier ladder; bounded-values ⇒ counting sort is *correct* here | Quick unaided pass |
| 2026-08-11 | LC 0094 Binary Tree Inorder | Prior archive | Reference Recall | **Solid** — recursion + explicit-stack duality clean | Reproduce iterative cold |
| 2026-08-11 | LC 3945 Digit Frequency Score | Prior archive | Reference Recall | **Solid** — spotted the math collapse (score = digit sum) | Quick unaided pass |
| 2026-08-11 | LC 0088 Merge Sorted Array | Prior archive | Reference Recall | **Solid** — back-merge, O(m+n), free space at tail | Quick unaided pass |
| 2026-07-31 | LC 2545 Sort Students by Kth Score | Archive + research | Mixed | **Solid** — sort by key; mutate-vs-return + `itemgetter` idioms | Quick recall (value is the idioms) |
| 2026-07-31 | LC 2657 Prefix Common Array | Prior archive | Reference Recall | **Solid** — permutation-freq trick clicked cleanly | Quick unaided pass |
| 2026-07-31 | LC 3751 Total Waviness (Range I) | Archive + editorial | Reference Recall | **Near-stuck** — digit DP passed on 7th attempt; brute force is the right tool here | Conceptual: re-explain the 5 DP state pieces, don't retype |
| 2026-07-31 | LC 3831 Median of a BST Level | Prior archive | Reference Recall | **Shaky** — recursive DFS passed on 3rd attempt; BFS-no-sort insight solid | Rebuild the recursive version clean, unaided |
| 2026-07-30 | LC 0226 Invert Binary Tree | Prior archive | Reference Recall | 4 traversals (recursion ×2, DFS-stack, BFS-queue); recursive core cold | Reproduce stack-DFS + queue-BFS unaided |
| 2026-07-30 | LC 0349 Intersection of Two Arrays | Prior archive | Reference Recall | 4 set approaches; reached for two-pointer, set-intersect is the answer | Next-day unaided pass |
| 2026-07-26 | LC 0938 Range Sum of BST | Prior archive | Reference Recall | Reconstructed with archive open — BST pruning clear | Next-day **unaided** pass (cold) |
| 2026-07-26 | LC 1038 Greater Sum Tree | Prior archive | Reference Recall | Rebuilt O(n²) collect-and-re-sum; led into 538's O(n) fix | Rebuild the O(n) reverse-inorder unaided |
| 2026-07-26 | LC 1874 Minimize Product Sum | Prior archive | Reference Recall | Greedy (rearrangement ineq.) + counting sort; both correct | Next-day unaided pass |
| 2026-07-26 | LC 2824 Count Pairs Sum < Target (≡ 1679) | Prior archive | Pattern Re-Drill | **Shaky** — both approaches, but guided | Next-day unaided pass |
| 2026-07-21 | LC 0001 Two Sum | Prior archive | Pattern Re-Drill | **Strong** — from memory, ordering correct | Retire soon; try an unseen hash-map problem |
| 2026-07-21 | LC 0014 Longest Common Prefix | Prior archive | Reference Recall | Ported for comparison; not rebuilt cold | Next-day recall owed |

---

## Recurring cross-problem lessons

Patterns worth more than any single solve — the reusable *decisions*:

- **"Exploit the structural guarantee the problem hands you."** BST ordering (938, 538, 3831),
  permutation-ness (2657), bounded values (2974, 1874). Ask: *what does the problem promise,
  and how does that let me skip work (a sort, a full scan, a comparison)?* This is the signature
  move forming across the log.
- **"Match the tool to the constraint — both directions."** Sometimes the fancy tool is
  needless and simple wins (2974, 349, 3751 at 10⁵). Sometimes the fancy tool is the *only* one
  that scales (digit DP if 3751 were "Range II" at 10¹⁸). Reading the constraint picks the tool.
- **"Bounded values → counting sort is available but fragile"** — 2974, 2824, 1874.
- **"The thing that should move/shrink didn't"** — merge-sort pointer, quicksort recursion,
  538's `noe` typo. A variable that must change each iteration silently doesn't.
- **"O(n²) → O(n) is usually: stop recomputing what you could carry"** — 538 vs 1038.

---

- **"Read what the problem IS before coding."** Tonight's five: use the sorted-ness you're
  given (88), notice the definition collapses (3945), the constraint dictates the approach
  (1265 immutability, 1365 bounded values). The obvious code is often not the intended one.
- **"Recursion ⇄ explicit stack are the same thing."** invert (226), inorder (94), immutable-LL
  (1265) — the explicit stack simulates the call stack; reach for it to dodge recursion limits.
- **"√n decomposition threads the needle"** — when O(n) space is too much and O(n²) time too
  slow, store √n landmarks + O(√n) local work (1265).

---

- **"Asymptotically-best isn't always constraint-appropriate."** Met three times: digit DP
  (3751), √n decomposition (1265), matrix exponentiation (1411). The clever technique is correct
  and scales, but the given constraint often makes the simpler tool faster in practice — pick the
  simplest the constraint allows, unless a "Part II" cranks n up.
- **"Small N (≤ ~20) + choose a subset ⇒ enumerate all 2^N."** Bitmask loop or take-or-skip
  recursion — same search, iterative vs recursive (1255).
- **"SQL has no split-to-rows — manufacture iteration"** via a recursive CTE (peel) or a numbers
  table + `SUBSTRING_INDEX` (index the n-th field) (3368).

---

## Pattern rotation from the archive

The 98-problem archive grouped by family (see [LEARNING-DASHBOARD](./LEARNING-DASHBOARD.md)).
Re-drill pulls from whichever family feels rustiest, not in order.

**Recently touched:** Trees/BST (938, 1038→538, 226, 3831, 94) · Two Pointers (2824, 88) ·
Greedy (1874) · Hash/Set (349, 2657) · Sorting (2545, 1365) · Digit DP (3751) ·
Math collapse (3945) · Linked List (1265) · **SQL (3368)** · **Bitmask/backtracking (1255)** ·
**State-compression DP (1411)**.
**Next up:** more SQL (still the largest family) — 3368 only scratched it.
