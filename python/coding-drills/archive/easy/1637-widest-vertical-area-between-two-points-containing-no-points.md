# 1637. Widest Vertical Area Between Two Points Containing No Points

**Difficulty:** Easy
**Pattern:** Sorting / Dimensional Reduction / Adjacent Pair Aggregation
**Date Solved:** 2026-06-09
**Status:** ✅

---

## Understanding the Goal

Given `n` points on a 2D plane, find the widest vertical strip you can draw between two points such that no points fall **inside** the strip (points on edges are fine).

**Key insight:** Y-coordinates are completely irrelevant. A vertical strip is determined entirely by x-coordinates. The strip needs no points strictly between its left and right edges.

**Reformulation:** Given x-coordinates, find the largest gap between any two consecutive values when sorted.

This is the **adjacent-pair aggregation** template again — 6th encounter (LC 3110, 3173, 3846, 2574, 1769). Sort x's, take `max(x[i+1] - x[i])` over adjacent pairs.

---

## LAYER 1: Line-by-Line Explanation

### One-liner with zip (preferred)

```python
class Solution:
    def maxWidthOfVerticalArea(self, points: List[List[int]]) -> int:
        # Step 1: extract x-coordinates, sort ascending
        # Don't care about y at all — only horizontal gaps matter
        xs = sorted(p[0] for p in points)
        
        # Step 2: max gap between adjacent x-values in sorted order
        # zip(xs, xs[1:]) yields (x[0],x[1]), (x[1],x[2]), ... — adjacent pairs
        return max(b - a for a, b in zip(xs, xs[1:]))
```

**Why y-coordinates are ignored:** A vertical strip is infinite in y. A point at any y inside the x-range blocks the strip. Drop the y's.

**Why sort works:** After sorting, consecutive x-values are the only candidate pairs for the widest "no points between" gap. Any non-consecutive pair has at least one point between them.

**Why `b - a` (not `abs`):** Sorted ascending means `b ≥ a` always.

### Explicit loop

```python
class Solution:
    def maxWidthOfVerticalArea(self, points: List[List[int]]) -> int:
        xs = sorted(p[0] for p in points)
        max_gap = 0
        for i in range(len(xs) - 1):
            gap = xs[i + 1] - xs[i]
            if gap > max_gap:
                max_gap = gap
        return max_gap
```

### Sort points directly

```python
class Solution:
    def maxWidthOfVerticalArea(self, points: List[List[int]]) -> int:
        points.sort()                       # Default sort by first element (x)
        return max(points[i+1][0] - points[i][0] for i in range(len(points) - 1))
```

### With itertools.pairwise (Python 3.10+)

```python
from itertools import pairwise

class Solution:
    def maxWidthOfVerticalArea(self, points: List[List[int]]) -> int:
        xs = sorted(p[0] for p in points)
        return max(b - a for a, b in pairwise(xs))
```

`pairwise(xs)` is the semantic replacement for `zip(xs, xs[1:])`.

---

## LAYER 2: Worked Examples

### Example 1: `points = [[8,7], [9,9], [7,4], [9,7]]` → 1

Extract x's: `[8, 9, 7, 9]`
Sort: `[7, 8, 9, 9]`

| pair | gap |
|------|-----|
| (7, 8) | 1 |
| (8, 9) | 1 |
| (9, 9) | 0 |

Max gap: **1** ✓

### Example 2: `points = [[3,1], [9,0], [1,0], [1,4], [5,3], [8,8]]` → 3

Extract x's: `[3, 9, 1, 1, 5, 8]`
Sort: `[1, 1, 3, 5, 8, 9]`

| pair | gap |
|------|-----|
| (1, 1) | 0 |
| (1, 3) | 2 |
| (3, 5) | 2 |
| (5, 8) | **3** |
| (8, 9) | 1 |

Max: **3** ✓ (gap between x=5 and x=8)

### Edge cases

- **Min n=2:** Single gap; that's the answer
- **All same x:** All gaps are 0 → answer 0
- **Duplicate x-values:** Gap 0 (points overlap horizontally)
- **Max constraint n=10⁵:** Sort is O(n log n) ≈ 1.7M ops → fast

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Sort + adjacent gap | O(n log n) | O(n) | **Default** |
| In-place sort + zip | O(n log n) | O(1) extra | Same big-O |
| Counting sort | O(n + R) | O(R) | x ≤ 10⁹ — too big here |
| Brute force pairs | O(n²) | O(1) | Wasteful |

**Why "Easy" but pattern-rich:**

Tests:
1. **Dimensional reduction** — recognize y doesn't matter
2. **Sort + adjacent-pair** — universal "widest gap" template
3. **Pythonic generators** — `zip(xs, xs[1:])` is idiomatic

**The "widest/narrowest gap" pattern (memorize):**

> "Find widest/narrowest distance between any two elements satisfying P" → **sort by relevant dimension, scan adjacent pairs**.

Sorting reduces the "any pair" search space to "consecutive pair" search space — no non-consecutive pair can be the widest (some consecutive pair fits inside it).

**Connection to adjacent-pair template:**

| Problem | seq | op | aggregation |
|---------|-----|-----|-------------|
| LC 3173 | nums | `\|` (OR) | list |
| LC 3110 | string | `abs(ord(a) - ord(b))` | sum |
| LC 3846 | typed path | Manhattan distance | sum |
| LC 1769 | balls | running cursor dist | two-pass sum |
| **LC 1637** | **sorted xs** | **`b - a`** | **max** |

---

## LAYER 4: Interview Variations

• **Narrowest gap:** `min` instead of `max`. Returns 0 if duplicates.
• **Return pair achieving max gap:** Track index where max occurs.
• **Widest gap with group constraint:** Sort by x, sweep with group filter.
• **k-th widest gap:** Sort all gaps, take k-th largest.
• **Streaming insertion:** Use `SortedList` from `sortedcontainers`.
• **Online queries with updates:** Balanced BST tracking gaps.
• **2D widest empty rectangle:** Much harder; sweep line + monotonic stack.
• **Closest two points in 2D:** Divide-and-conquer; classic O(n log n).
• **Wrap-around (circular):** Add `xs[0] + period - xs[-1]` as candidate.
• **Widest with at most k points inside (relaxed):** Sliding window.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic one-liner
class Solution:
    def maxWidthOfVerticalArea(self, points: List[List[int]]) -> int:
        xs = sorted(p[0] for p in points)
        return max(b - a for a, b in zip(xs, xs[1:]))
```

**Explicit loop:**
```python
def maxWidthOfVerticalArea(points):
    xs = sorted(p[0] for p in points)
    max_gap = 0
    for i in range(len(xs) - 1):
        gap = xs[i + 1] - xs[i]
        if gap > max_gap:
            max_gap = gap
    return max_gap
```

**Sort points directly:**
```python
def maxWidthOfVerticalArea(points):
    points.sort()
    return max(points[i+1][0] - points[i][0] for i in range(len(points) - 1))
```

**With pairwise:**
```python
from itertools import pairwise

def maxWidthOfVerticalArea(points):
    xs = sorted(p[0] for p in points)
    return max(b - a for a, b in pairwise(xs))
```

---

**Time:** O(n log n) | **Space:** O(n)

**Pattern flag:** "Find widest/narrowest distance between any two elements" → **sort + adjacent-pair scan**. Sorting reduces "any pair" to "consecutive pair." For 2D geometry with vertical/horizontal strip framing, **dimensional reduction** is the unlock: drop the irrelevant axis, then 1D sort. Recognize `op(a, b) for a, b in zip(seq, seq[1:])` instantly — 6+ encounters across bit ops, strings, prefix sums, geometry, digit problems.
