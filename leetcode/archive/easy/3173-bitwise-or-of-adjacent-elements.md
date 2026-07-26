# 3173. Bitwise OR of Adjacent Elements

**Difficulty:** Easy
**Pattern:** Bit Manipulation / Array Traversal
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

For each consecutive pair `(nums[i], nums[i+1])`, compute their bitwise OR and put it at `answer[i]`. Result has length `n - 1`.

**Key insight:** Simplest "pairwise sliding window of size 2" problem. Walk the array once, OR each adjacent pair. One line in Python.

Bitwise OR `|` combines bits position-by-position: bit b is set in `a | b` iff bit b is set in `a` OR in `b`. For non-negative integers, `a | b ≥ max(a, b)` — OR can only add bits.

---

## LAYER 1: Line-by-Line Explanation

**Pythonic one-liner with zip:**

```python
class Solution:
    def orArray(self, nums: List[int]) -> List[int]:
        return [a | b for a, b in zip(nums, nums[1:])]
```

- `nums` and `nums[1:]` — same array offset by one
- `zip(nums, nums[1:])` pairs them: `(nums[0], nums[1]), (nums[1], nums[2]), ...`
- List comprehension applies `|` to each pair
- Output length `n - 1` because zip stops at the shorter iterable

**Explicit loop (most readable):**

```python
class Solution:
    def orArray(self, nums: List[int]) -> List[int]:
        answer = []
        for i in range(len(nums) - 1):
            answer.append(nums[i] | nums[i + 1])
        return answer
```

`range(len(nums) - 1)` iterates `0, 1, ..., n-2` — indices where a right neighbor exists.

**Pre-allocated:**

```python
class Solution:
    def orArray(self, nums: List[int]) -> List[int]:
        n = len(nums)
        answer = [0] * (n - 1)
        for i in range(n - 1):
            answer[i] = nums[i] | nums[i + 1]
        return answer
```

---

## LAYER 2: Worked Examples

**Example 1: `nums = [1, 3, 7, 15]` → `[3, 7, 15]`**

| i | nums[i] (bin) | nums[i+1] (bin) | OR (bin) | OR (dec) |
|---|---------------|-----------------|----------|----------|
| 0 | 0001 | 0011 | 0011 | 3 |
| 1 | 0011 | 0111 | 0111 | 7 |
| 2 | 0111 | 1111 | 1111 | 15 |

"Growing bits" sequence — each value contains the previous one's bits, so `a | b = b`.

**Example 2: `nums = [8, 4, 2]` → `[12, 6]`**

| i | nums[i] | nums[i+1] | OR (bin) | OR (dec) |
|---|---------|-----------|----------|----------|
| 0 | 1000 (8) | 0100 (4) | 1100 | 12 |
| 1 | 0100 (4) | 0010 (2) | 0110 | 6 |

Disjoint bits — `a | b = a + b` when `a & b = 0`.

**Example 3: `nums = [5, 4, 9, 11]` → `[5, 13, 11]`**

| i | nums[i] | nums[i+1] | OR (bin) | OR (dec) |
|---|---------|-----------|----------|----------|
| 0 | 0101 (5) | 0100 (4) | 0101 | 5 |
| 1 | 0100 (4) | 1001 (9) | 1101 | 13 |
| 2 | 1001 (9) | 1011 (11) | 1011 | 11 |

**Walking through `zip(nums, nums[1:])` for `[5, 4, 9, 11]`:**

```
nums    = [5, 4, 9, 11]
nums[1:] = [4, 9, 11]
zip yields: (5, 4), (4, 9), (9, 11)
```

3 pairs → 3 outputs → length n - 1 = 3 ✓

**Edge cases:**
- `n = 2`: one pair, one output
- All zeros: all OR results are 0
- All same value: `[7, 7, 7]` → `[7, 7]`
- Powers of 2 (disjoint): `[1, 2, 4]` → `[3, 6]`

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| List comp with zip | O(n) | O(1) | **Pythonic, preferred** |
| Explicit loop with append | O(n) | O(1) | Most readable |
| Pre-allocated array | O(n) | O(1) | Marginal speedup |

**`zip(nums, nums[1:])` — canonical "adjacent pairs" idiom:**

- `zip(arr, arr[1:])` — consecutive pairs (size-2 window)
- `zip(arr, arr[1:], arr[2:])` — consecutive triples
- For larger windows, use `collections.deque` or `itertools` recipes

**Bitwise OR properties:**

| Identity | Why |
|----------|-----|
| `a | 0 = a` | OR with 0 is identity |
| `a | a = a` | OR with self is identity |
| `a | b = b | a` | Commutative |
| `(a | b) | c = a | (b | c)` | Associative |
| `a | b ≥ max(a, b)` | OR can only add bits |
| `a | b = a + b iff a & b = 0` | Disjoint bits → OR equals sum |

**Why `nums[i] ≤ 100` doesn't matter:**

100 fits in 7 bits. Same code works for `nums[i] ≤ 10^9`.

**Generic size-k sliding window pattern:**

```python
def window_aggregate(arr, k, op):
    return [op(arr[i:i+k]) for i in range(len(arr) - k + 1)]
```

This problem is the k=2, op=`OR` instance.

---

## LAYER 4: Interview Variations

• **Adjacent AND:** Replace `|` with `&`. `a & b ≤ min(a, b)`.
• **Adjacent XOR:** Replace with `^`. Parity of bit differences.
• **Adjacent sum/max/product:** Same pattern, different op.
• **OR over window of size k:** Naive O(n × k); sparse table or per-bit frequency counter for larger k.
• **OR of ALL elements (scalar):** `reduce(or_, nums)` or `reduce(or_, nums, 0)`.
• **Range OR queries:** Sparse table for O(1) queries (OR is idempotent + associative).
• **Maximum XOR of pair (LC 421):** Hard — uses binary trie.
• **OR of all subarrays (LC 898):** Track unique OR values; set-of-running-ORs trick.
• **Output position of max-OR pair:** Track index alongside max.
• **OR of adjacent triples:** `[a|b|c for a,b,c in zip(nums, nums[1:], nums[2:])]`. Length n - 2.
• **Alternating AND/OR:** Conditional on i % 2.
• **Streaming:** Keep last value as state; emit OR on each new arrival.
• **In-place version:** Walk left-to-right, overwrite `nums[i] = nums[i] | nums[i+1]`. Last cell unchanged.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic one-liner
class Solution:
    def orArray(self, nums: List[int]) -> List[int]:
        return [a | b for a, b in zip(nums, nums[1:])]
```

**Explicit loop:**
```python
def orArray(nums):
    answer = []
    for i in range(len(nums) - 1):
        answer.append(nums[i] | nums[i + 1])
    return answer
```

**Pre-allocated:**
```python
def orArray(nums):
    n = len(nums)
    answer = [0] * (n - 1)
    for i in range(n - 1):
        answer[i] = nums[i] | nums[i + 1]
    return answer
```

**With `operator.or_`:**
```python
from operator import or_
def orArray(nums):
    return list(map(or_, nums, nums[1:]))
```

---

**Time:** O(n) | **Space:** O(1) excluding output

**Pattern flag:** "Apply a binary operation to every adjacent pair" → `[op(a, b) for a, b in zip(arr, arr[1:])]`. The zip-pairing trick is one of the most useful Python idioms — memorize it. It generalizes to any pairwise computation.
