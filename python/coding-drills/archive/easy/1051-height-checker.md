# 1051. Height Checker

**Difficulty:** Easy
**Pattern:** Sorting / Comparison
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

Compare each position of `heights` against what it *should* be in sorted order. Count positions that don't match.

**Key insight:** The "expected" array is just `heights` sorted ascending. You don't need any clever algorithm — sort a copy, zip with original, count mismatches. Two lines.

**The editorial uses bubble sort** to teach the sorting algorithm, but in any real solution you'd use Python's built-in `sorted()` (Timsort, O(n log n)). For LC's constraints (n ≤ 100, values ≤ 100), even bubble sort's O(n²) flies. With those bounded values, **counting sort gives O(n)** — which is the truly elegant approach worth knowing.

---

## LAYER 1: Line-by-Line Explanation

**Pythonic standard sort (the one you'd actually write):**

```python
class Solution:
    def heightChecker(self, heights: List[int]) -> int:
        expected = sorted(heights)
        return sum(h != e for h, e in zip(heights, expected))
```

- `sorted(heights)` — returns a new sorted list, doesn't mutate `heights`
- `zip(heights, expected)` — pairs up corresponding elements `(h0, e0), (h1, e1), ...`
- `h != e` — boolean, becomes `1` when True and `0` when False inside `sum`
- `sum(...)` — total count of mismatches

**Editorial — Bubble sort (instructional, but slow):**

```python
class Solution:
    def heightChecker(self, heights: List[int]) -> int:
        def bubble_sort():
            n = len(sorted_heights)
            for i in range(n - 1):
                for j in range(n - i - 1):
                    if sorted_heights[j] > sorted_heights[j + 1]:
                        sorted_heights[j], sorted_heights[j + 1] = (
                            sorted_heights[j + 1],
                            sorted_heights[j],
                        )

        sorted_heights = heights[:]      # Copy to avoid mutating input
        bubble_sort()
        count = 0
        for i in range(len(sorted_heights)):
            if heights[i] != sorted_heights[i]:
                count += 1
        return count
```

**How bubble sort works:** Repeatedly walk the array, swapping adjacent out-of-order pairs. After pass `i`, the largest `i+1` elements are in their final positions at the end (they "bubble up"). The outer loop runs n-1 times, the inner shrinks each pass.

**Why `heights[:]` matters:** Without the copy, `sorted_heights = heights` would make both names point to the same list. Sorting would mutate the input — losing the original order we need to compare against.

**Counting sort version (O(n) — the optimal solution):**

```python
class Solution:
    def heightChecker(self, heights: List[int]) -> int:
        count = [0] * 101                       # heights are 1..100
        for h in heights:
            count[h] += 1

        # Walk sorted order via counting array, compare to heights[i]
        mismatch = 0
        i = 0
        for h in range(101):
            while count[h] > 0:
                if heights[i] != h:
                    mismatch += 1
                i += 1
                count[h] -= 1
        return mismatch
```

This achieves O(n + R) where R = 101 is the value range — effectively O(n) since R is a constant.

---

## LAYER 2: Worked Examples

**Example 1: `heights = [1,1,4,2,1,3]` → 3**

`expected = sorted(heights) = [1,1,1,2,3,4]`

| i | heights[i] | expected[i] | match? |
|---|-----------|-------------|--------|
| 0 | 1 | 1 | ✅ |
| 1 | 1 | 1 | ✅ |
| 2 | 4 | 1 | ❌ |
| 3 | 2 | 2 | ✅ |
| 4 | 1 | 3 | ❌ |
| 5 | 3 | 4 | ❌ |

Mismatches: 3 ✓

**Example 2: `heights = [5,1,2,3,4]` → 5**

`expected = [1,2,3,4,5]`

Every index differs — `5 ≠ 1, 1 ≠ 2, 2 ≠ 3, 3 ≠ 4, 4 ≠ 5` → 5 mismatches ✓

**Example 3: `heights = [1,2,3,4,5]` → 0**

Already sorted, `expected = heights`, 0 mismatches ✓

**Bubble sort trace on `heights = [1,1,4,2,1,3]`:**

Pass 1 (i=0, inner j=0..4):
- j=0: [1,1] ok
- j=1: [1,4] ok
- j=2: [4,2] swap → [1,1,2,4,1,3]
- j=3: [4,1] swap → [1,1,2,1,4,3]
- j=4: [4,3] swap → [1,1,2,1,3,4]

Pass 2 (i=1, j=0..3):
- j=0: [1,1] ok
- j=1: [1,2] ok
- j=2: [2,1] swap → [1,1,1,2,3,4]
- j=3: [2,3] ok

Pass 3 (i=2, j=0..2):
- All comparisons already in order

The array stabilizes at `[1,1,1,2,3,4]` — matches `sorted(heights)` ✓

**Counting sort trace on `heights = [1,1,4,2,1,3]`:**

Build `count`: `count[1]=3, count[2]=1, count[3]=1, count[4]=1`

Walk values 1..100:
| h | count[h] | i | heights[i] | match? | mismatch |
|---|----------|---|-----------|--------|----------|
| 1 | 3 | 0 | 1 | ✅ | 0 |
| 1 | 2 | 1 | 1 | ✅ | 0 |
| 1 | 1 | 2 | 4 | ❌ | 1 |
| 2 | 1 | 3 | 2 | ✅ | 1 |
| 3 | 1 | 4 | 1 | ❌ | 2 |
| 4 | 1 | 5 | 3 | ❌ | 3 |

Result: 3 ✓

**Edge cases:**
- Single element: `heights = [7]` → already sorted → 0
- All same: `heights = [5,5,5]` → already sorted (ties don't break order) → 0
- Reverse sorted: `heights = [4,3,2,1]` → all 4 differ → 4
- Two elements swapped: `heights = [1,3,2,4]` → 2 mismatches (positions 1 and 2)

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Bubble sort (editorial) | O(n²) | O(n) for copy | Slow but instructive |
| Built-in `sorted()` (Timsort) | O(n log n) | O(n) | **Default, Pythonic** |
| Counting sort | O(n + R) | O(R) | **Optimal when values bounded** |
| Counter-based comparison | O(n) | O(n) | Different angle, doesn't preserve order info |

**Why this problem is really about understanding sorting comparison:**

Many beginners overthink this: "Do I need to find the longest non-decreasing subsequence? Some O(n) trick?" No — the problem literally says "compare to sorted order." Just sort and compare.

**Why the editorial uses bubble sort:** Pedagogical. It's teaching the sorting concept from scratch. In an interview you'd never use bubble sort — you'd say "I'll use Python's sorted, which is Timsort, O(n log n)" or "Since heights are bounded 1..100, I can use counting sort for O(n)."

**Counting sort's bigger lesson:** Whenever inputs are integers in a small bounded range, counting sort beats comparison sorts. The trade-off is space O(R). Here R = 100, so it's free.

**Pitfall — what if you did `heights.sort()`?**

```python
# BUG: mutates the input!
def heightChecker(heights):
    original = heights[:]
    heights.sort()
    return sum(o != h for o, h in zip(original, heights))
```

This works *functionally* but mutates the caller's list. In LeetCode it usually doesn't matter, but in production it's a side-effect bug. Always prefer `sorted()` (returns new list) over `.sort()` (mutates in place) unless you specifically want in-place behavior.

**Counter-based alternative (different idea):**

```python
from collections import Counter
def heightChecker(heights):
    return sum((Counter(heights) - Counter(sorted_h)).values())
```

This counts how many "extra" elements appear in `heights` vs the sorted order. **Subtle:** it gives the right answer but for a different reason — and it doesn't generalize well. Stick with the zip-compare approach.

---

## LAYER 4: Interview Variations

• **Return the indices of mismatches, not just the count:**
  ```python
  return [i for i, (h, e) in enumerate(zip(heights, sorted(heights))) if h != e]
  ```

• **Count mismatches against sorted DESCENDING order:**
  `sum(h != e for h, e in zip(heights, sorted(heights, reverse=True)))`.

• **Count mismatches when sorted by a custom key (e.g., by height % 10):**
  Replace `sorted(heights)` with `sorted(heights, key=lambda x: x % 10)`.

• **Minimum number of adjacent swaps to sort `heights`** (bubble sort swap count):
  Number of inversions — count pairs `(i, j)` with `i < j` but `heights[i] > heights[j]`. Different problem; use merge sort O(n log n) or BIT.

• **Minimum number of arbitrary swaps to sort:**
  Different from mismatches — needs cycle decomposition of the permutation. Mismatch count is an upper bound but not tight.

• **Stream version (heights arrive one at a time):**
  Maintain a sorted multiset (SortedList). On query, scan and compare.

• **Largest "out-of-place" run (longest streak of consecutive mismatches):**
  Walk once, track current streak length.

• **Heights have arbitrary unbounded values (no R = 100 constraint):**
  Counting sort is no longer free. Use `sorted()` for O(n log n).

• **Find positions that DO match (complement):**
  `n - mismatches`, or `sum(h == e for h, e in zip(heights, sorted(heights)))`.

• **Multiple snapshots — compare heights against historical orderings:**
  Pre-sort each historical version; compare with `zip` each time.

• **Allowed K swaps — return whether sorted order is reachable:**
  K ≥ inversions count is sufficient if swaps can be arbitrary. Different problem.

• **What if heights array is huge (10^9 elements) and you only have streaming access?**
  Can't sort in memory. Use external merge sort, or sample-and-estimate.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic, O(n log n)
class Solution:
    def heightChecker(self, heights: List[int]) -> int:
        return sum(h != e for h, e in zip(heights, sorted(heights)))
```

**Two-line variant (more readable):**
```python
def heightChecker(heights):
    expected = sorted(heights)
    return sum(h != e for h, e in zip(heights, expected))
```

**Counting sort — O(n), optimal for bounded values:**
```python
def heightChecker(heights):
    count = [0] * 101
    for h in heights:
        count[h] += 1
    mismatch, i = 0, 0
    for h in range(101):
        while count[h] > 0:
            if heights[i] != h:
                mismatch += 1
            i += 1
            count[h] -= 1
    return mismatch
```

**Editorial — Bubble sort:**
```python
def heightChecker(heights):
    arr = heights[:]
    n = len(arr)
    for i in range(n - 1):
        for j in range(n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
    return sum(1 for i in range(n) if heights[i] != arr[i])
```

---

**Time:** O(n log n) standard, O(n + R) with counting sort
**Space:** O(n) for the sorted copy, or O(R) for the count array

**Pattern flag:** "Compare actual order to ideal order" → sort a copy and zip. Whenever you see bounded small integer values (1..100, 0..255), reach for **counting sort** for the O(n) win.
