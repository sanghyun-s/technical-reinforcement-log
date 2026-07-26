# 1512. Number of Good Pairs

**Difficulty:** Easy  
**Pattern:** Hash Map / Counting  
**Date Solved:** 2026-05-19  
**Status:** ✅

---

## Understanding the Goal

Count pairs `(i, j)` where `nums[i] == nums[j]` AND `i < j` (i.e., j comes after i).

**Key insight:** For each value, if it appears `n` times, the number of valid pairs is the combination `C(n, 2) = n * (n-1) / 2`. But we can compute this on the fly without ever counting first — as we walk the array, each new occurrence of a value pairs with every previous occurrence.

---

## LAYER 1: Line-by-Line Explanation

```python
from collections import defaultdict
from typing import List

class Solution:
    def numIdenticalPairs(self, nums: List[int]) -> int:
        counts = defaultdict(int)     # Tracks how many times we've seen each number so far
        ans = 0                       # Running total of good pairs
        
        for num in nums:
            ans += counts[num]        # This num pairs with every previous occurrence
            counts[num] += 1          # Now record this occurrence for future nums
        
        return ans
```

**Why the order matters:** `ans += counts[num]` runs BEFORE `counts[num] += 1`. This guarantees the current index `j` only pairs with strictly earlier indices `i < j` — never with itself.

---

## LAYER 2: Worked Examples

**Example 1: `nums = [1,2,3,1,1,3]` → Output: 4**

Walk through one element at a time:

| i | num | counts before | ans += counts[num] | counts after | ans |
|---|-----|---------------|-------------------|--------------|-----|
| 0 | 1   | {}            | +0                | {1:1}        | 0   |
| 1 | 2   | {1:1}         | +0                | {1:1, 2:1}   | 0   |
| 2 | 3   | {1:1, 2:1}    | +0                | {1:1, 2:1, 3:1} | 0 |
| 3 | 1   | {1:1, 2:1, 3:1} | +1              | {1:2, 2:1, 3:1} | 1 |
| 4 | 1   | {1:2, 2:1, 3:1} | +2              | {1:3, 2:1, 3:1} | 3 |
| 5 | 3   | {1:3, 2:1, 3:1} | +1              | {1:3, 2:1, 3:2} | 4 |

**Pair verification:** (0,3), (0,4), (3,4) for value 1, and (2,5) for value 3 → 4 pairs ✓

**Example 2: `nums = [1,1,1,1]` → Output: 6**

| i | num | counts[num] before | ans |
|---|-----|---------------------|-----|
| 0 | 1   | 0                   | 0   |
| 1 | 1   | 1                   | 1   |
| 2 | 1   | 2                   | 3   |
| 3 | 1   | 3                   | 6   |

**Math check:** C(4,2) = 4 × 3 / 2 = 6 ✓

**Example 3: `nums = [1,2,3]` → Output: 0**

No duplicates, so `counts[num]` is always 0 when we read it. Loop never adds to `ans`. ✓

**Edge case: `nums = [5]`**
Single element, loop runs once, `counts[5]` is 0 when read → `ans = 0`. ✓

---

## LAYER 3: Key Insights

| Aspect | Value |
|--------|-------|
| **Time** | O(n) — single pass |
| **Space** | O(k) — k = number of distinct values |
| **Approach** | Counting on the fly, no second pass needed |

**Why this beats the naive nested loop:** Brute force is O(n²) — check every pair. The hash map version converts it to O(n) by remembering past occurrences in O(1) lookups.

**Why this beats the "count then formula" approach:** Both are O(n), but counting on the fly is a single elegant pass. The formula `n*(n-1)/2` requires two passes (build count map, then sum the combinations). Functionally identical, but the streaming version is simpler.

**The combinatorial identity at play:**  
Sum of `0 + 1 + 2 + ... + (n-1)` = `n*(n-1)/2`. Each pass through the loop, for a value seen `k` times before, we add `k` to `ans`. Over all occurrences of a value, we add `0 + 1 + 2 + ... + (n-1)` — exactly `C(n, 2)`.

---

## LAYER 4: Interview Variations

• **Count pairs where `nums[i] + nums[j] == k`** (Two Sum-style):  
  Same pattern — use hash map storing what you've seen. For each `num`, add `counts[k - num]` to answer.

• **Count pairs where `nums[i] - nums[j] == k`** (LC 532, K-diff Pairs):  
  Two lookups per element: `counts[num + k]` and `counts[num - k]`. Need to handle k=0 carefully.

• **Count "bad" pairs where `j - i != nums[j] - nums[i]`** (LC 2364):  
  Rearrange to `nums[i] - i != nums[j] - j`. Count pairs with equal `nums[k] - k`, subtract from total pairs.

• **Return the actual pair indices, not just the count:**  
  Use `defaultdict(list)` mapping value → list of indices. For each new occurrence, generate pairs with all previous indices.

• **Count pairs in a stream (data arrives one at a time):**  
  Exact same algorithm — the streaming nature is already baked in. Just maintain `counts` and `ans` as state.

• **Constraint: values can be huge (10^9), but only 100 array length:**  
  Hash map still works (O(n) space). If memory mattered AND values were bounded, you could use a fixed array instead of a dict.

• **Count pairs where values are equal within a sliding window of size k:**  
  Add a "remove" step when window slides — decrement counts as elements leave the window.

---

## LAYER 5: Cheat Sheet

```python
# Streaming hash map (preferred — one pass)
def numIdenticalPairs(nums):
    counts = defaultdict(int)
    ans = 0
    for num in nums:
        ans += counts[num]
        counts[num] += 1
    return ans
```

**Alternative — Count then formula:**
```python
from collections import Counter
def numIdenticalPairs(nums):
    return sum(c * (c - 1) // 2 for c in Counter(nums).values())
```

**Brute force (for comparison, NOT recommended):**
```python
def numIdenticalPairs(nums):
    return sum(1 for i in range(len(nums)) for j in range(i+1, len(nums)) if nums[i] == nums[j])
```

---

**Time:** O(n) | **Space:** O(k) where k = distinct values

**Pattern flag:** Whenever a problem asks to count pairs `(i, j)` with `i < j` satisfying some equality, reach for the streaming hash map first. The trick is: walk the array once, ask "how many earlier elements pair with me?", then record yourself.
