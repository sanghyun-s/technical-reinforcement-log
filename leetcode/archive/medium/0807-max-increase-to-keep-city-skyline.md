# 807. Max Increase to Keep City Skyline

**Difficulty:** Medium
**Pattern:** Greedy / Matrix / Two-Constraint Cap / Row-Col Aggregation
**Date Solved:** 2026-06-14
**Status:** ✅

---

## Understanding the Goal

A 2D grid of building heights. The "skyline" viewed from each of the 4 cardinal directions is the outer contour:

- **North/South skyline** = looking down/up the columns → **max of each column**
- **East/West skyline** = looking across the rows → **max of each row**

We can raise any building, as long as **all four skylines stay exactly the same.** Maximize the total increase.

**The greedy insight (the entire problem):**

For each building at `(r, c)`:
- If we raise it above `row_max[r]`, the East/West skyline changes — forbidden
- If we raise it above `col_max[c]`, the North/South skyline changes — forbidden
- So the maximum allowed new height is **`min(row_max[r], col_max[c])`**

The increase per cell is `min(row_max[r], col_max[c]) - grid[r][c]`. Sum across all cells.

**Why "Greedy":** each cell's decision is **independent** — pushing one cell to its cap doesn't restrict any other cell. We can greedily push every building to its local max.

---

## LAYER 1: Line-by-Line Explanation

### Editorial — Pythonic with `zip(*grid)` transpose

```python
class Solution:
    def maxIncreaseKeepingSkyline(self, grid: List[List[int]]) -> int:
        # Step 1: row_maxes[r] = max value in row r
        row_maxes = [max(row) for row in grid]
        
        # Step 2: col_maxes[c] = max value in column c
        # zip(*grid) transposes — yields columns as tuples
        # *grid unpacks the list of rows as separate arguments to zip()
        col_maxes = [max(col) for col in zip(*grid)]
        
        # Step 3: for each cell, compute increase
        # cap = min(row_max[r], col_max[c]); increase = cap - original
        return sum(
            min(row_maxes[r], col_maxes[c]) - val
            for r, row in enumerate(grid)
            for c, val in enumerate(row)
        )
```

**The `zip(*grid)` trick (memorize):**

```python
grid = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
list(zip(*grid))
# = [(1, 4, 7), (2, 5, 8), (3, 6, 9)]
#    ↑ col 0     ↑ col 1     ↑ col 2
```

`*grid` unpacks the list `[row0, row1, row2]` into separate arguments to `zip`. zip then groups the i-th element of each, yielding columns. **This is the canonical Python matrix transpose** — internalize it.

### Explicit double-loop version

```python
class Solution:
    def maxIncreaseKeepingSkyline(self, grid: List[List[int]]) -> int:
        n = len(grid)
        row_maxes = [max(grid[r]) for r in range(n)]
        col_maxes = [max(grid[r][c] for r in range(n)) for c in range(n)]
        
        total = 0
        for r in range(n):
            for c in range(n):
                total += min(row_maxes[r], col_maxes[c]) - grid[r][c]
        return total
```

Whiteboard-friendly.

### NumPy version

```python
import numpy as np

class Solution:
    def maxIncreaseKeepingSkyline(self, grid: List[List[int]]) -> int:
        g = np.array(grid)
        row_maxes = g.max(axis=1)
        col_maxes = g.max(axis=0)
        caps = np.minimum(row_maxes[:, None], col_maxes[None, :])
        return int((caps - g).sum())
```

Broadcast min: `row_maxes[:, None]` shapes to (n, 1), `col_maxes[None, :]` shapes to (1, n), `np.minimum` broadcasts to (n, n).

---

## LAYER 2: Worked Examples

### Example 1: `grid = [[3,0,8,4],[2,4,5,7],[9,2,6,3],[0,3,1,0]]` → 35

**Row maxes:** [8, 7, 9, 3]
**Col maxes:** [9, 4, 8, 7]

For each cell, cap = `min(row_max[r], col_max[c])`:

| | c=0 (9) | c=1 (4) | c=2 (8) | c=3 (7) |
|---|---|---|---|---|
| **r=0 (8)** | min(8,9)=8 orig 3 → +5 | min(8,4)=4 orig 0 → +4 | min(8,8)=8 orig 8 → +0 | min(8,7)=7 orig 4 → +3 |
| **r=1 (7)** | min(7,9)=7 orig 2 → +5 | min(7,4)=4 orig 4 → +0 | min(7,8)=7 orig 5 → +2 | min(7,7)=7 orig 7 → +0 |
| **r=2 (9)** | min(9,9)=9 orig 9 → +0 | min(9,4)=4 orig 2 → +2 | min(9,8)=8 orig 6 → +2 | min(9,7)=7 orig 3 → +4 |
| **r=3 (3)** | min(3,9)=3 orig 0 → +3 | min(3,4)=3 orig 3 → +0 | min(3,8)=3 orig 1 → +2 | min(3,7)=3 orig 0 → +3 |

Row sums: 12 + 7 + 8 + 8 = **35** ✓

Verify skylines unchanged: row maxes [8,7,9,3] preserved ✓; col maxes [9,4,8,7] preserved ✓.

### Example 2: `[[0,0,0],[0,0,0],[0,0,0]]` → 0

All caps are 0. Every cell already at cap. **0** ✓

### 2×2 sanity: `[[1,2],[3,4]]`

row_maxes=[2,4], col_maxes=[3,4].
- (0,0): min(2,3)=2 orig 1 → +1
- (0,1): min(2,4)=2 orig 2 → +0
- (1,0): min(4,3)=3 orig 3 → +0
- (1,1): min(4,4)=4 orig 4 → +0

Total: **1** ✓

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **List comp + sum** | **O(n²)** | **O(n)** | **Preferred** |
| Explicit double loop | O(n²) | O(n) | Whiteboard |
| NumPy broadcast | O(n²) | O(n²) intermediate | Wins constants for large n |

**The "two-constraint cap" pattern:**

> "Each item has multiple independent upper bounds. Push each to the minimum of its bounds."

```python
caps[i] = min(bound_1[i], bound_2[i], ..., bound_k[i])
gain = sum(caps[i] - original[i])
```

For 3D grids, just extend the min to 3-way.

**Why this is "Greedy" not DP:**

Raising building (1,1) to its cap doesn't change the row max or column max of OTHER cells. So each cell is **independently** maximized. That's the textbook **greedy condition**: local optimum ⇒ global optimum.

**Exchange argument:** any non-cap configuration can be raised to the cap without changing any skyline → greedy is optimal.

**Connection to past problems:**

| Problem | Pattern | What was reduced? |
|---------|---------|--------------------|
| LC 1672 (Day 4B) Richest Wealth | Row sum + max | `max(sum(row))` |
| LC 3898 (Day 12) Vertex Degrees | Row sum | `[sum(row)]` |
| LC 807 (this) | **Row max + col max + min** | **`min(row_max, col_max)`** |

**The `zip(*grid)` transpose:** the canonical Python idiom for column-wise operations. Memorize.

**4 directions collapse to 2:** North = South (columns), East = West (rows). So "preserve 4 skylines" really means "preserve all row AND col maxes." Two constraints per cell.

---

## LAYER 4: Interview Variations

• **Single direction must stay:** Cap is just `row_max[r]` (or `col_max[c]`).
• **Decrease allowed too:** Same formula; sum of absolute differences.
• **3D grid:** Three-way min of row/col/depth maxes.
• **Diagonal skylines too:** Add diagonal max constraints; 4-way min.
• **Weighted increases:** `sum((cap - val) * weight[r][c])`.
• **Find which cells were raised most:** Track per-cell increase, sort.
• **Streaming cells:** Maintain running row/col maxes.
• **K-th tallest skyline:** Different problem; harder.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic
class Solution:
    def maxIncreaseKeepingSkyline(self, grid: List[List[int]]) -> int:
        row_maxes = [max(row) for row in grid]
        col_maxes = [max(col) for col in zip(*grid)]
        return sum(
            min(row_maxes[r], col_maxes[c]) - val
            for r, row in enumerate(grid)
            for c, val in enumerate(row)
        )
```

**Explicit loop:**
```python
def maxIncreaseKeepingSkyline(grid):
    n = len(grid)
    row_maxes = [max(grid[r]) for r in range(n)]
    col_maxes = [max(grid[r][c] for r in range(n)) for c in range(n)]
    total = 0
    for r in range(n):
        for c in range(n):
            total += min(row_maxes[r], col_maxes[c]) - grid[r][c]
    return total
```

**Functional with map:**
```python
def maxIncreaseKeepingSkyline(grid):
    row_maxes = list(map(max, grid))
    col_maxes = list(map(max, zip(*grid)))
    return sum(min(row_maxes[r], col_maxes[c]) - grid[r][c]
               for r in range(len(grid))
               for c in range(len(grid)))
```

**NumPy:**
```python
import numpy as np

def maxIncreaseKeepingSkyline(grid):
    g = np.array(grid)
    rm, cm = g.max(axis=1), g.max(axis=0)
    return int((np.minimum(rm[:, None], cm[None, :]) - g).sum())
```

---

**Time:** O(n²) | **Space:** O(n)

**Pattern flag:** "Each item has multiple independent upper bounds; push each to the min" → **two-constraint cap pattern**. Compute bound arrays separately; per cell take `min`. Sum gains.

The **transpose trick `zip(*grid)`** is key for 2D grid problems — reduce columns the same way you reduce rows:

```python
row_aggs = [f(row) for row in grid]
col_aggs = [f(col) for col in zip(*grid)]
```

**First explicit Greedy/Matrix combo on the log.** Day 5 covered greedy on arrays; LC 1672/3898 covered matrix row sums. LC 807 puts them together with the two-constraint twist.

🎯 **Why this is Greedy, not DP:** each cell's optimal choice is independent — raising one doesn't change any other's cap. Local optimum = global optimum.
