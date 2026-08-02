# Weekly Technical Reinforcement Log

The main stage. Every study action — LeetCode, DataCamp, YouTube, app-code reading — lands
here first, so the most recent week is visible without opening a single folder.

**Reading the Mode column:** it's an honesty label, not a grade. See the legend in
[README](./README.md#how-work-is-labeled).

---

## Week of 2026-07-29

| Date | Track | Activity | Mode | Repetition? | Takeaway | Interview line? |
|---|---|---|---|---|---|---|
| 07-30 | LeetCode | LC 0349 Intersection of Two Arrays | Reference Recall | Re-drill | `set(a) & set(b)` is the answer; reached for two-pointer but didn't need it (recurring judgment) | Candidate |
| 07-30 | LeetCode | LC 0226 Invert Binary Tree | Reference Recall | Re-drill | 4 traversals; DFS vs BFS differ only by `pop()` vs `popleft()`; swap is order-independent | Candidate |
| 07-30 | DataCamp | OOP in Python — Ch 1: classes, `self`, `__init__`, constructor validation, `Point` from scratch | Course-Guided | New | `__init__` validates + assigns at creation = what Pydantic automates; moves the Pydantic question toward defensible | Yes |

## Week of 2026-07-22

| Date | Track | Activity | Mode | Repetition? | Takeaway | Interview line? |
|---|---|---|---|---|---|---|
| 07-26 | LeetCode | LC 1874 Minimize Product Sum | Reference Recall | Re-drill | Rearrangement inequality: pair ascending × descending; counting-sort alt only if bounded | Candidate |
| 07-26 | LeetCode | LC 538 Convert BST to Greater Tree (new; twin of 1038) | First-Pass Assisted | New | Reverse-inorder carries a running total → O(n); collect-and-re-sum is O(n²) | Yes |
| 07-26 | LeetCode | LC 1038 Greater Sum Tree | Reference Recall | Re-drill | Set up 538 — my archived version was the O(n²) one | Yes |
| 07-26 | LeetCode | LC 938 Range Sum of BST | Reference Recall | Re-drill | BST pruning skips whole subtrees out of range | Candidate |
| 07-26 | LeetCode | LC 2824 Count Pairs Sum < Target | Pattern Re-Drill | Re-drill | `count += right-left` clears a block; sorting valid for unordered-pair count | Candidate |
| 07-26 | DataCamp | DSA in Python — Ch 4: sorts (**course complete**) | Course-Guided | New | Merge-sort pointer bug + quicksort infinite recursion | Yes |
| 07-26 | LeetCode | LC 2974 Minimum Number Game | Reference Recall | New | Sort + pair-swap beats counting-sort here | Yes |
| 07-25 | DataCamp | DSA in Python — Ch 2–3: queues, trees, graphs, recursion, binary search, BFS | Course-Guided | New | Built BST + weighted graph + both binary searches; caught a Fibonacci slip | Yes |
| 07-24 | LeetCode | LC 360 Sort Transformed Array | First-Pass Assisted | New | Two-pointer from both ends (parabola extremes at array ends) | Recall owed |
| 07-21 | LeetCode | LC 0001 Two Sum | Pattern Re-Drill | Yes | Hash-map + brute force from memory; check-before-store | Yes |
| 07-21 | LeetCode | LC 0014 Longest Common Prefix | Reference Recall | Archive | Horizontal-scan shrinking; `startswith` over `find()==0` | Recall owed |

## Week of 2026-07-15

| Date | Track | Activity | Mode | Repetition? | Takeaway | Interview line? |
|---|---|---|---|---|---|---|
| 07-19 | DataCamp | DSA in Python — Ch 1: linked lists, stacks, Big O | Course-Guided | New | Built a stack by hand; O(1) push/pop; empty-stack guard | Yes |
| 07-19 | LeetCode | LC 1165 Single-Row Keyboard | First-Pass Assisted | New | Precompute char→index dict for O(1) lookup | Yes |
| 07-19 | LeetCode | LC 3925 Concatenate Array With Reverse | First-Pass Assisted | New | Slicing/`+` build new lists; in-place reverse is the trap | Yes |
| 07-17 | DataCamp | Intermediate Python for Developers (complete) | Course-Guided | New | `*args`/`**kwargs`, docstrings as contract, typed errors | Yes |

---

## Weekly rollup

Momentum over perfection. The point is that the rows exist, week after week.

| Week | Sessions | New problems | Re-drills / recall | Course reps | Cold solves | Sentences promoted |
|---|---|---|---|---|---|---|
| 2026-07-29 | 3 | 0 | 2 | 1 | 0 | 1 |
| 2026-07-22 | 11 | 3 | 6 | 2 | 0 | 4 |
| 2026-07-15 | 2 | 2 | 0 | 2 | 0 | 3 |

*Cold solves are intentionally low right now — the current phase is understanding + recall, per
the repetition-first rule. Cold solves ramp up closer to fall recruiting. Six re-drills this
week across Two Pointers, Trees/BST, and Greedy is the momentum the plan is built on.*
