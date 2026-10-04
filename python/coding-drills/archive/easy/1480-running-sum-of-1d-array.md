# 1480. Running Sum of 1d Array

**Difficulty:** Easy
**Pattern:** Prefix Sum
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

For each index `i`, return the sum of `nums[0..i]` (inclusive). This is the textbook definition of a **prefix sum array**.

**Key insight:** Each entry is just the previous entry plus the current element: `result[i] = result[i-1] + nums[i]`. One pass, O(n) time.

If you can mutate the input, you can do it in O(1) extra space by accumulating in place. This is the simplest possible application of the prefix sum technique and a perfect closer for Day 8.

---

## LAYER 1: Line-by-Line Explanation

**In-place running sum (optimal):**

```python
class Solution:
    def runningSum(self, nums: List[int]) -> List[int]:
        for i in range(1, len(nums)):
            nums[i] += nums[i - 1]      # Each cell absorbs the running total
        return nums
```

**How it works:**
- Start from index 1 (index 0 is already correct: `result[0] = nums[0]`)
- At each `i`, add the previous (already-cumulative) value to current
- After the loop, `nums` has been transformed into its running sum

For `nums = [1, 2, 3, 4]`:
- i=1: `nums[1] += nums[0]` → `[1, 3, 3, 4]`
- i=2: `nums[2] += nums[1]` → `[1, 3, 6, 4]`
- i=3: `nums[3] += nums[2]` → `[1, 3, 6, 10]`

**Why this works in a single pass:** By the time we read `nums[i-1]` at iteration `i`, that cell already holds the cumulative sum (it was updated in iteration `i-1`). So we can chain the updates without needing a separate storage array.

**Pythonic one-liner with itertools:**

```python
from itertools import accumulate
class Solution:
    def runningSum(self, nums: List[int]) -> List[int]:
        return list(accumulate(nums))
```

`accumulate(nums)` is a generator that yields the running sum at each step. `list(...)` materializes it. This is *the* idiomatic Python solution — recognized instantly by anyone who's read the standard library.

**Out-of-place version (doesn't mutate input):**

```python
class Solution:
    def runningSum(self, nums: List[int]) -> List[int]:
        result = [0] * len(nums)
        result[0] = nums[0]
        for i in range(1, len(nums)):
            result[i] = result[i - 1] + nums[i]
        return result
```

Same logic, but builds a new array. O(n) extra space. Prefer this if you're not allowed to modify the input.

---

## LAYER 2: Worked Examples

**Example 1: `nums = [1, 2, 3, 4]` → `[1, 3, 6, 10]`**

| i | nums[i] | running_sum after iteration |
|---|---------|------------------------------|
| 0 | 1 | 1 |
| 1 | 2 | 1+2 = 3 |
| 2 | 3 | 3+3 = 6 |
| 3 | 4 | 6+4 = 10 |

Output: `[1, 3, 6, 10]` ✓

**Example 2: `nums = [1, 1, 1, 1, 1]` → `[1, 2, 3, 4, 5]`**

Each step adds 1 to the previous running total. Result is `[1, 2, 3, 4, 5]` ✓ — the counting numbers.

**Example 3: `nums = [3, 1, 2, 10, 1]` → `[3, 4, 6, 16, 17]`**

| i | nums[i] | running_sum |
|---|---------|-------------|
| 0 | 3 | 3 |
| 1 | 1 | 3+1 = 4 |
| 2 | 2 | 4+2 = 6 |
| 3 | 10 | 6+10 = 16 |
| 4 | 1 | 16+1 = 17 |

Output: `[3, 4, 6, 16, 17]` ✓

**In-place trace for Example 3 (watch the array transform):**

```
start: [3, 1, 2, 10, 1]
i=1:   [3, 4, 2, 10, 1]      (nums[1] += nums[0])
i=2:   [3, 4, 6, 10, 1]      (nums[2] += nums[1])
i=3:   [3, 4, 6, 16, 1]      (nums[3] += nums[2])
i=4:   [3, 4, 6, 16, 17]     (nums[4] += nums[3])
```

**Edge cases:**
- `nums = [5]` → `[5]` (single element, loop body doesn't execute)
- `nums = [0, 0, 0]` → `[0, 0, 0]` (zeros stay zero)
- `nums = [-1, -2, -3]` → `[-1, -3, -6]` (negatives work fine)
- `nums = [10, -10, 10, -10]` → `[10, 0, 10, 0]` (cumulative can dip to zero or below)
- Large values near the constraint limit `nums = [10^6, 10^6, ..., 10^6]` of length 1000 → final entry = 10^9, fits comfortably in 32-bit signed int.

---

## LAYER 3: Key Insights

| Approach | Time | Space (excluding output) | Notes |
|----------|------|--------------------------|-------|
| In-place mutation | O(n) | **O(1)** | **Optimal when mutation is OK** |
| `itertools.accumulate` | O(n) | O(n) (new list) | **Pythonic, one-liner** |
| Out-of-place with explicit loop | O(n) | O(n) | Most readable, no side effects |
| Brute force (sum slice per index) | O(n²) | O(1) | Wasteful — DON'T use |

**This is the foundational prefix-sum problem:**

LC 1480 exists to teach you the prefix sum array. Every other prefix-sum problem (range-sum queries, subarray-sum equals k, product except self, etc.) builds on this exact idea: precompute cumulative aggregates in O(n) so you can answer range questions in O(1).

**The prefix sum identity:**

If `P[i] = nums[0] + nums[1] + ... + nums[i]`, then:

> `sum(nums[l..r]) = P[r] - P[l-1]` (with `P[-1] = 0`)

This single identity is the reason prefix sums matter. It turns "sum any range" from O(n) into O(1).

**Why mutation in-place works without breaking the algorithm:**

A common worry: "If I overwrite `nums[i-1]`, won't future iterations get wrong values?"

No — because we only READ `nums[i-1]` to UPDATE `nums[i]`. We never need the ORIGINAL `nums[i-1]` again. The dependency graph is strictly forward: each `i` depends on `i-1` (already finalized) and itself (about to be finalized). No conflicts.

**The brute force trap:**

```python
# DON'T DO THIS — O(n²)
def runningSum(nums):
    return [sum(nums[:i+1]) for i in range(len(nums))]
```

For each index, this re-sums the whole prefix from scratch. Total work: 1 + 2 + 3 + ... + n = O(n²). For n=1000 it's 10⁶ ops — slow but technically passes. For larger inputs, it dies. Always reach for the O(n) incremental version.

**`accumulate` is more general than you think:**

`itertools.accumulate(iterable, func=operator.add)` works for ANY associative binary function:
- `accumulate(nums)` — running sum (default)
- `accumulate(nums, operator.mul)` — running product
- `accumulate(nums, max)` — running max
- `accumulate(nums, operator.xor)` — running XOR
- `accumulate(nums, lambda a, b: a + b, initial=0)` — with explicit initial value (Python 3.8+)

Internalize this — it's one of the most useful one-liners in the Python standard library.

---

## LAYER 4: Interview Variations

• **Running product instead of running sum:**
  `list(accumulate(nums, operator.mul))` or replace `+=` with `*=`.

• **Running max / running min:**
  `list(accumulate(nums, max))` — for each i, the max of `nums[0..i]`.

• **Running XOR:**
  `list(accumulate(nums, operator.xor))` — useful for parity / bit-tracking problems.

• **Range sum queries** (LC 303 Range Sum Query - Immutable):
  Precompute prefix sum once in O(n). Each query `sum(nums[l..r]) = P[r] - P[l-1]` in O(1).

• **Range sum queries with updates** (LC 307):
  Prefix sum becomes O(n) per update. Use a Fenwick tree / segment tree for O(log n) per operation.

• **Subarray sum equals k** (LC 560):
  Prefix sum + hashmap. For each `P[j]`, look up `P[j] - k` in the hashmap.

• **Maximum subarray sum** (LC 53):
  Kadane's algorithm OR `max(P[r] - min(P[0..r-1]))`. Prefix sums give an alternative framing.

• **Continuous subarray sum divisible by k** (LC 523):
  `P[r] - P[l] ≡ 0 (mod k)` ⟺ `P[r] ≡ P[l] (mod k)`. Hash prefix sums by their value mod k.

• **Product of array except self** (LC 238):
  Two-pass running products (left and right). Same shape as running sum but multiplicative.

• **2D prefix sum** (LC 304):
  `P[i][j] = sum of mat[0..i][0..j]`. Submatrix sum in O(1).

• **Difference array (the inverse operation):**
  Given a running sum, recover original: `nums[i] = P[i] - P[i-1]`. Useful for range updates.

• **Stream version (numbers arrive online, query latest running sum):**
  Maintain a single variable, update with each new value. O(1) per update.

• **K-th element of the running sum:**
  If you need just one entry, `sum(nums[:k+1])` — O(k) instead of O(n).

• **Running sum modulo m:**
  `running = (running + x) % m` at each step. Avoid overflow / keep values bounded.

• **Reverse running sum (suffix sums):**
  `suffix[i] = sum(nums[i..n-1])`. Walk from right to left, same template.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic one-liner
from itertools import accumulate
class Solution:
    def runningSum(self, nums: List[int]) -> List[int]:
        return list(accumulate(nums))
```

**In-place — O(1) extra space:**
```python
def runningSum(nums):
    for i in range(1, len(nums)):
        nums[i] += nums[i - 1]
    return nums
```

**Out-of-place loop (most readable):**
```python
def runningSum(nums):
    result = [nums[0]]
    for i in range(1, len(nums)):
        result.append(result[-1] + nums[i])
    return result
```

**With explicit running variable:**
```python
def runningSum(nums):
    total = 0
    result = []
    for x in nums:
        total += x
        result.append(total)
    return result
```

**Brute force (DON'T USE):**
```python
def runningSum(nums):
    return [sum(nums[:i+1]) for i in range(len(nums))]
```

---

**Time:** O(n) | **Space:** O(1) in-place, or O(n) for output

**Pattern flag:** This is **THE** prefix sum problem. Whenever a future problem asks about "sum over any range" or "cumulative something," your first move is to build this array. Memorize the `accumulate` one-liner; it's a Python superpower.
