# 3831. Median of a Binary Search Tree Level

**Difficulty:** Medium
**Pattern:** Tree DFS/BFS / Level Collection / Median Finding
**Date Solved:** 2026-06-14
**Status:** ✅

---

## Understanding the Goal

Collect all node values at a specific `level` of a BST, return the median. For even count, return the **upper** median. If level is empty/missing, return -1.

**Two natural approaches:**

1. **BFS (level-order)** — processes one level at a time; stops naturally when target is reached
2. **DFS (depth-tracking)** — recursive descent, append to collector when `depth == level`

**Bonus insight — the BST property:**

The hint says "sort the values at that level," but BFS level-order on a BST visits each level in **sorted ascending order**. So you don't strictly need to sort if you use BFS.

**The upper-median formula:**

For sorted array `arr` of length `n`, **`arr[n // 2]`** is always the upper median:
- n=1: `arr[0]`
- n=2: `arr[1]` (upper of two)
- n=3: `arr[1]` (middle)
- n=4: `arr[2]` (upper of two middles)

---

## LAYER 1: Line-by-Line Explanation

### Approach 1: BFS — Level-Order Traversal ⭐

```python
from collections import deque

class Solution:
    def levelMedian(self, root: Optional[TreeNode], level: int) -> int:
        if not root:
            return -1
        
        queue = deque([root])
        current_level = 0
        
        # Advance level by level until we reach `level`
        while queue and current_level < level:
            size = len(queue)                    # snapshot the level size
            for _ in range(size):
                node = queue.popleft()
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            current_level += 1
        
        # Two exit cases:
        # 1. Reached the target level — queue holds target-level nodes
        # 2. Queue empty before reaching level — return -1
        if not queue:
            return -1
        
        # Sort and find upper median
        values = sorted(node.val for node in queue)
        n = len(values)
        return values[n // 2]                    # upper median formula
```

**Key BFS detail — `size = len(queue)` snapshot:**

To process EXACTLY one level per outer iteration, capture the queue size at the start. Then only `pop` that many times before advancing the level counter. Children added during this iteration are at the NEXT level — they wait.

### Approach 2: DFS Recursive

```python
class Solution:
    def levelMedian(self, root, level):
        values = []
        
        def dfs(node, depth):
            if not node:
                return
            if depth == level:
                values.append(node.val)
                return
            if depth < level:                    # pruning
                dfs(node.left, depth + 1)
                dfs(node.right, depth + 1)
        
        dfs(root, 0)
        
        if not values:
            return -1
        values.sort()
        return values[len(values) // 2]
```

### Approach 3: BFS Optimized — exploit BST property (no sort)

```python
from collections import deque

class Solution:
    def levelMedian(self, root, level):
        if not root:
            return -1
        
        queue = deque([root])
        for _ in range(level):
            if not queue:
                return -1
            queue = deque(
                child
                for node in queue
                for child in (node.left, node.right)
                if child
            )
        
        if not queue:
            return -1
        
        # BST + BFS = level is ALREADY sorted ascending
        # Just index directly — no sort needed
        return queue[len(queue) // 2].val
```

**Why no sort needed:** for any two nodes A, B at the same level with A before B in BFS, their LCA L has A in L's left subtree and B in L's right. BST property: A.val < L.val < B.val. So BFS naturally yields sorted order.

---

## LAYER 2: Worked Examples

### Example 1: `root = [4,null,5,null,7], level = 2` → 7

```
    4
     \
      5
       \
        7
```

BFS trace:
- iter 0: queue=[4], level=0. Process 4 → queue=[5]. level=1.
- iter 1: queue=[5], level=1. Process 5 → queue=[7]. level=2 — EXIT.
- Queue has [7]. Median: 7 ✓

### Example 2: `root = [6,3,8], level = 1` → 8

```
    6
   / \
  3   8
```

Queue at level 1: [3, 8]. Already sorted!
Median: `[3, 8][2 // 2] = 8` ← **upper median** ✓

### Example 3: `root = [2, 1], level = 2` → -1

```
    2
   /
  1
```

Level 2 doesn't exist → queue empties before reaching → **-1** ✓

### Larger BST sanity check

```
        50
       /  \
      30   70
     / \   / \
    20 40 60 80
```

Level 2 via BFS: [20, 40, 60, 80] — already sorted ✓
Median: `[20,40,60,80][2] = 60` (upper of 40, 60).

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **BFS + sort** | O(n + w log w) | O(w) | Hint's approach |
| **BFS no-sort (BST)** ⭐ | **O(n)** | **O(w)** | **Exploits BST** |
| DFS recursive | O(n + w log w) | O(h) | Recursive idea |

**The level-by-level BFS template (MEMORIZE):**

```python
queue = deque([root])
while queue:
    size = len(queue)              # snapshot — THE trick
    for _ in range(size):
        node = queue.popleft()
        # process node at current level
        if node.left: queue.append(node.left)
        if node.right: queue.append(node.right)
    # at this point, finished one level
```

The `size = len(queue)` capture is what makes BFS "level-aware." Without it, you process nodes mixing levels.

**Applies to:** LC 102, 107, 199, 515, 637, **LC 3831 (this), LC 1302**, LC 994 (BFS grid), LC 127 (shortest path BFS).

**BFS vs DFS for level-based problems:**

| Pattern | BFS | DFS |
|---------|-----|-----|
| "Process by level" / "level-k values" | **Natural** | Awkward |
| "Path sum" / "subtree property" | Awkward | **Natural** |

**The "upper median index" trick — `arr[n // 2]`:**

| n | `n // 2` | Result |
|---|----------|--------|
| 1 | 0 | only element |
| 2 | 1 | upper of two |
| 3 | 1 | middle |
| 4 | 2 | upper of two middles |

**One formula handles both odd and even** with upper-median selection.

For **lower median:** `arr[(n - 1) // 2]`.

**BST property — when it actually helps:**

| Problem | BST helps? |
|---------|------------|
| LC 938 Range Sum | Yes — prune recursion |
| LC 1038 BST to Greater Sum | Yes — reverse in-order |
| LC 1382 Balance a BST | Yes — in-order gives sorted |
| **LC 3831 (this)** | **Bonus: BFS yields sorted levels** |

---

## LAYER 4: Interview Variations

• **Lower median:** `arr[(n - 1) // 2]`.
• **True median for even (average of two middles):** `(arr[n//2 - 1] + arr[n//2]) / 2`.
• **Median at multiple levels:** Modified BFS, per-level computation.
• **K-th smallest at level:** Sort and index.
• **Sum / max / min of a level:** Same BFS skeleton, different reduction.
• **Non-BST tree:** BFS still works; lose the sort optimization.
• **N-ary tree:** Replace `node.left, node.right` with `for child in node.children`.
• **Streaming median (insertions):** SortedList or two heaps.
• **Median of all levels combined:** Quickselect O(n).

---

## LAYER 5: Cheat Sheet

```python
# Preferred — BFS with sort
from collections import deque

class Solution:
    def levelMedian(self, root: Optional[TreeNode], level: int) -> int:
        if not root:
            return -1
        queue = deque([root])
        current_level = 0
        while queue and current_level < level:
            for _ in range(len(queue)):
                node = queue.popleft()
                if node.left: queue.append(node.left)
                if node.right: queue.append(node.right)
            current_level += 1
        if not queue:
            return -1
        values = sorted(node.val for node in queue)
        return values[len(values) // 2]
```

**BFS optimized — no sort:**
```python
from collections import deque

def levelMedian(root, level):
    if not root: return -1
    queue = deque([root])
    for _ in range(level):
        if not queue: return -1
        queue = deque(
            child for node in queue
            for child in (node.left, node.right) if child
        )
    if not queue: return -1
    return queue[len(queue) // 2].val
```

**DFS recursive:**
```python
def levelMedian(root, level):
    values = []
    def dfs(node, depth):
        if not node: return
        if depth == level:
            values.append(node.val); return
        if depth < level:
            dfs(node.left, depth + 1)
            dfs(node.right, depth + 1)
    dfs(root, 0)
    if not values: return -1
    values.sort()
    return values[len(values) // 2]
```

**Lower median variation:**
```python
n = len(values)
return values[n // 2] if n % 2 == 1 else values[n // 2 - 1]
```

---

**Time:** O(n) BFS optimized / O(n + w log w) with sort | **Space:** O(w) BFS, O(h) DFS

**Pattern flag:** "Process tree at a specific depth/level" → **BFS with `size = len(queue)` snapshot**. Snapshot the queue size at start; pop exactly that many to process one level. This is THE level-aware BFS template.

For tree problems involving "level," "depth," "shortest path" → BFS is natural.
For tree problems involving "path sum," "subtree property" → DFS is natural.

**Bonus insight — BST property:** BFS level-order on a BST yields each level in sorted order. Skip the sort, save O(w log w).

**The upper-median formula `arr[n // 2]`** works for all `n` — handles both odd (true middle) and even (upper of two middles) without branching.

🎯 **First level-aware BFS on the log.** Adds BFS to your tree toolkit (you had DFS in 5+ flavors from Day 9). BFS + DFS cover essentially all tree traversal scenarios.
