# Weekly Technical Reinforcement Log

The main stage. Every study action — LeetCode, DataCamp, YouTube, app-code reading — lands
here first, so the most recent week is visible without opening a single folder.

**Reading the Mode column:** it's an honesty label, not a grade. See the legend in
[README](./README.md#how-work-is-labeled).

---

## Week of 2026-08-19

| Date | Track | Activity | Mode | Repetition? | Takeaway | Interview line? |
|---|---|---|---|---|---|---|
| 08-19 | LeetCode | LC 1302 Deepest Leaves Sum | Reference Recall | Re-drill | "Deepest leaves = last BFS level" — reset sum per level, no depth tracking; hints lead to weaker two-pass | Candidate |
| 08-19 | LeetCode | LC 1382 Balance a BST | Reference Recall | Re-drill | Inorder → sorted → middle-as-root; midpoint choice guarantees balance (binary search run backwards) | Candidate |

## Week of 2026-08-12

| Date | Track | Activity | Mode | Repetition? | Takeaway | Interview line? |
|---|---|---|---|---|---|---|
| 08-17 | DataCamp | Testing in Python — Ch 1: `assert` tests, `pytest.raises`, markers (skip/skipif/xfail), CLI + `-k` | Course-Guided | New | Answers "how do you verify AI code?" — write asserts vs specified behaviour + `pytest.raises`; caught the `list(set())` order-fragility | **Promoted** |
| 08-16 | LeetCode | LC 0561 Array Partition | Reference Recall | Re-drill | Sort + sum even indices; adjacent-pair greedy is optimal (swap-never-worse proof); counting sort over-engineering at n≤10⁴ | Candidate |
| 08-16 | LeetCode | LC 0338 Counting Bits | Reference Recall | Re-drill | DP `ans[i]=ans[i>>1]+(i&1)` collapses O(n log n)→O(n): bits of i = bits of i//2 + last bit | Candidate |
| 08-16 | LeetCode | LC 1637 Widest Vertical Area | Reference Recall | Re-drill | y is a red herring — 1D problem; sort x's, max adjacent gap | Candidate |
| 08-15 | LeetCode | LC 1411 Ways to Paint N×3 Grid (**Hard**) | Reference Recall | Re-drill | 12 patterns → 2 classes (ABA/ABC); DP ladder O(144n)→O(n)→O(log n); matrix expo over-engineering at n≤5000 | Candidate |
| 08-15 | LeetCode | LC 1255 Max Score Words (**Hard**) | Reference Recall | Re-drill | words≤14 ⇒ enumerate 2^14 subsets; bitmask ⇄ take-or-skip recursion are the same search | Candidate |
| 08-15 | LeetCode | LC 3368 First Letter Capitalization (**Hard, SQL**) | Reference Recall | Re-drill | SQL has no split-to-rows: recursive CTE (peel) vs numbers table + `SUBSTRING_INDEX` (index n-th word) | Candidate |
| 08-11 | LeetCode | LC 1265 Print Immutable LL in Reverse | Reference Recall | Re-drill | Space/time frontier: recursion O(n)/O(n) → brute O(n²)/O(1) → **√n decomposition** O(n)/O(√n) | Candidate |
| 08-11 | LeetCode | LC 1365 Smaller Numbers Than Current | Reference Recall | Re-drill | 3-tier ladder; bounded values ⇒ counting sort is the *correct* optimal (positive mirror of 3751) | Candidate |
| 08-11 | LeetCode | LC 0094 Binary Tree Inorder | Reference Recall | Re-drill | Iterative = recursion made visible; explicit stack mimics the call stack | Candidate |
| 08-11 | LeetCode | LC 3945 Digit Frequency Score | Reference Recall | Re-drill | Math collapse: Σ d·freq(d) IS just the digit sum — no frequencies needed | Candidate |
| 08-11 | LeetCode | LC 0088 Merge Sorted Array | Reference Recall | Re-drill | Back-merge in place, O(m+n); walk backwards because the free space is at the tail | Candidate |

## Week of 2026-08-05

| Date | Track | Activity | Mode | Repetition? | Takeaway | Interview line? |
|---|---|---|---|---|---|---|
| 08-10 | DataCamp | OOP in Python — Ch 4: Liskov Substitution, internal attrs, **`@property`** getters/setters (**course complete**) | Course-Guided | New | `@property` + setter validates on every assignment — enforce "balance never negative" on financial fields; Liskov: "is-a" can be wrong | **Promoted** |
| 08-08 | DataCamp | OOP in Python — Ch 2–3: class attributes, `@classmethod`, **inheritance**, `super()`, `__eq__` | Course-Guided | New | `class MyModel(BaseModel)` IS inheritance — closes the Pydantic question; subclassed a real `pd.DataFrame` | **Promoted** |

## Week of 2026-07-29

| Date | Track | Activity | Mode | Repetition? | Takeaway | Interview line? |
|---|---|---|---|---|---|---|
| 07-31 | LeetCode | LC 2545 Sort Students by Kth Score | Mixed | Mix | Sort by key; `sorted` (new list) vs `.sort()` (mutate); `itemgetter` over lambda | Candidate |
| 07-31 | LeetCode | LC 2657 Prefix Common Array | Reference Recall | Re-drill | Permutation ⇒ a value hitting freq 2 is common; count that, no sets. Structural-guarantee move | Yes |
| 07-31 | LeetCode | LC 3751 Total Waviness (Range I) | Reference Recall (near-stuck) | Re-drill | Brute force wins at 10⁵; digit DP is over-engineering here but the only thing that scales to 10¹⁸ | Yes |
| 07-31 | LeetCode | LC 3831 Median of a BST Level | Reference Recall (shaky) | Re-drill | BST + left-to-right BFS ⇒ level already sorted, index median with no sort; recursive rebuild took 3 tries | Candidate |
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
| 2026-08-19 | 2 | 0 | 2 | 0 | 0 | 0 |
| 2026-08-12 | 12 | 0 | 11 | 1 | 0 | 1 |
| 2026-08-05 | 2 | 0 | 0 | 2 | 0 | 2 |
| 2026-07-29 | 7 | 0 | 6 | 1 | 0 | 1 |
| 2026-07-22 | 11 | 3 | 6 | 2 | 0 | 4 |
| 2026-07-15 | 2 | 2 | 0 | 2 | 0 | 3 |

*Cold solves are intentionally low right now — the current phase is understanding + recall, per
the repetition-first rule. Cold solves ramp up closer to fall recruiting. Six re-drills this
week across Two Pointers, Trees/BST, and Greedy is the momentum the plan is built on.*
