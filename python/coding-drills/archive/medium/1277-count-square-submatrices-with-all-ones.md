# 1277. Count Square Submatrices with All Ones

**Difficulty:** Medium
**Pattern:** Dynamic Programming / 2D Grid DP / In-Place Counting
**Date Solved:** 2026-06-09
**Status:** ✅

---

## Understanding the Goal

Count every square submatrix (any size 1×1 up to k×k) where ALL cells are 1.

**The key insight (the magic):**

> `dp[i][j]` = side length of largest all-ones square whose **bottom-right corner is at (i, j)**.

If `matrix[i][j] = 1`, a square of side `k` ending at (i, j) exists iff squares of side `k - 1` end at (i-1, j), (i, j-1), AND (i-1, j-1). So:

```
dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
```

**Why this counts ALL squares (not just biggest):**

If `dp[i][j] = 3`, then (i, j) is bottom-right of:
- One 1×1 square
- One 2×2 square
- One 3×3 square

That's exactly **3 squares ending at (i, j)**. So `dp[i][j]` gives **both the largest side AND the count** of squares ending there.

**Sum all `dp[i][j]` → total square count.**

Same insight as **LC 221 (Maximal Square)** — but here we *sum* instead of *max*. Two problems sharing a recurrence with different aggregations.

---

## LAYER 1: Line-by-Line Explanation

### Editorial — 2D DP with padded grid

```python
class Solution:
    def countSquares(self, matrix: List[List[int]]) -> int:
        row, col = len(matrix), len(matrix[0])
        
        # dp[i+1][j+1] = side of largest all-ones square ending at matrix[i][j]
        # Border row/column of zeros (padding) → avoids out-of-bounds checks
        dp = [[0] * (col + 1) for _ in range(row + 1)]
        ans = 0
        
        for i in range(row):
            for j in range(col):
                if matrix[i][j]:                        # Only 1-cells can end a square
                    # min of three neighbors + 1
                    # Padding makes edges naturally min=0 → dp=1 (just this cell)
                    dp[i + 1][j + 1] = min(dp[i][j + 1], dp[i + 1][j], dp[i][j]) + 1
                    
                    # Each cell contributes its dp value to total
                    # dp = k means k squares end here
                    ans += dp[i + 1][j + 1]
        
        return ans
```

**Why padding (`row + 1`, `col + 1`):**

Without padding, you'd need:
```python
if i == 0 or j == 0:
    dp[i][j] = matrix[i][j]
else:
    dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
```

With padding, boundaries handled automatically: `dp[0][*]` and `dp[*][0]` are zero, so `min(0, 0, 0) + 1 = 1` works at edges. **The "ghost row/column" trick** — widely used in grid DPs.

**Coordinate mapping:**
- Loop `i, j` index `matrix`
- DP uses `dp[i+1][j+1]` for `matrix[i][j]`
- `dp[i][j+1]` = neighbor above
- `dp[i+1][j]` = neighbor left
- `dp[i][j]` = neighbor up-left (diagonal)

### In-place version (O(1) extra space — destroys input)

```python
class Solution:
    def countSquares(self, matrix: List[List[int]]) -> int:
        row, col = len(matrix), len(matrix[0])
        ans = 0
        
        for i in range(row):
            for j in range(col):
                # First row/column: only 1×1 possible (or 0)
                # matrix[i][j] already 0 or 1 — exactly right starting value
                if matrix[i][j] and i > 0 and j > 0:
                    # Update in place — neighbors already updated
                    matrix[i][j] = min(matrix[i-1][j], matrix[i][j-1], matrix[i-1][j-1]) + 1
                
                ans += matrix[i][j]
        
        return ans
```

**Why in-place works:** When updating `matrix[i][j]`, only read from (i-1, j), (i, j-1), (i-1, j-1) — all processed before in row-major order. Holds correct DP values.

### Hint-based approach (O(n³) with prefix sums)

```python
class Solution:
    def countSquares(self, matrix: List[List[int]]) -> int:
        row, col = len(matrix), len(matrix[0])
        
        # 2D prefix sum
        pre = [[0] * (col + 1) for _ in range(row + 1)]
        for i in range(row):
            for j in range(col):
                pre[i+1][j+1] = pre[i][j+1] + pre[i+1][j] - pre[i][j] + matrix[i][j]
        
        def rect_sum(r1, c1, r2, c2):
            return pre[r2+1][c2+1] - pre[r1][c2+1] - pre[r2+1][c1] + pre[r1][c1]
        
        ans = 0
        for i in range(row):
            for j in range(col):
                max_k = min(row - i, col - j)
                for k in range(1, max_k + 1):
                    if rect_sum(i, j, i + k - 1, j + k - 1) == k * k:
                        ans += 1
                    else:
                        break       # Larger k won't be all-ones either
        return ans
```

Hint-based but O(n³). DP version is O(n²). Both within constraints (n ≤ 300).

---

## LAYER 2: Worked Examples

### Example 1: `[[0,1,1,1], [1,1,1,1], [0,1,1,1]]` → 15

Building the DP table:

| i,j | matrix | dp neighbors (top, left, top-left) | dp value |
|-----|--------|--------------------------------------|----------|
| 0,0 | 0 | — | 0 |
| 0,1 | 1 | (0, 0, 0) | 1 |
| 0,2 | 1 | (0, 1, 0) | 1 |
| 0,3 | 1 | (0, 1, 0) | 1 |
| 1,0 | 1 | (0, 0, 0) | 1 |
| 1,1 | 1 | (1, 1, **0**) | **1** |
| 1,2 | 1 | (1, 1, 1) | 2 |
| 1,3 | 1 | (1, 2, 1) | 2 |
| 2,0 | 0 | — | 0 |
| 2,1 | 1 | (1, 0, 1) | 1 |
| 2,2 | 1 | (2, 1, 1) | 2 |
| 2,3 | 1 | (2, 2, 2) | 3 |

Final dp:
```
. 1 1 1
1 1 2 2
. 1 2 3
```

Sum: (0+1+1+1) + (1+1+2+2) + (0+1+2+3) = 3 + 6 + 6 = **15** ✓

**Verification:** 10 (side-1) + 4 (side-2) + 1 (side-3) = 15.

The 3×3 square is at top-left (0,1), rows 0-2 cols 1-3:
```
1 1 1
1 1 1
1 1 1
```
All ones ✓.

### Example 2: `[[1,0,1], [1,1,0], [1,1,0]]` → 7

| i,j | matrix | dp value |
|-----|--------|----------|
| 0,0 | 1 | 1 |
| 0,1 | 0 | 0 |
| 0,2 | 1 | 1 |
| 1,0 | 1 | 1 |
| 1,1 | 1 | min(0, 1, 1) + 1 = 1 |
| 1,2 | 0 | 0 |
| 2,0 | 1 | 1 |
| 2,1 | 1 | min(1, 1, 1) + 1 = 2 |
| 2,2 | 0 | 0 |

Sum: 1+0+1 + 1+1+0 + 1+2+0 = **7** ✓

### Edge cases

- **All zeros:** dp all 0; sum = 0
- **All ones (m × n):** DP gives full count by side
- **1×1 with value 1:** dp = [[1]]; answer = 1
- **1×n strip of ones:** dp = [[1]*n]; answer = n
- **m, n ≤ 300:** 90K cells × O(1) = 90K ops — instant

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **2D DP (editorial)** | **O(m × n)** | **O(m × n)** | **Standard** |
| In-place DP | O(m × n) | O(1) extra | Destroys input |
| 1D rolling DP | O(m × n) | O(n) | Only previous row |
| Prefix sum + brute | O(min(m,n) × m × n) | O(m × n) | Hint approach |

**The "DP cell = answer per cell" trick:**

Each `dp[i][j]` directly tells you "squares ending here." So `sum(dp)` = answer. Compare to LC 221 where `dp[i][j]` is same recurrence but you `max()` over the table.

**One recurrence, two problems:**

| Problem | Aggregation | Result |
|---------|-------------|--------|
| LC 221 Maximal Square | `max(dp)` over all cells | Area of largest square |
| **LC 1277** | **`sum(dp)`** | **Count of all squares** |

When you see a per-cell recurrence, ask which aggregation is interesting:
- `max` → largest instance
- `sum` → total count
- `count(dp[i][j] > k)` → how many exceed threshold

**Why recurrence works (intuitive proof):**

For square of side `k` ending at (i, j):
1. Cell (i, j) = 1 ✓
2. Three sub-squares of side `k - 1` ending at (i-1, j), (i, j-1), (i-1, j-1) must exist

If ANY smaller, can't form side k. `dp[i][j]` bounded by min of three neighbors, plus 1.

**The "ghost row/column" padding pattern:**

Eliminates boundary special-cases. Seen in:
- Prefix sum problems
- 2D DP (this, LC 64, LC 62)
- Grid traversal

Memorize: `dp = [[0] * (cols + 1) for _ in range(rows + 1)]`, index `dp[i+1][j+1]`.

**Connection to other patterns:**

- **LC 221 Maximal Square:** Same recurrence, max
- **LC 64 Minimum Path Sum:** 2D DP with sum
- **LC 62 Unique Paths:** 2D DP, two neighbors
- **LC 1480 Running Sum:** 1D prefix (simpler cousin)
- **LC 2574 Left/Right Sum Differences:** 1D two-pass

You've drilled enough 2D thinking (LC 885, 1572, 2373) that this clicks.

---

## LAYER 4: Interview Variations

• **Maximal Square (LC 221):** Same recurrence, return `max(dp)²`. Classic warm-up.
• **Largest Square of Zeros:** Same recurrence on matrix == 0.
• **Maximal Rectangle (LC 85):** Much harder — histogram + monotonic stack per row.
• **Count Rectangles of All Ones:** Far more complex; no simple recurrence.
• **K-th largest square:** Compute dp, sort, take K-th.
• **Total area covered by all squares:** Sum `k²` per square ended.
• **At most K zeros allowed:** Different recurrence; sliding window per square.
• **Up to MAX_SIDE size:** Cap dp values.
• **3D extension (cubes in 3D grid):** Same recurrence, four neighbors.
• **Streaming (rows arrive):** Maintain dp row by row; O(n) per new row.
• **Find actual squares:** Track origins alongside dp.
• **Sparse matrix:** Use different data structure; only ones matter.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Editorial DP with padding
class Solution:
    def countSquares(self, matrix: List[List[int]]) -> int:
        row, col = len(matrix), len(matrix[0])
        dp = [[0] * (col + 1) for _ in range(row + 1)]
        ans = 0
        for i in range(row):
            for j in range(col):
                if matrix[i][j]:
                    dp[i + 1][j + 1] = min(dp[i][j + 1], dp[i + 1][j], dp[i][j]) + 1
                    ans += dp[i + 1][j + 1]
        return ans
```

**In-place (O(1) extra):**
```python
def countSquares(matrix):
    row, col = len(matrix), len(matrix[0])
    ans = 0
    for i in range(row):
        for j in range(col):
            if matrix[i][j] and i > 0 and j > 0:
                matrix[i][j] = min(matrix[i-1][j], matrix[i][j-1], matrix[i-1][j-1]) + 1
            ans += matrix[i][j]
    return ans
```

**1D rolling DP:**
```python
def countSquares(matrix):
    row, col = len(matrix), len(matrix[0])
    prev = [0] * (col + 1)
    ans = 0
    for i in range(row):
        curr = [0] * (col + 1)
        for j in range(col):
            if matrix[i][j]:
                curr[j + 1] = min(prev[j + 1], curr[j], prev[j]) + 1
                ans += curr[j + 1]
        prev = curr
    return ans
```

**Compact (in-place, exploits matrix=1 starting value):**
```python
class Solution:
    def countSquares(self, matrix):
        for i in range(len(matrix)):
            for j in range(len(matrix[0])):
                if matrix[i][j] and i and j:
                    matrix[i][j] += min(matrix[i-1][j], matrix[i][j-1], matrix[i-1][j-1])
        return sum(sum(row) for row in matrix)
```

Beautiful but compresses the algorithm.

**Hint-based (prefix sum + brute, O(n³)):**
```python
def countSquares(matrix):
    row, col = len(matrix), len(matrix[0])
    pre = [[0] * (col + 1) for _ in range(row + 1)]
    for i in range(row):
        for j in range(col):
            pre[i+1][j+1] = pre[i][j+1] + pre[i+1][j] - pre[i][j] + matrix[i][j]
    
    ans = 0
    for i in range(row):
        for j in range(col):
            for k in range(1, min(row - i, col - j) + 1):
                r2, c2 = i + k - 1, j + k - 1
                total = pre[r2+1][c2+1] - pre[i][c2+1] - pre[r2+1][j] + pre[i][j]
                if total == k * k:
                    ans += 1
                else:
                    break
    return ans
```

---

**Time:** O(m × n) | **Space:** O(m × n) DP, O(1) in-place

**Pattern flag:** "Count grid sub-structures with property P" → **2D DP** where `dp[i][j]` = "largest valid sub-structure ENDING at (i, j)." For squares: `dp[i][j] = min(three_neighbors) + 1`. Then **`sum(dp)` counts ALL** (each cell contributes its own count); **`max(dp)²` gives largest** (LC 221).

One of the most elegant 2D DP recurrences in interview prep. Same recurrence in LC 221 (max), LC 1277 (sum), LC 1727 (related). The **"DP cell as direct answer per cell"** is the meta-insight: when each `dp[i][j]` independently contributes to the total, aggregation is a sum (or max).

⭐ **First 2D Grid DP** on the log.
