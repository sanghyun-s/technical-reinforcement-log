# 339. Nested List Weight Sum

**Difficulty:** Medium
**Pattern:** Recursive DFS / N-ary Tree Traversal / Depth-Weighted Aggregation
**Date Solved:** 2026-06-14
**Status:** ✅

---

## Understanding the Goal

A nested list — like `[[1,1], 2, [1,1]]` — has integers and sub-lists at arbitrary depths. Each integer has a depth = "number of lists I'm inside of." Compute the **depth-weighted sum**.

**Key insight — recursive structure ⇔ recursive solution:**

The data is recursive (a list whose items can themselves be lists). The natural solution is also recursive: walk through each item; if it's an integer, contribute `value × depth`; if it's a list, recurse with `depth + 1`.

**This is essentially N-ary tree DFS:**
- Each list = a tree node
- Each integer = a leaf
- `getList()` = children
- "Depth" = standard tree depth, starting at 1

If you've internalized LC 104 Max Depth and LC 1302 Deepest Leaves Sum (today), this is the same template with one tweak: **multiply by depth on the way down**.

**The NestedInteger API:**
- `isInteger()` → True if leaf integer
- `getInteger()` → value (if leaf)
- `getList()` → nested list (if not leaf)

---

## LAYER 1: Line-by-Line Explanation

### Editorial — Recursive DFS

```python
class Solution:
    def depthSum(self, nestedList: List[NestedInteger]) -> int:
        def dfs(nested_list, depth):
            total = 0
            # Iterate each item at this level
            for nested in nested_list:
                if nested.isInteger():
                    # Leaf — contribute value × depth
                    total += nested.getInteger() * depth
                else:
                    # Recurse into sub-list, depth + 1
                    total += dfs(nested.getList(), depth + 1)
            return total
        
        # Start at depth 1 (outer list IS depth 1)
        return dfs(nestedList, 1)
```

**Why `depth=1` for outermost:**

Per problem definition: "number of lists I'm inside of." Outermost list IS one — every top-level integer is inside exactly ONE list. So they start at depth 1.

### Iterative BFS — explicit queue

```python
from collections import deque

class Solution:
    def depthSum(self, nestedList):
        queue = deque((item, 1) for item in nestedList)
        total = 0
        while queue:
            item, depth = queue.popleft()
            if item.isInteger():
                total += item.getInteger() * depth
            else:
                for child in item.getList():
                    queue.append((child, depth + 1))
        return total
```

### Level-aware BFS (mirrors LC 3831/1302 template)

```python
from collections import deque

class Solution:
    def depthSum(self, nestedList):
        queue = deque(nestedList)
        total = 0
        depth = 1
        while queue:
            size = len(queue)
            for _ in range(size):
                item = queue.popleft()
                if item.isInteger():
                    total += item.getInteger() * depth
                else:
                    queue.extend(item.getList())
            depth += 1
        return total
```

Instead of carrying depth per item, increment once per level. Same complexity, slightly less per-item overhead.

This is the **level-aware BFS template from LC 3831 and 1302** — applied to nested lists.

---

## LAYER 2: Worked Examples

### Example 1: `[[1,1], 2, [1,1]]` → 10

```
Level 1:  [ [1,1],    2,    [1,1] ]
              ↓               ↓
Level 2:    [1, 1]          [1, 1]
```

**DFS trace:**

`dfs([[1,1], 2, [1,1]], depth=1)`:
- `[1,1]` (list) → `dfs([1,1], 2)`:
  - `1` → 1 × 2 = 2
  - `1` → 1 × 2 = 2
  - returns 4
- `2` (int) → 2 × 1 = 2
- `[1,1]` (list) → `dfs([1,1], 2)` → 4

Total: 4 + 2 + 4 = **10** ✓

### Example 2: `[1, [4, [6]]]` → 27

`dfs([1, [4, [6]]], 1)`:
- `1` → 1 × 1 = 1
- `[4, [6]]` → `dfs([4, [6]], 2)`:
  - `4` → 4 × 2 = 8
  - `[6]` → `dfs([6], 3)`:
    - `6` → 6 × 3 = 18
    - returns 18
  - returns 26
- returns **27** ✓

### Deeply nested: `[1, [2, [3, [4, [5]]]]]` → 55

1×1 + 2×2 + 3×3 + 4×4 + 5×5 = 1 + 4 + 9 + 16 + 25 = **55**

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **Recursive DFS** ⭐ | **O(N)** | **O(D) recursion** | **Editorial; cleanest** |
| Iterative BFS | O(N) | O(N) queue worst | No recursion limit |
| Iterative DFS stack | O(N) | O(N) stack worst | No recursion limit |
| Level-aware BFS | O(N) | O(N) queue | Mirrors LC 3831/1302 |

For recursive data, **recursive DFS is the natural choice** — code mirrors the data structure.

**The "recursive data → recursive function" principle:**

When input is defined recursively (nested lists, trees, JSON), the natural solution is recursive. Each data case maps to a function clause:

```
DATA:                                FUNCTION:
nested_list = [item, item, ...]      def solve(nested_list):
item = integer | sub-list                for item in nested_list:
                                             if integer: handle leaf
                                             else: recurse on sub-list
```

This isomorphism is **structural recursion** — fundamental functional programming idea.

**This is N-ary tree DFS in disguise:**

| Binary tree | This problem |
|-------------|--------------|
| `node.left, node.right` | `for child in item.getList()` |
| `if not node.left and not node.right` (leaf) | `if item.isInteger()` (leaf) |
| Same DFS skeleton | Same DFS skeleton |

**Generalizing to LC 364 (Inverse Weight Sum):**

LC 364 asks for `sum(value × (max_depth - depth + 1))` — deepest get weight 1, shallowest get max. Two approaches:
1. Two-pass: find max_depth, then DFS with inverse weight
2. One-pass: maintain `unweighted_sum` and `weighted_total` that update level-wise

**Connection to past patterns:**

| Problem | Connection |
|---------|------------|
| LC 94 (Day 9) Inorder | Same skeleton (binary DFS) |
| LC 104 (Day 9) Max Depth | Depth tracking |
| LC 1302 (today) Deepest Sum | **Identical pattern, binary** |
| **LC 339 (this)** | **Same template, N-ary** |

You can compress: "**depth-aware DFS**" = same template, different reduction (max, sum, count, weighted sum).

**Why constraint `depth ≤ 50` matters:**

50 levels of recursion is well within Python's default 1000-limit. We don't need `sys.setrecursionlimit()`. For `depth ≤ 10⁵`, we'd need iterative DFS.

---

## LAYER 4: Interview Variations

• **Inverse depth weight (LC 364):** `value × (max_depth - depth + 1)`. Two-pass.
• **Average depth-weighted:** total / sum(weights).
• **Find max depth only:** Recurse and return depth.
• **Sum at specific depth k:** Filter during recursion.
• **Count of integers at depth:** Same idea, count instead of sum.
• **Flatten nested list:** Same recursion, append to result.
• **Maximum integer at any depth:** `max()` instead of `sum()`.
• **Compare two nested lists for equality:** Parallel DFS (like LC 100, N-ary).
• **Nested list to JSON-like dict:** Different goal; same skeleton.
• **Path-weighted (track path during DFS):** Pass path as parameter.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Recursive DFS (editorial)
class Solution:
    def depthSum(self, nestedList: List[NestedInteger]) -> int:
        def dfs(nested_list, depth):
            total = 0
            for nested in nested_list:
                if nested.isInteger():
                    total += nested.getInteger() * depth
                else:
                    total += dfs(nested.getList(), depth + 1)
            return total
        return dfs(nestedList, 1)
```

**Iterative BFS:**
```python
from collections import deque

def depthSum(nestedList):
    queue = deque((item, 1) for item in nestedList)
    total = 0
    while queue:
        item, depth = queue.popleft()
        if item.isInteger():
            total += item.getInteger() * depth
        else:
            for child in item.getList():
                queue.append((child, depth + 1))
    return total
```

**Iterative DFS with stack:**
```python
def depthSum(nestedList):
    stack = [(item, 1) for item in nestedList]
    total = 0
    while stack:
        item, depth = stack.pop()
        if item.isInteger():
            total += item.getInteger() * depth
        else:
            for child in item.getList():
                stack.append((child, depth + 1))
    return total
```

**Level-aware BFS:**
```python
from collections import deque

def depthSum(nestedList):
    queue = deque(nestedList)
    total = 0
    depth = 1
    while queue:
        size = len(queue)
        for _ in range(size):
            item = queue.popleft()
            if item.isInteger():
                total += item.getInteger() * depth
            else:
                queue.extend(item.getList())
        depth += 1
    return total
```

**LC 364 — Inverse Weight Sum (BONUS):**
```python
class Solution:
    def depthSumInverse(self, nestedList):
        def max_depth(items, depth):
            md = depth
            for item in items:
                if not item.isInteger():
                    md = max(md, max_depth(item.getList(), depth + 1))
            return md
        D = max_depth(nestedList, 1)
        
        def weighted_sum(items, depth):
            total = 0
            for item in items:
                if item.isInteger():
                    total += item.getInteger() * (D - depth + 1)
                else:
                    total += weighted_sum(item.getList(), depth + 1)
            return total
        return weighted_sum(nestedList, 1)
```

---

**Time:** O(N) | **Space:** O(D) recursion or O(N) iterative

**Pattern flag:** "Recursively structured data, compute aggregate over all leaves" → **structural recursion** mirroring the data definition. For each item: if leaf, contribute; if container, recurse. Pass depth/path/state as parameters.

This is **N-ary tree DFS** in disguise. Same template as LC 1302 (Deepest Leaves Sum) and LC 104 (Max Depth) — just iterating `for child in item.getList()` instead of `node.left, node.right`.

**The recursive-data → recursive-function principle:** when data has multiple cases (integer | list, leaf | branch), define one function with matching cases. **Structural recursion** appears in AST evaluation, JSON traversal, LISP, recursive descent parsers, tree problems.

🎯 **First N-ary "tree" DFS on the log** — generalizes binary tree DFS to arbitrary-degree branching.
