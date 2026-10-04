# Data Structures and Algorithms in Python

| | |
|---|---|
| **Priority** | Tier A · #1 (roadmap) |
| **Status** | ✅ Complete — all 4 chapters |
| **Started** | 2026-07-19 |
| **Completed** | 2026-07-26 |
| **Link** | https://app.datacamp.com/learn/courses/data-structures-and-algorithms-in-python |

---

## What this defends

The data structures and algorithms *underneath* the LeetCode patterns. Covers stack/queue,
trees (DFS/BFS), graphs, recursion, binary search, and all the core sorts — directly touching
five of the eight §5.2 families, plus the Big O vocabulary to justify any of them. Capability,
not positioning: it changes what I can build and analyze, not just what I can say.

---

## Chapter map (what each closed)

- **Ch 1 — Foundations:** linked lists, stacks, Big O.
- **Ch 2 — Structures:** queues, hash tables, trees, graphs, recursion.
- **Ch 3 — Searching:** linear search, binary search (iterative + recursive), DFS, BFS, BST search.
- **Ch 4 — Sorting:** bubble, selection, insertion, merge, quicksort.

---

## Chapter 4 — sorting (final section)

**Concepts**
- **Bubble sort** — repeated adjacent swaps; the `is_sorted` flag lets it exit early once a
  full clean pass happens, and shrinking `list_length` each pass skips the already-settled
  tail. O(n²) worst case, O(n) on an already-sorted list thanks to the flag.
- **Selection sort** — each pass finds the min of the unsorted region and swaps it into place.
  Tracks both `lowest` (value) and `index` (where). Always O(n²), but few swaps.
- **Merge sort** — divide and conquer: split to halves, sort recursively, merge. O(n log n)
  guaranteed, but O(n) extra space for the halves.
- **Quicksort (Hoare partition)** — pick a pivot, run two pointers inward swapping
  out-of-place pairs until they cross, drop the pivot into place, recurse on each side.
  O(n log n) average, O(1) extra space, but O(n²) if pivots are unlucky.

**Bugs I caught / fixed (the transferable part)**
- **Merge sort pointer bug** — in the two "copy the leftovers" loops, you must advance *that
  loop's own* pointer (`i` in the left-half loop, `j` in the right-half loop). Incrementing the
  wrong one re-reads the same element forever → index runs off the end → `IndexError`.
- **Quicksort infinite recursion** — recursing on `(first, last)` (the whole list) instead of
  the sub-ranges `(first, partition)` and `(partition+1, last)` never shrinks the input →
  `RecursionError`. The recursive call has to work on something *smaller*.
- **Pattern across both:** "the thing that should move didn't." A pointer that doesn't advance,
  or a recursion that doesn't shrink its input, is the same failure wearing two costumes — and
  it's the same family as the Fibonacci/Hanoi slips from Ch 2.

---

## Direct hits on my own codebase

| Concept | Where it shows up | What I now understand |
|---|---|---|
| Big O worst case | Every "complexity?" interview question | Can justify O(1) / O(log n) / O(n log n) / O(n²), not just assert |
| Divide & conquer | Any recursive split (merge sort, tree ops) | Why O(n log n) beats O(n²) sorting |
| Two-pointer partition | Quicksort; LC 360 two-pointer | Same converging-pointer idea I used on Sort Transformed Array |
| Recursion that shrinks input | PDF/agent recursion in PREPARE | A recursive call must reduce the problem, or it never terminates |

---

## Still unclear (open — internal note)

- Insertion sort — named in the recap but I didn't hand-implement it; worth one pass.
- Quicksort pivot strategies (median-of-three, random) to dodge the O(n²) worst case.
- BST *delete* (insert + search solid; delete's three cases still fuzzy).

---

## Interview sentence

> I finished the DSA course by hand-implementing the core sorts, and the most useful part
> wasn't the algorithms — it was debugging them: a merge that re-reads an element because the
> wrong pointer advances, or a quicksort that never terminates because it recurses on the whole
> list. Both are the same bug — something that should shrink or advance doesn't — and spotting
> that class fast is exactly the code-reading fluency I'm building.

---

## Follow-up keywords

`merge sort O(n log n)` · `quicksort partition` · `divide and conquer` · `pointer advance bug` · `recursion must shrink input` · `Big O of sorts`
