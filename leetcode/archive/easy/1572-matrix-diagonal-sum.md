# 1572. Matrix Diagonal Sum

**Difficulty:** Easy
**Pattern:** Matrix / Index Manipulation
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

Sum the two diagonals of a square matrix:
- **Primary diagonal:** top-left to bottom-right → cells where `row == col`, i.e., `mat[i][i]`
- **Secondary diagonal:** top-right to bottom-left → cells where `row + col == n - 1`, i.e., `mat[i][n-1-i]`

If `n` is odd, the two diagonals share the **center cell** `mat[n//2][n//2]`. Count it once.

**Key insight:** Walk one loop from `i = 0` to `n-1`, add both diagonal values at each step. If `n` is odd, the center gets double-counted — subtract it once at the end. O(n) time.

---

## LAYER 1: Line-by-Line Explanation

**Editorial — Single pass with overlap correction:**

```python
class Solution:
    def diagonalSum(self, mat: List[List[int]]) -> int:
        n = len(mat)
        ans = 0
        for i in range(n):
            ans += mat[i][i]              # Primary diagonal cell
            ans += mat[n - 1 - i][i]      # Secondary diagonal cell
        # If n is odd, the center cell mat[n//2][n//2] was added twice
        if n % 2 != 0:
            ans -= mat[n // 2][n // 2]
        return ans
```

**Decoding the indices:**

For an n×n matrix, picture an example with n=3:

```
(0,0)  (0,1)  (0,2)        primary: (0,0), (1,1), (2,2) → mat[i][i]
(1,0)  (1,1)  (1,2)        secondary: (0,2), (1,1), (2,0) → mat[i][n-1-i]
(2,0)  (2,1)  (2,2)
```

- **Primary at row i:** column = i → `mat[i][i]`
- **Secondary at row i:** column = `n-1-i` → as i goes 0..n-1, column goes n-1..0

The editorial uses `mat[n-1-i][i]` for secondary, not `mat[i][n-1-i]`. Both are equivalent — same set of cells, just traversed in opposite order. Sum is identical.

**Why the odd-n correction:**

When `n` is odd, the cell at `(n//2, n//2)` lies on BOTH diagonals:
- For primary at `i = n//2`: cell `(n//2, n//2)` ✓
- For secondary at `i = n//2`: cell `(n-1-n//2, n//2) = (n//2, n//2)` ✓ (when n is odd, `n-1-n//2 == n//2`)

The loop adds it twice. Subtracting it once at the end fixes the double-count.

When `n` is even, the diagonals don't intersect (they pass between cells), so no correction needed.

**Pythonic one-liner (uses set to dedupe overlap automatically):**

```python
class Solution:
    def diagonalSum(self, mat: List[List[int]]) -> int:
        n = len(mat)
        cells = {(i, i) for i in range(n)} | {(i, n - 1 - i) for i in range(n)}
        return sum(mat[r][c] for r, c in cells)
```

The set union deduplicates the center automatically. Slightly slower due to set overhead, but no special-case logic for odd `n`.

---

## LAYER 2: Worked Examples

**Example 1: `mat = [[1,2,3],[4,5,6],[7,8,9]]` → 25**

n = 3 (odd).

| i | mat[i][i] (primary) | mat[n-1-i][i] (secondary) |
|---|---------------------|----------------------------|
| 0 | mat[0][0] = 1       | mat[2][0] = 7              |
| 1 | mat[1][1] = 5       | mat[1][1] = 5              |
| 2 | mat[2][2] = 9       | mat[0][2] = 3              |

Sum from loop: 1 + 7 + 5 + 5 + 9 + 3 = 30

n is odd → subtract `mat[1][1] = 5`: 30 − 5 = **25** ✓

Check against problem: 1 + 5 + 9 (primary) + 3 + 7 (secondary, excluding center) = 25 ✓

**Example 2: `mat = [[1,1,1,1],[1,1,1,1],[1,1,1,1],[1,1,1,1]]` → 8**

n = 4 (even). All values are 1.

Primary diagonal: 4 cells × 1 = 4
Secondary diagonal: 4 cells × 1 = 4
Total: 8. No overlap correction (n even). ✓

**Example 3: `mat = [[5]]` → 5**

n = 1 (odd).

Loop iteration i=0:
- Primary: mat[0][0] = 5
- Secondary: mat[0][0] = 5
- Sum so far: 10

n odd → subtract mat[0][0] = 5: 10 − 5 = **5** ✓

The 1×1 case is a perfect sanity check — the single cell is on both diagonals, so it must be counted once.

**Walkthrough — verify n=4 has no overlap:**

For n=4, primary cells: (0,0), (1,1), (2,2), (3,3)
Secondary cells (using `mat[i][n-1-i]`): (0,3), (1,2), (2,1), (3,0)

No cell appears in both sets — even-n diagonals don't share any cell. Center of a 4×4 falls between cells (at "(1.5, 1.5)"), not on any cell. ✓

**5×5 example (extra trace for clarity):**

`mat = [[1,2,3,4,5],[6,7,8,9,10],[11,12,13,14,15],[16,17,18,19,20],[21,22,23,24,25]]`

n = 5 (odd).

| i | mat[i][i] | mat[4-i][i] |
|---|-----------|-------------|
| 0 | 1 | 21 |
| 1 | 7 | 17 |
| 2 | 13 | 13 ← center, double-counted |
| 3 | 19 | 9 |
| 4 | 25 | 5 |

Sum: 1 + 21 + 7 + 17 + 13 + 13 + 19 + 9 + 25 + 5 = 130
Subtract center: 130 − 13 = **117**

Verify: primary (1+7+13+19+25=65) + secondary excluding center (21+17+9+5=52) = 117 ✓

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Single loop + correction (editorial) | O(n) | O(1) | **Optimal, idiomatic** |
| Two separate loops | O(n) | O(1) | Same complexity, slightly more code |
| Set-based dedup | O(n) | O(n) for set | Cleaner logic, slightly slower |
| Nested loop scanning whole matrix | O(n²) | O(1) | Wasteful for diagonals |

**The clever index pattern — internalize this:**

For an n×n matrix:
- **Primary diagonal**: cells (i, i) for i in [0, n)
- **Secondary diagonal**: cells (i, n-1-i) for i in [0, n)
- **Anti-diagonal alternative**: cells (n-1-i, i) — same cells, reversed traversal

These two patterns (`(i,i)` and `(i, n-1-i)`) come up in dozens of matrix problems. Memorize them.

**Why the odd/even split matters:**

```
n=3 (odd):                  n=4 (even):
P . S                       P . . S
. X .                       . P S .       ← P=primary, S=secondary
S . P                       . S P .       ← X = overlap (only when odd)
                            S . . P
```

Odd: diagonals meet at exactly one cell (the geometric center).
Even: diagonals don't share any cell — they pass through the "gaps" between cells.

**The general formula for whether two diagonals overlap on a cell:**

Cell (r, c) is on primary iff `r == c`. On secondary iff `r + c == n - 1`. Both iff `r == c` AND `2r == n - 1`. The latter has an integer solution only when `n - 1` is even, i.e., when n is odd. In that case, `r = (n-1)/2 = n//2`. ✓

**Why not just use the set-based approach in production?**

You could — it's clean and avoids the conditional. But the loop+correction is faster (O(1) extra space, no hashing), and the `if n % 2` line teaches a useful idea: when an algorithm naturally double-counts in a special case, just subtract the duplicate once. This pattern shows up in inclusion-exclusion problems too.

---

## LAYER 4: Interview Variations

• **Sum of any single diagonal** (only primary, or only secondary):
  Drop the other loop and the correction.

• **Sum of all four "corner" cells:**
  `mat[0][0] + mat[0][n-1] + mat[n-1][0] + mat[n-1][n-1]`. Watch out for n=1 (all four collapse to one cell).

• **Sum of the kth diagonal above the main diagonal:**
  `sum(mat[i][i+k] for i in range(n-k))`. For below the diagonal, `sum(mat[i+k][i] for i in range(n-k))`.

• **Sum of all anti-diagonals (group cells by `r + c`):**
  Returns a list of n + n - 1 sums. Use `defaultdict(int)` keyed by `r + c`.

• **Sum of cells NOT on either diagonal** (LC 2148 variant idea):
  Sum entire matrix minus diagonal sum.

• **Sum of cells equidistant from both diagonals** (the "X" shape's interior):
  Geometric filter — keep cells where neither `r == c` nor `r + c == n - 1`.

• **Non-square matrix diagonals:**
  Primary still makes sense up to `min(rows, cols)`. Secondary needs careful index bounds.

• **3D tensor diagonals:**
  Cells where all three indices match. Generalize the pattern.

• **Sum a specific diagonal of a sparse matrix represented as `dict[(r,c)] = value`:**
  `sum(d.get((i, i), 0) for i in range(n))`.

• **Find the maximum element on either diagonal:**
  Same loop pattern, replace `+=` with `max(...)`.

• **Check if both diagonals have equal sums** (LC 1582 variant):
  Compute each separately, compare.

• **Diagonal traversal output as a 1D list** (LC 498 Diagonal Traverse):
  Different problem — output cells in zig-zag order. Uses a similar index understanding but a more elaborate algorithm.

• **Spiral order traversal** (LC 54):
  Doesn't use diagonals directly but uses similar matrix-walking intuition.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Editorial style
class Solution:
    def diagonalSum(self, mat: List[List[int]]) -> int:
        n = len(mat)
        total = 0
        for i in range(n):
            total += mat[i][i] + mat[n - 1 - i][i]
        if n % 2:
            total -= mat[n // 2][n // 2]
        return total
```

**One-liner with conditional:**
```python
def diagonalSum(mat):
    n = len(mat)
    s = sum(mat[i][i] + mat[i][n-1-i] for i in range(n))
    return s - (mat[n//2][n//2] if n % 2 else 0)
```

**Set-based dedup (cleanest logic):**
```python
def diagonalSum(mat):
    n = len(mat)
    cells = {(i, i) for i in range(n)} | {(i, n-1-i) for i in range(n)}
    return sum(mat[r][c] for r, c in cells)
```

**Two-loop version (most readable):**
```python
def diagonalSum(mat):
    n = len(mat)
    primary = sum(mat[i][i] for i in range(n))
    secondary = sum(mat[i][n-1-i] for i in range(n))
    overlap = mat[n//2][n//2] if n % 2 else 0
    return primary + secondary - overlap
```

---

**Time:** O(n) | **Space:** O(1)

**Pattern flag:** Matrix diagonal problems hinge on two index formulas: **`(i, i)` for primary** and **`(i, n-1-i)` for secondary**. Whenever a problem mentions "diagonal," reach for these first. The odd/even overlap correction is the classic gotcha — odd-n diagonals share the center cell, even-n diagonals don't.
