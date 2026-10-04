# 1365. How Many Numbers Are Smaller Than the Current Number

**Difficulty:** Easy
**Pattern:** Sorting / Counting Sort / Prefix Sum
**Date Solved:** 2026-06-09
**Status:** ✅

---

## Understanding the Goal

For each `nums[i]`, count how many other elements are **strictly smaller**. Return as array in input order.

**Three solution levels:**

1. **Brute force (Hint 1):** O(n²) — scan per element. Works for n=500.
2. **Sort + binary search (Hint 2):** O(n log n) — sort once, then `bisect_left`.
3. **Counting sort + prefix sum:** O(n + R) where R = value range. **The prize approach** — constraint `nums[i] ≤ 100` is the giveaway.

When the value range is small and bounded, counting sort beats comparison sort.

---

## LAYER 1: Line-by-Line Explanation

### Brute force (Hint 1)

```python
class Solution:
    def smallerNumbersThanCurrent(self, nums: List[int]) -> List[int]:
        n = len(nums)
        result = [0] * n
        for i in range(n):
            for j in range(n):
                if i != j and nums[j] < nums[i]:
                    result[i] += 1
        return result
```

**Pythonic compression:**
```python
return [sum(x < n for x in nums) for n in nums]
```

(`nums[i] < nums[i]` is always False, so the `i != j` check is unnecessary.)

### Sort + binary search (Hint 2)

```python
from bisect import bisect_left

class Solution:
    def smallerNumbersThanCurrent(self, nums: List[int]) -> List[int]:
        # Sort a COPY (preserve original indices)
        sorted_nums = sorted(nums)
        # For each x, find first occurrence in sorted_nums
        # That index = count of elements strictly less than x
        # Example: sorted=[1,2,2,3,8], for x=2, bisect_left returns 1 → 1 smaller
        return [bisect_left(sorted_nums, x) for x in nums]
```

**Why `bisect_left` (not `bisect_right`):**
- `bisect_left(sorted, x)` returns LEFTMOST insertion point
- That index equals count of elements strictly less than x
- `bisect_right` would include elements equal to x — wrong

### Counting sort + prefix sum (the prize approach)

```python
class Solution:
    def smallerNumbersThanCurrent(self, nums: List[int]) -> List[int]:
        # Step 1: count occurrences of each value (0..100)
        count = [0] * 101
        for x in nums:
            count[x] += 1
        
        # Step 2: prefix sum — count[v] becomes "how many ≤ v"
        for v in range(1, 101):
            count[v] += count[v - 1]
        
        # Step 3: "# strictly less than x" = count[x-1]
        # count[x-1] = # elements ≤ x-1 = # elements < x
        # Edge: x=0 means nothing smaller → return 0
        return [count[x - 1] if x > 0 else 0 for x in nums]
```

**Why O(n + R):**
- Step 1: O(n)
- Step 2: O(R=101)
- Step 3: O(n)

For n=500, R=101: ~700 ops. Beats O(n log n) ≈ 4500.

**The key step — Step 2 (prefix sum on counts):**

After Step 1: `count[v]` = "how many equal to v"
After Step 2: `count[v]` = "how many ≤ v" (cumulative)

Then `count[v - 1]` = "how many ≤ v-1" = "how many strictly less than v" ✓

### Separate count and less_than arrays (clearer)

```python
class Solution:
    def smallerNumbersThanCurrent(self, nums: List[int]) -> List[int]:
        count = [0] * 101
        for x in nums:
            count[x] += 1
        
        less_than = [0] * 101
        for v in range(1, 101):
            less_than[v] = less_than[v - 1] + count[v - 1]
        
        return [less_than[x] for x in nums]
```

---

## LAYER 2: Worked Examples

### Example 1: `nums = [8, 1, 2, 2, 3]` → `[4, 0, 1, 1, 3]`

**Brute force check:**

| i | nums[i] | smaller |
|---|---------|---------|
| 0 | 8 | {1, 2, 2, 3} → 4 |
| 1 | 1 | {} → 0 |
| 2 | 2 | {1} → 1 |
| 3 | 2 | {1} → 1 |
| 4 | 3 | {1, 2, 2} → 3 |

Output: `[4, 0, 1, 1, 3]` ✓

**Counting sort trace:**

Step 1 — Counts (relevant slice):
```
count[1] = 1
count[2] = 2
count[3] = 1
count[8] = 1
```

Step 2 — Prefix:
```
count[0] = 0
count[1] = 1
count[2] = 3
count[3] = 4
count[7] = 4
count[8] = 5
```

Step 3 — Lookup `count[x - 1]`:

| x | count[x-1] | meaning |
|---|------------|---------|
| 8 | count[7] = 4 | 4 less |
| 1 | count[0] = 0 | 0 less |
| 2 | count[1] = 1 | 1 less |
| 2 | count[1] = 1 | 1 less |
| 3 | count[2] = 3 | 3 less |

Output: `[4, 0, 1, 1, 3]` ✓

### Example 2: `nums = [6, 5, 4, 8]` → `[2, 1, 0, 3]`

Sorted: `[4, 5, 6, 8]`. 6 → 2 smaller, 5 → 1, 4 → 0, 8 → 3. ✓

### Example 3: `nums = [7, 7, 7, 7]` → `[0, 0, 0, 0]`

All equal → no strict smaller. ✓

### Edge cases

- **All same:** All zeros
- **Sorted ascending:** `[0, 1, 2, 3]`
- **Sorted descending:** `[3, 2, 1, 0]`
- **Values include 0:** The `x > 0` guard matters
- **Min n=2:** `[1, 2]` → `[0, 1]`

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Brute force | O(n²) | O(1) | OK for n=500 |
| Sort + bisect | O(n log n) | O(n) | Robust for any range |
| **Counting + prefix** | **O(n + R)** | **O(R)** | **Best when R bounded** |

**Why this beats comparison sort:**

For n=500, R=101:
- Comparison sort: ~4500 ops
- Counting sort: ~601 ops

For n=10⁶, R=101:
- Comparison: 2×10⁷ ops
- Counting: ~10⁶ ops

Gap widens with n. **Bounded range = counting always wins.**

**The constraint signals the algorithm tier:**

This is a "tier-classification" problem. Hint 1 = brute. Hint 2 = sort. The constraint `nums[i] ≤ 100` whispers a third tier — counting sort.

**Recognizing constraint-based optimization** is one of the most useful interview skills: **constraints tell you which algorithm tier you're expected to demonstrate.**

**Strict less than vs less-than-or-equal — the off-by-one trap:**

After prefix: `count[v] = # ≤ v`. For strict less:
- Want: `# < x`
- = `# ≤ x-1`
- = `count[x-1]` ✓

The `x - 1` shift converts ≤ to <. The `if x > 0 else 0` guards against `x-1 = -1` (Python wraparound bug).

**Connection to other patterns you know:**

- **Counting sort (LC 1051 Day 8)** — same Step 1
- **Prefix sum (LC 1480, 2574 Day 8)** — same Step 2
- **Increment-then-check (LC 2657, 3289)** — related counter pattern

This problem **composes two patterns** you already know. Recognizing composition is a key meta-skill.

---

## LAYER 4: Interview Variations

• **Count GREATER:** `total - count[x]` after prefix sum.
• **Count ≤ instead of <:** Use `count[x]` directly. No `x-1` shift.
• **Count in range [a, b]:** `count[b] - count[a-1]`. Classic range query.
• **Stream query "how many smaller than X":** Fenwick tree (BIT) — O(log R) per op.
• **Count smaller AFTER current index (LC 315 Hard):** Merge sort with tracking, or BIT.
• **Count smaller BEFORE current index:** Forward pass with running counts.
• **Smallest k values:** `heapq.nsmallest(k, nums)`.
• **k-th smallest:** Quickselect O(n) average; `sorted(nums)[k-1]` simpler.
• **Distinct count of smaller:** Set of seen values; size = answer per element.
• **Weighted smaller:** Replace counts with weights; prefix sum still works.
• **2D version (points below-left):** Sort + Fenwick.
• **Approximate counting (huge n):** Count-min sketch or HyperLogLog.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — counting sort + prefix sum
class Solution:
    def smallerNumbersThanCurrent(self, nums: List[int]) -> List[int]:
        count = [0] * 101
        for x in nums:
            count[x] += 1
        for v in range(1, 101):
            count[v] += count[v - 1]
        return [count[x - 1] if x > 0 else 0 for x in nums]
```

**Brute force:**
```python
def smallerNumbersThanCurrent(nums):
    return [sum(x < n for x in nums) for n in nums]
```

**Sort + bisect:**
```python
from bisect import bisect_left

def smallerNumbersThanCurrent(nums):
    sorted_nums = sorted(nums)
    return [bisect_left(sorted_nums, x) for x in nums]
```

**Counter + filter:**
```python
from collections import Counter

def smallerNumbersThanCurrent(nums):
    c = Counter(nums)
    return [sum(v for k, v in c.items() if k < x) for x in nums]
```

**Separate count/less_than:**
```python
def smallerNumbersThanCurrent(nums):
    count = [0] * 101
    for x in nums:
        count[x] += 1
    less_than = [0] * 101
    for v in range(1, 101):
        less_than[v] = less_than[v - 1] + count[v - 1]
    return [less_than[x] for x in nums]
```

---

**Time:** O(n + R) counting, O(n log n) sort, O(n²) brute | **Space:** O(R) counting, O(n) sort

**Pattern flag:** "For each element, count others satisfying P" → if range R small, **counting sort + prefix sum**: count frequencies → convert to prefix → lookup. Constraint `nums[i] ≤ 100` is the signal — bounded value range = counting beats comparison sort. Composes two patterns you know: counting sort (LC 1051) + prefix sum (LC 1480, 2574). **Composition** is the meta-skill.
