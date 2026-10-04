# 2574. Left and Right Sum Differences

**Difficulty:** Easy
**Pattern:** Prefix Sum / Two Pass
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

For each index `i`, compute the absolute difference between the sum of everything to its left and the sum of everything to its right.

**Key insight (the editorial's trick):** You don't need separate `leftSum[]` and `rightSum[]` arrays. Build the answer in two sweeps using a single array:

1. **Forward pass:** Fill `ans[i]` with `leftSum[i]` (running prefix sum, NOT including `nums[i]`)
2. **Backward pass:** Subtract `rightSum[i]` from `ans[i]`, take absolute value

The second pass uses the fact that `ans[i]` already holds `leftSum[i]`, so `ans[i] - rightSum[i]` gives `leftSum[i] - rightSum[i]`. Take `abs(...)` and you're done. O(n) time, O(1) extra space (output not counted).

Even cleaner: use the identity `rightSum[i] = total - leftSum[i] - nums[i]`. One pass, no second loop.

---

## LAYER 1: Line-by-Line Explanation

**Editorial — Two-pass approach:**

```python
class Solution:
    def leftRightDifference(self, nums: List[int]) -> List[int]:
        n = len(nums)
        ans = [0] * n

        # Pass 1: ans[i] = leftSum[i] (sum of elements before index i)
        left_sum = 0
        for i in range(n):
            ans[i] = left_sum             # Store leftSum BEFORE adding nums[i]
            left_sum += nums[i]           # Now include nums[i] for next iteration

        # Pass 2: replace ans[i] with abs(leftSum[i] - rightSum[i])
        right_sum = 0
        for i in range(n - 1, -1, -1):
            ans[i] = abs(ans[i] - right_sum)   # ans[i] still holds leftSum[i]
            right_sum += nums[i]               # Include nums[i] for next iteration

        return ans
```

**The "store before update" pattern (critical to understand):**

In pass 1, we want `ans[i] = sum(nums[0..i-1])` — the sum BEFORE `nums[i]`. So at index `i`:
- `left_sum` currently holds the running sum **not yet including** `nums[i]`. That's `leftSum[i]`.
- Store it: `ans[i] = left_sum`.
- THEN include `nums[i]` so the variable is ready for the next iteration: `left_sum += nums[i]`.

If you flip the order (`left_sum += nums[i]` first, then `ans[i] = left_sum`), you'd accidentally include `nums[i]` itself — that's the most common bug in prefix-sum code. Always update AFTER you've consumed the previous value.

The backward pass uses the same trick with `right_sum`.

**One-pass solution using total trick:**

```python
class Solution:
    def leftRightDifference(self, nums: List[int]) -> List[int]:
        total = sum(nums)
        left_sum = 0
        ans = []
        for x in nums:
            right_sum = total - left_sum - x
            ans.append(abs(left_sum - right_sum))
            left_sum += x
        return ans
```

**Why this works:**
- Total of the whole array doesn't change → compute once.
- At index `i`: `leftSum[i] + nums[i] + rightSum[i] = total`, so `rightSum[i] = total - leftSum[i] - nums[i]`.
- No need to walk backward — derive `right_sum` from `total` and `left_sum`.

Same O(n) complexity, but a single pass and arguably more readable.

---

## LAYER 2: Worked Examples

**Example 1: `nums = [10, 4, 8, 3]` → `[15, 1, 11, 22]`**

*Editorial two-pass walkthrough:*

**Pass 1 (forward, fill leftSum):**

| i | left_sum BEFORE | ans[i] = left_sum | left_sum AFTER += nums[i] |
|---|------------------|-------------------|---------------------------|
| 0 | 0 | 0 | 10 |
| 1 | 10 | 10 | 14 |
| 2 | 14 | 14 | 22 |
| 3 | 22 | 22 | 25 |

After pass 1: `ans = [0, 10, 14, 22]` (this is `leftSum[]`)

**Pass 2 (backward, subtract rightSum, take abs):**

| i | right_sum BEFORE | ans[i] BEFORE | ans[i] = abs(... - right_sum) | right_sum AFTER += nums[i] |
|---|------------------|---------------|-------------------------------|----------------------------|
| 3 | 0  | 22 | abs(22 - 0) = 22  | 3  |
| 2 | 3  | 14 | abs(14 - 3) = 11  | 11 |
| 1 | 11 | 10 | abs(10 - 11) = 1  | 15 |
| 0 | 15 | 0  | abs(0 - 15) = 15  | 25 |

Final `ans = [15, 1, 11, 22]` ✓

*One-pass walkthrough:*

`total = 10 + 4 + 8 + 3 = 25`

| i | x | left_sum | right_sum = 25 - left_sum - x | abs(left_sum - right_sum) |
|---|---|----------|-------------------------------|---------------------------|
| 0 | 10 | 0  | 25 - 0 - 10 = 15 | abs(0 - 15) = 15 |
| 1 | 4  | 10 | 25 - 10 - 4 = 11 | abs(10 - 11) = 1 |
| 2 | 8  | 14 | 25 - 14 - 8 = 3  | abs(14 - 3) = 11 |
| 3 | 3  | 22 | 25 - 22 - 3 = 0  | abs(22 - 0) = 22 |

Result: `[15, 1, 11, 22]` ✓

**Example 2: `nums = [1]` → `[0]`**

n = 1. No left or right neighbors.

- leftSum[0] = 0, rightSum[0] = 0
- abs(0 - 0) = 0

Output: `[0]` ✓

**Why the formula `leftSum[i] + nums[i] + rightSum[i] = total` works:**

For index `i`, the array splits into three parts:
- Everything strictly to the LEFT of i → `leftSum[i]`
- The element AT index i → `nums[i]`
- Everything strictly to the RIGHT of i → `rightSum[i]`

These three parts cover every index exactly once, with no overlap. So their sum equals the total of the whole array.

**Edge cases:**
- `nums = [5]` → `[0]` (single element, both sums are 0)
- `nums = [1, 1]` → `leftSum = [0, 1]`, `rightSum = [1, 0]`, ans = `[|0-1|, |1-0|] = [1, 1]`
- `nums = [5, 5, 5]` → `leftSum = [0, 5, 10]`, `rightSum = [10, 5, 0]`, ans = `[10, 0, 10]`
- Constant array `[c, c, c, c]` of length n: leftSum[i] = i·c, rightSum[i] = (n-1-i)·c → ans[i] = c · |2i − (n−1)|

---

## LAYER 3: Key Insights

| Approach | Time | Space (excluding output) | Notes |
|----------|------|--------------------------|-------|
| Naive nested loops | O(n²) | O(1) | Hint 2 suggests this — works but wasteful |
| Two separate arrays (leftSum, rightSum) | O(n) | O(n) | Conceptually clean |
| Two passes with reused output array (editorial) | O(n) | **O(1)** | **Minimal extra memory** |
| One pass with total trick | O(n) | O(1) | **Single sweep, simplest logic** |

**The reusing-output pattern (worth internalizing):**

The editorial's elegance is recognizing that you can pile multiple meanings onto the same array:

1. First pass: `ans[i]` means "leftSum at i"
2. Second pass: `ans[i]` becomes "abs(leftSum - rightSum) at i"

This "transform-in-place across multiple passes" trick is common in tight-memory algorithms and competitive programming. The cost is mental load — you have to track which "phase" each cell is in.

**The total-trick pattern (more general):**

`rightSum[i] = total - leftSum[i] - nums[i]` is an instance of the broader idea: **whenever you have a running prefix and need the complementary suffix, derive it from the total instead of walking backward.**

This works for sum, but also for:
- XOR: `rightXOR = totalXOR ^ leftXOR ^ nums[i]` (because XOR is its own inverse)
- Product (if no zeros): `rightProduct = totalProduct / leftProduct / nums[i]`
- Min/Max: doesn't work directly — you DO need both directions.

**When to prefer two-pass vs one-pass with total:**

- **Two-pass (editorial):** Pedagogically clearer, no risk of integer overflow concerns, doesn't rely on identity tricks.
- **One-pass (total trick):** Cleaner code, single loop, requires recognizing the identity. Sum-friendly because all operations are integer addition.

In an interview, mention both. The "leftSum + rightSum + self = total" insight is what gets you bonus points.

**Why the hints suggest O(n²):**

Hints 1 and 2 describe the brute force: for each index `i`, walk left to sum and walk right to sum. That's O(n²) — n indices × O(n) per index. Acceptable for `n ≤ 1000` (10^6 ops), but the editorial demonstrates the O(n) prefix-sum upgrade.

**General prefix-sum mantra:**

> "Any time a problem asks about sums over ranges or partitions, build a prefix sum."

Prefix sum is the single most important array-processing technique you can learn. It converts "sum over [l, r]" from O(r-l+1) to O(1) per query.

---

## LAYER 4: Interview Variations

• **Return the actual `leftSum` and `rightSum` arrays separately** (not the absolute difference):
  Build both with one forward pass + one backward pass.

• **Find index `i` that minimizes |leftSum - rightSum|** ("balance point" or LC 724 Find Pivot Index):
  Compute prefix sums, find the index where leftSum equals rightSum (or closest to it).

• **Find the pivot index** (LC 724) — index where leftSum == rightSum:
  Use `total - leftSum - nums[i] == leftSum` → `leftSum == (total - nums[i]) / 2` (if integer).

• **Sum of elements within a sliding window of fixed size k:**
  Different problem — fixed-size sliding window, not prefix sum. Use `sum(nums[i:i+k])` per iteration or maintain a running sum.

• **2D prefix sum** for matrix range queries (LC 304):
  Extends to `prefix[i][j] = sum of mat[0..i][0..j]`. Query any submatrix in O(1) after O(rows × cols) preprocessing.

• **Subarray sum equals k** (LC 560):
  Prefix sum + hash map. For each prefix `P[j]`, look up how many earlier prefixes equal `P[j] - k`.

• **Maximum subarray sum** (LC 53 Kadane):
  Related but uses local-max DP, not prefix sum directly.

• **Update operations (mutable array)**:
  Prefix sum no longer cheap for updates (O(n) per update). Use a **Fenwick tree (BIT)** or **segment tree** for O(log n) point updates + range queries.

• **Range update + point query:**
  Difference array trick: store `diff[i] = arr[i] - arr[i-1]`. Range update becomes 2 point updates on `diff`.

• **Product of array except self** (LC 238):
  Classic two-pass: left products then right products, multiply. Same shape as this problem with × instead of +.

• **XOR of array except self:**
  `totalXOR ^ nums[i]` since XOR is self-inverse. One-liner.

• **Min/max of array except self:**
  Can't use the "total minus self" trick (min isn't invertible). Need two passes maintaining running min/max from each direction.

• **Difference between left and right MAXIMUMS instead of sums:**
  Build left-max prefix and right-max suffix arrays, then compute diff. Same template, different aggregator.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — one pass with total trick
class Solution:
    def leftRightDifference(self, nums: List[int]) -> List[int]:
        total = sum(nums)
        left_sum = 0
        ans = []
        for x in nums:
            right_sum = total - left_sum - x
            ans.append(abs(left_sum - right_sum))
            left_sum += x
        return ans
```

**Editorial — two-pass reusing output array:**
```python
def leftRightDifference(nums):
    n = len(nums)
    ans = [0] * n

    left_sum = 0
    for i in range(n):
        ans[i] = left_sum
        left_sum += nums[i]

    right_sum = 0
    for i in range(n - 1, -1, -1):
        ans[i] = abs(ans[i] - right_sum)
        right_sum += nums[i]

    return ans
```

**Two separate arrays (most readable):**
```python
def leftRightDifference(nums):
    n = len(nums)
    left = [0] * n
    right = [0] * n
    for i in range(1, n):
        left[i] = left[i-1] + nums[i-1]
    for i in range(n - 2, -1, -1):
        right[i] = right[i+1] + nums[i+1]
    return [abs(left[i] - right[i]) for i in range(n)]
```

**With `accumulate` from itertools:**
```python
from itertools import accumulate
def leftRightDifference(nums):
    n = len(nums)
    left = [0] + list(accumulate(nums[:-1]))
    right = list(accumulate(nums[:0:-1]))[::-1] + [0]
    return [abs(l - r) for l, r in zip(left, right)]
```

---

**Time:** O(n) | **Space:** O(1) excluding output

**Pattern flag:** "Need sum to the left / sum to the right at every index" → **prefix sum** is the right tool. Two key tricks: (1) store running sum BEFORE updating it to exclude the current element; (2) use `rightSum = total - leftSum - nums[i]` to avoid a second pass when the aggregator is invertible (sum, XOR). For non-invertible aggregators (min, max), two passes are unavoidable.
