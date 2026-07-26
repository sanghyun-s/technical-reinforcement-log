# 561. Array Partition

**Difficulty:** Easy
**Pattern:** Greedy / Sorting
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

You have `2n` numbers. Pair them up into `n` pairs and sum the *minimum* of each pair. Maximize that sum.

**Key insight (the entire problem):** Sort the array, then sum every element at an **even index** (0, 2, 4, ...). That's it — one line.

**Why:** When the array is sorted, pairing adjacent elements `(a[0], a[1]), (a[2], a[3]), ...` minimizes the "loss" from each pair. The smaller of each pair is the even-indexed one, and you've made every pair's smaller value as large as it can possibly be.

The editorial uses **counting sort** (because values are bounded to `[-10^4, 10^4]`), which gets O(n) time instead of O(n log n). But the standard sort solution is the one you'd write in an interview — it's a one-liner.

---

## LAYER 1: Line-by-Line Explanation

**Standard sort solution (the one-liner):**

```python
class Solution:
    def arrayPairSum(self, nums: List[int]) -> int:
        return sum(sorted(nums)[::2])
```

- `sorted(nums)` — ascending sort, O(n log n)
- `[::2]` — slice every other element starting from index 0 (the even indices)
- `sum(...)` — total

**Editorial's counting sort solution (O(n) — faster because values are bounded):**

```python
class Solution:
    def arrayPairSum(self, nums: List[int]) -> int:
        K = 10000
        # Frequency array indexed by (value + K) to handle negatives
        element_to_count = [0] * (2 * K + 1)
        for element in nums:
            element_to_count[element + K] += 1

        max_sum = 0
        is_even_index = True

        # Walk values low-to-high (equivalent to iterating a sorted array)
        for element in range(2 * K + 1):
            while element_to_count[element] > 0:
                if is_even_index:
                    max_sum += element - K       # undo the +K offset
                is_even_index = not is_even_index
                element_to_count[element] -= 1
        return max_sum
```

**Why the `+ K` offset:** Python list indices must be ≥ 0. Values can be negative (down to -10,000). Shifting by K = 10,000 maps the range `[-10000, 10000]` to `[0, 20000]`. The subtraction `element - K` reverses it when reading back.

**Why this is O(n) and not O(value range):** Strictly it's O(n + range), but since range is a constant (20,001), the practical complexity is O(n).

---

## LAYER 2: Worked Examples

**Example 1: `nums = [1,4,3,2]` → 4**

Sort: `[1, 2, 3, 4]`

| index | value | even? | take? |
|-------|-------|-------|-------|
| 0 | 1 | ✅ | +1 |
| 1 | 2 | ❌ | skip |
| 2 | 3 | ✅ | +3 |
| 3 | 4 | ❌ | skip |

Sum: 1 + 3 = 4 ✓

Pairs: (1,2) and (3,4) → min(1,2) + min(3,4) = 1 + 3 = 4 ✓

**Example 2: `nums = [6,2,6,5,1,2]` → 9**

Sort: `[1, 2, 2, 5, 6, 6]`

| index | value | even? | take? |
|-------|-------|-------|-------|
| 0 | 1 | ✅ | +1 |
| 1 | 2 | ❌ | skip |
| 2 | 2 | ✅ | +2 |
| 3 | 5 | ❌ | skip |
| 4 | 6 | ✅ | +6 |
| 5 | 6 | ❌ | skip |

Sum: 1 + 2 + 6 = 9 ✓

Pairs: (1,2), (2,5), (6,6) → min=1, min=2, min=6 → 9 ✓

**Why other pairings give worse results — let's prove with example 1:**

| pairing | mins | sum |
|---------|------|-----|
| (1,4),(2,3) | 1, 2 | **3** |
| (1,3),(2,4) | 1, 2 | **3** |
| (1,2),(3,4) | 1, 3 | **4** ← optimal (adjacent after sort) |

**Why the editorial's counting sort gives the same answer:**

For `nums = [1,4,3,2]`:
- After building freq: `count[1+K]=1, count[2+K]=1, count[3+K]=1, count[4+K]=1`
- Walk from low value to high. is_even_index flips on every value.
- value 1: take (+1), flip → next is odd
- value 2: skip, flip → next is even
- value 3: take (+3), flip → next is odd
- value 4: skip, flip → next is even
- Total: 4 ✓

The counting sort effectively simulates "walk the sorted array, take every other element."

**Walkthrough with duplicates — `nums = [7,7,7,7]`:**

Sort: `[7,7,7,7]`. Take indices 0, 2 → 7+7 = 14.
Pairing: (7,7), (7,7) → min=7, min=7 → 14 ✓

**Edge cases:**
- `nums = [1,1]` → sort [1,1] → take index 0 → 1
- Negative values: `nums = [-5, -3, -1, 1]` → take indices 0, 2 → -5 + -1 = -6
- All same: `nums = [3,3,3,3,3,3]` → 3+3+3 = 9

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Sort + take even indices | O(n log n) | O(1) or O(n) for sort | **Default, one-liner** |
| Counting sort (editorial) | O(n + R) where R = value range | O(R) | Faster when R is small/bounded |
| Brute force (try all pairings) | O((2n)! / (2^n × n!)) | exponential | Useless beyond n=5 |

**The greedy proof — why adjacent pairing in sorted order is optimal:**

Claim: Among any pairing scheme, pairing adjacent elements in sorted order maximizes the sum of mins.

Sketch:
- Sort the array as `a[0] ≤ a[1] ≤ ... ≤ a[2n-1]`.
- The smallest element `a[0]` MUST be the min of whatever pair it's in (it's the smallest of all). So its contribution is `a[0]`, no matter who it's paired with.
- We "lose" the other element of that pair (it doesn't count). To minimize loss, pair `a[0]` with the next-smallest `a[1]` — that's the smallest possible sacrifice.
- Now recurse on `a[2..]`.

Result: take `a[0], a[2], a[4], ...` — every even index.

**Why hint #3 is the heart of the problem:** "Minimum element gets added in sacrifice of maximum element" — meaning whenever you pair `a` with `b` where `a < b`, you keep `a` and "waste" `b`. To minimize total waste, waste only the values that are barely-larger than the kept value — pair each kept value with the next-smallest unused value.

**Why counting sort works here but not always:** It needs bounded integer values. The constraint `-10^4 ≤ nums[i] ≤ 10^4` gives a fixed range of 20,001 buckets — tiny. For float values or unbounded integers, you'd use the standard sort.

**Algorithm taxonomy:** This is a greedy problem with the **exchange argument** as the proof technique. The pattern "sort, then pair/group in a regular fashion" appears constantly: pair-mins (this), pair-diffs (LC 1877), k-th smallest sum, etc.

---

## LAYER 4: Interview Variations

• **Maximize sum of `max(a, b)` per pair:**
  Take odd indices instead: `sum(sorted(nums)[1::2])`. By symmetry.

• **Maximize sum of `|a - b|` per pair:**
  Pair smallest with largest: `sum(sorted_nums[n:]) - sum(sorted_nums[:n])`.

• **Minimize the largest pair sum** (LC 1877 Minimize Maximum Pair Sum):
  Pair smallest with largest: `max(sorted[i] + sorted[2n-1-i] for i in range(n))`.

• **K-th smallest pair sum** (LC 373):
  Heap-based — different problem entirely.

• **Pair into groups of 3, maximize sum of mins:**
  Sort, take every 3rd from index 0. Same template.

• **Pair such that |a - b| ≤ K for every pair** (matching with constraint):
  Greedy still works after sort, but might be infeasible. Need to check pairs are within K.

• **Pair into n pairs, maximize product instead of sum of mins:**
  Different objective — not obviously greedy. May need DP.

• **Stream version (numbers arrive over time):**
  Maintain a sorted data structure (heap or balanced BST). On query, sum every other element.

• **What if the array length is odd (2n+1)?**
  Problem ill-defined as stated. Variations might allow "leave one out" — usually leave out the smallest.

• **Return the actual pairing, not just the sum:**
  Sort with original indices, then pair adjacent in sorted order:
  ```python
  indexed = sorted(enumerate(nums), key=lambda x: x[1])
  pairs = [(indexed[i][0], indexed[i+1][0]) for i in range(0, len(nums), 2)]
  ```

• **All values are distinct, want lexicographically smallest pairing:**
  Greedy pairing still optimal for sum; lex order is a tiebreaker on returned pairs.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — one-liner
class Solution:
    def arrayPairSum(self, nums: List[int]) -> int:
        return sum(sorted(nums)[::2])
```

**Editorial — Counting sort, O(n) when range is bounded:**
```python
def arrayPairSum(nums):
    K = 10000
    count = [0] * (2 * K + 1)
    for x in nums:
        count[x + K] += 1
    total = 0
    take = True
    for val in range(2 * K + 1):
        while count[val] > 0:
            if take:
                total += val - K
            take = not take
            count[val] -= 1
    return total
```

**Verbose sort + loop (no slice magic):**
```python
def arrayPairSum(nums):
    nums.sort()
    total = 0
    for i in range(0, len(nums), 2):
        total += nums[i]
    return total
```

**With explanation comments:**
```python
def arrayPairSum(nums):
    # Sort ascending. The min of each adjacent pair is the lower (even) index.
    # By pairing adjacent values, we minimize the "loss" of the unselected value.
    nums.sort()
    return sum(nums[::2])
```

---

**Time:** O(n log n) for standard sort, O(n + R) for counting sort
**Space:** O(1) extra (sort in place) or O(R) for counting array

**Pattern flag:** "Group n items into pairs/groups and optimize sum of a per-group function" → almost always sort first, then apply a regular grouping pattern (every-2nd, smallest-with-largest, etc.). The greedy works because the sorted order eliminates suboptimal pairings via the exchange argument.
