# 3667. Sort Array By Absolute Value

**Difficulty:** Easy
**Pattern:** Sorting / Custom Key
**Date Solved:** 2026-06-09
**Status:** ✅

---

## Understanding the Goal

Sort the array by **absolute value** rather than raw value. Any tie-breaking order is acceptable.

**Key insight:** The **custom sort key** pattern. Python's `sorted()` and `list.sort()` accept a `key` function that transforms each element before comparison. Pass `key=abs` and you're done.

Elements themselves are unchanged — only the comparison rule changes.

---

## LAYER 1: Line-by-Line Explanation

### Pythonic one-liner (preferred)

```python
class Solution:
    def sortByAbsoluteValue(self, nums: List[int]) -> List[int]:
        # `sorted(iterable, key=func)` returns NEW list sorted by func(x) values
        # `abs` is a built-in function — pass the function itself (no parentheses!)
        # For each x, comparison uses abs(x) instead of x
        return sorted(nums, key=abs)
```

**Crucial detail — `key=abs` not `key=abs()`:**
- `abs` is the function object — pass it directly
- `abs()` would CALL it (and fail, no argument)
- Same convention for `key=len`, `key=str.lower`, etc.

**Why `sorted()` not `nums.sort()`:**
- `sorted(nums)` returns NEW list, input unchanged (safer)
- `nums.sort()` mutates in place, returns `None`

For interview defaults, prefer `sorted()` — no side effects.

### In-place sort variant

```python
class Solution:
    def sortByAbsoluteValue(self, nums: List[int]) -> List[int]:
        nums.sort(key=abs)              # Saves O(n) memory; mutates input
        return nums
```

### Lambda (more verbose)

```python
class Solution:
    def sortByAbsoluteValue(self, nums: List[int]) -> List[int]:
        # `lambda x: abs(x)` is equivalent to just `abs`
        return sorted(nums, key=lambda x: abs(x))
```

### Decorate-Sort-Undecorate (DSU) — classic pattern

```python
class Solution:
    def sortByAbsoluteValue(self, nums: List[int]) -> List[int]:
        # 1. Decorate: pair each element with sort key
        decorated = [(abs(x), x) for x in nums]
        # 2. Sort: tuples compared lexicographically
        decorated.sort()
        # 3. Undecorate: extract original values
        return [x for _, x in decorated]
```

What `key=` does internally — but explicit. Useful in older languages without `key`.

### Squared values (equivalent ordering)

```python
class Solution:
    def sortByAbsoluteValue(self, nums: List[int]) -> List[int]:
        return sorted(nums, key=lambda x: x * x)
```

x² preserves abs ordering since x² is monotonic in |x|.

---

## LAYER 2: Worked Examples

### Example 1: `nums = [3, -1, -4, 1, 5]` → `[-1, 1, 3, -4, 5]`

| x | abs(x) |
|---|--------|
| 3 | 3 |
| -1 | 1 |
| -4 | 4 |
| 1 | 1 |
| 5 | 5 |

Sort by abs column:

| abs | original x |
|-----|------------|
| 1 | -1 (stable: was at index 1) |
| 1 | 1 (stable: was at index 3) |
| 3 | 3 |
| 4 | -4 |
| 5 | 5 |

Output: `[-1, 1, 3, -4, 5]` ✓

### Example 2: `nums = [-100, 100]` → `[-100, 100]`

Both have abs = 100. Python's sort is **stable** — input order preserved among ties.

Original: `[-100, 100]` → output `[-100, 100]` ✓

If input were `[100, -100]`, output would be `[100, -100]` — both valid per the problem.

### Edge cases

- **All positive:** Works like normal numeric sort
- **All negative:** `[-3, -2, -1]` → abs `[3, 2, 1]` → sort ascending → `[-1, -2, -3]`
- **Single element:** Returned as-is
- **Duplicates with sign variants:** Stability preserves relative order
- **Contains zero:** `0` has abs=0; sorts to front
- **Max n=100:** Trivially fast

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| `sorted(nums, key=abs)` | O(n log n) | O(n) | **Preferred** |
| `nums.sort(key=abs)` | O(n log n) | O(1) extra | In-place |
| DSU manual | O(n log n) | O(n) | Educational |
| Bucket sort | O(n + R) | O(R) | Tightest for bounded |

**The `key=` parameter — one of Python's most reused tools:**

```python
sorted(strings, key=len)                          # By string length
sorted(words, key=str.lower)                      # Case-insensitive
sorted(students, key=lambda s: s.gpa)             # By attribute
sorted(items, key=lambda x: (-x.score, x.name))   # Multi-level: score desc, name asc
```

The `key` function is called ONCE per element (cached for comparisons) → O(n × cost(key)).

**Why `key=abs` is Python at its best:**
- Reads naturally: "sorted by abs"
- One word change to swap rules
- No comparator needed
- Stable: ties keep input order

Java/C++ equivalents are wordier:
```java
Arrays.sort(nums, (a, b) -> Integer.compare(Math.abs(a), Math.abs(b)));
```

Python's `key` transforms ONCE, then sorts; comparator-based sorts call comparator O(n log n) times.

**Stability matters:**

A sort is **stable** if equal-key elements retain original order. Python's sort (Timsort) is stable. Enables multi-pass sorting:
```python
# Sort by city, then by name — within each city, names alphabetical
sorted(sorted(items, key=lambda x: x.name), key=lambda x: x.city)
```

**Common interview gotcha — sorting strings of numbers:**

```python
sorted(["10", "2", "1"])           # ['1', '10', '2'] — LEXICOGRAPHIC!
sorted(["10", "2", "1"], key=int)  # ['1', '2', '10'] — numeric ✓
```

`key=int` converts each string to int before comparing. Same idea as `key=abs`.

**Bucket sort for bounded range:**

Since `|x| ≤ 100`, you could bucket: O(n + R) beats O(n log n). For n=100, trivially fast either way.

---

## LAYER 4: Interview Variations

• **Descending:** `sorted(nums, key=abs, reverse=True)`.
• **Ties broken by sign (negatives first):** `sorted(nums, key=lambda x: (abs(x), x))`.
• **Ties broken by sign (positives first):** `sorted(nums, key=lambda x: (abs(x), -x))`.
• **k-th smallest by abs:** `heapq.nsmallest(k, nums, key=abs)` — O(n log k).
• **Top k by abs:** `heapq.nlargest(k, nums, key=abs)`.
• **Already-sorted by raw value (LC 977):** Two-pointer from both ends; extremes have largest abs.
• **Sort by multiple criteria:** Tuple key `(criterion1, criterion2)`.
• **Custom comparator:** `functools.cmp_to_key(comparator)`.
• **Bucket sort:** When `|x|` bounded, O(n + R) beats O(n log n).
• **External sort (data > memory):** Read chunks, sort each, merge — same `key=`.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — one-liner with key=abs
class Solution:
    def sortByAbsoluteValue(self, nums: List[int]) -> List[int]:
        return sorted(nums, key=abs)
```

**In-place variant:**
```python
def sortByAbsoluteValue(nums):
    nums.sort(key=abs)
    return nums
```

**With lambda (verbose):**
```python
def sortByAbsoluteValue(nums):
    return sorted(nums, key=lambda x: abs(x))
```

**Squared values:**
```python
def sortByAbsoluteValue(nums):
    return sorted(nums, key=lambda x: x * x)
```

**DSU pattern:**
```python
def sortByAbsoluteValue(nums):
    decorated = [(abs(x), x) for x in nums]
    decorated.sort()
    return [x for _, x in decorated]
```

**Descending:**
```python
def sortByAbsoluteValue(nums):
    return sorted(nums, key=abs, reverse=True)
```

**Bucket sort (since |x| ≤ 100):**
```python
def sortByAbsoluteValue(nums):
    buckets = [[] for _ in range(101)]
    for x in nums:
        buckets[abs(x)].append(x)
    return [x for bucket in buckets for x in bucket]
```

---

**Time:** O(n log n) comparison sort, O(n + R) bucket sort | **Space:** O(n) new list, O(1) in-place

**Pattern flag:** "Sort by transformed value, not raw" → **custom `key=` function**. `sorted(items, key=func)` calls `func` once per element and sorts by results. Built-ins (`abs`, `len`, `str.lower`) and attribute accessors make idiomatic Python. Transformation is invisible to result — only COMPARISON RULE changes. Cleanest way to express "sort by X" in Python — memorize.
