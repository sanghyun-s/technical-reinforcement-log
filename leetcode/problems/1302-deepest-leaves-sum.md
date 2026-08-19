# 1302. Deepest Leaves Sum

| | |
|---|---|
| **Difficulty** | Medium |
| **Family** | Trees (BFS / DFS) |
| **Date** | 2026-08-19 |
| **Mode** | Reference Recall (A1 submitted, A2–A4 hand-typed from archive) |
| **Link** | https://leetcode.com/problems/deepest-leaves-sum/ |

---

## 1. Problem
Return the sum of the values of the tree's **deepest** leaves.

## 2. The insight — "deepest leaves" = "the last level of a BFS"
Reframe it and the problem dissolves: BFS processes level by level, so whatever level you're on
when the queue empties is the deepest — its sum is the answer. No depth tracking, no comparisons,
no two passes. (Same level-by-level idea as 3831.)

## 3. Ranking the four (the ranking IS the lesson)
- **A4 — BFS, the intended elegant answer.** Snapshot each level with `for _ in range(len(queue))`,
  reset `level_sum` each level; when the queue empties, `level_sum` holds the deepest level. **No
  depth variable at all.** Cleanest.
- **A1 / A2 — single-pass DFS with running max-depth.** Track deepest depth; a *deeper* leaf
  **resets** the sum, an *equal* leaf **accumulates**. A2 recursive, A1 explicit-stack (dodges
  recursion limits) — the recursion↔stack duality again. Reset-or-accumulate is the correctness core.
- **A3 — two-pass (find max depth, then sum at it) — the WEAKEST, and it's what Hints 1–2 literally
  describe.** Correct, but traverses the whole tree **twice** for no benefit; A1/A2/A4 are single-pass.
  **Following the hints leads to the inferior solution** — recognising "fold two passes into one" is
  the optimisation instinct (the 538 / Counting Bits "don't traverse twice" lesson).

All four O(n) time / O(n) space — so the distinction is purely elegance and pass count.

---

## My solutions
```python
# A4 — BFS (lead with this): last level's sum, no depth tracking
from collections import deque
def deepestLeavesSum(self, root):
    queue = deque([root]); level_sum = 0
    while queue:
        level_sum = 0
        for _ in range(len(queue)):
            node = queue.popleft(); level_sum += node.val
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
    return level_sum

# A2 — recursive DFS: deeper leaf resets total, equal leaf accumulates
# A1 — same, explicit stack of (node, depth); push RIGHT first so LEFT pops first
# A3 — two-pass: target = max_depth(root) - 1, then sum nodes at target  (naive; traverses twice)
```

## Notes
- **A1 push order:** "push right first so left pops first" is correct — a stack reverses order on pop.
- **A3 off-by-one:** `max_depth(root) - 1` converts 1-based depth count to the 0-based `target` — got it right.
- The interview-worthy move: *the hints suggest two passes, but BFS does it in one by summing the last level.*

## Interview sentence
> The deepest leaves are just the last level of a BFS — level-order traversal, reset a running sum
> each level, and when the queue empties the last sum is the answer. No depth tracking. The hints
> suggest two passes (find max depth, then sum at it), but BFS folds that into one.
