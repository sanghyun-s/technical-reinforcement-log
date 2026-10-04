# 338. Counting Bits

**Difficulty:** Easy
**Pattern:** Dynamic Programming / Bit Manipulation
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

For every integer `i` from 0 to `n`, count the number of 1-bits (the "popcount" or Hamming weight) in its binary representation. Return all results in an array.

**Key insight:** The editorial gives an O(n log n) solution — it processes each number independently in O(log n) time. The follow-up specifically asks for O(n), which requires a DP recurrence: **use the popcount of smaller numbers to compute the current one in O(1)**.

There are several elegant O(n) recurrences. The simplest is:

> `ans[i] = ans[i >> 1] + (i & 1)`

Read: "the number of 1s in `i` equals the number of 1s in `i/2`, plus 1 if `i` is odd." Right-shifting by 1 just drops the last bit, so the count of 1s differs by exactly that bit.

---

## LAYER 1: Line-by-Line Explanation

**Editorial's solution (O(n log n) — Brian Kernighan's trick):**

```python
class Solution:
    def countBits(self, n: int) -> List[int]:

        def pop_count(x: int) -> int:
            count = 0
            while x != 0:
                x &= x - 1              # Clear the lowest set bit
                count += 1              # Count that bit
            return count

        ans = [0] * (n + 1)
        for x in range(n + 1):
            ans[x] = pop_count(x)
        return ans
```

**The `x &= x - 1` trick:** Subtracting 1 from `x` flips its lowest set bit AND all the zeros below it. AND-ing with the original `x` zeros out that lowest set bit (and only that bit). Each iteration removes exactly one set bit, so the loop runs once per 1-bit in `x` — that's the count.

Example: `x = 12 = 1100`
- iter 1: `x = 1100 & 1011 = 1000` (count=1)
- iter 2: `x = 1000 & 0111 = 0000` (count=2)
- done → 2 ones ✓

**Optimal O(n) solution (DP):**

```python
class Solution:
    def countBits(self, n: int) -> List[int]:
        ans = [0] * (n + 1)
        for i in range(1, n + 1):
            ans[i] = ans[i >> 1] + (i & 1)
        return ans
```

**Why this works:**
- `i >> 1` is integer division by 2, which drops the rightmost bit
- `i & 1` extracts that rightmost bit (1 if odd, 0 if even)
- So `popcount(i) = popcount(i without last bit) + (last bit)`

Each entry uses one already-computed entry → O(1) per number → O(n) total.

---

## LAYER 2: Worked Examples

**Example 1: `n = 2` → `[0, 1, 1]`**

| i | binary | i >> 1 | ans[i>>1] | i & 1 | ans[i] |
|---|--------|--------|-----------|-------|--------|
| 0 | 0      | —      | —         | —     | 0      |
| 1 | 1      | 0      | 0         | 1     | 0+1=1  |
| 2 | 10     | 1      | 1         | 0     | 1+0=1  |

Output: `[0, 1, 1]` ✓

**Example 2: `n = 5` → `[0, 1, 1, 2, 1, 2]`**

| i | binary | i >> 1 | ans[i>>1] | i & 1 | ans[i] |
|---|--------|--------|-----------|-------|--------|
| 0 | 0      | —      | —         | —     | 0      |
| 1 | 1      | 0      | 0         | 1     | 1      |
| 2 | 10     | 1      | 1         | 0     | 1      |
| 3 | 11     | 1      | 1         | 1     | 2      |
| 4 | 100    | 2      | 1         | 0     | 1      |
| 5 | 101    | 2      | 1         | 1     | 2      |

Output: `[0, 1, 1, 2, 1, 2]` ✓

**Walkthrough of the alternative recurrence** `ans[i] = ans[i & (i-1)] + 1`:

For `i = 5 = 101`:
- `i - 1 = 4 = 100`
- `i & (i-1) = 101 & 100 = 100 = 4`
- `ans[5] = ans[4] + 1 = 1 + 1 = 2` ✓

This uses Brian Kernighan's trick as a recurrence: "popcount(i) = popcount(i with lowest 1 removed) + 1".

**Tracing Brian Kernighan's trick on `x = 13 = 1101`:**
- iter 1: `1101 & 1100 = 1100` (count=1)
- iter 2: `1100 & 1011 = 1000` (count=2)
- iter 3: `1000 & 0111 = 0000` (count=3)
- done → 3 ones ✓

**Edge cases:**
- `n = 0` → `[0]` (only count for 0)
- `n = 1` → `[0, 1]`
- Powers of 2 always have popcount = 1 (`100`, `1000`, `10000`...)
- `n = 2^k - 1` (all 1s): popcount = k

---

## LAYER 3: Key Insights

| Approach | Time | Space (output excluded) | Notes |
|----------|------|-------------------------|-------|
| Brian Kernighan per number (editorial) | O(n log n) | O(1) | Easy to understand |
| Naive bit shifting per number | O(n log n) | O(1) | Same complexity |
| DP: `ans[i>>1] + (i&1)` | **O(n)** | **O(1)** | **Optimal, simplest** |
| DP: `ans[i & (i-1)] + 1` | O(n) | O(1) | Equally optimal |
| DP: `ans[i - 2^k] + 1` (highest power) | O(n) | O(n) tracking | More complex |
| `bin(i).count('1')` per number | O(n log n) | O(log n) per call | One-liner, slow |

**The three classic O(n) recurrences (all valid, all elegant):**

1. **Last-bit removal** (recommended): `ans[i] = ans[i >> 1] + (i & 1)`
2. **Lowest-set-bit removal**: `ans[i] = ans[i & (i-1)] + 1`
3. **Highest-power-of-2 offset**: `ans[i] = ans[i - largest_pow2(i)] + 1`

All three say "use a known smaller answer, do one O(1) operation." The first is cleanest because `i >> 1 < i` for all i ≥ 1, so the recurrence is trivially valid.

**Why the O(n log n) solution still passes:**
Even at n = 100,000, log n ≈ 17, so ~1.7M operations. Fast in Python. But the follow-up exists because in real systems (large n, embedded constraints) the O(n) version is meaningfully better.

**The deeper pattern this teaches:**
Whenever you compute a property for every integer in a range, ask: *can I express `f(i)` in terms of `f(smaller_i)` in O(1)?* If yes, that's an O(n) DP. This template solves dozens of "count something for every i" problems.

---

## LAYER 4: Interview Variations

• **Count trailing zeros for every i in [0, n]:**
  `ans[i] = ans[i >> 1] + 1 if i even else 0`. Same template.

• **Count 1-bits for a single integer `x`** (LC 191 Hamming Weight):
  Use Brian Kernighan's trick directly — O(set bits) which is faster than O(log n) for sparse numbers.

• **Hamming distance between two integers** (LC 461):
  `popcount(x XOR y)`. The XOR isolates positions where bits differ.

• **Total Hamming distance across an array** (LC 477):
  For each bit position 0–31, count how many numbers have that bit set (`k`). Contribution = `k * (n - k)`. Total = sum across positions.

• **Sort integers by popcount, ties by value** (LC 1356):
  `sorted(nums, key=lambda x: (bin(x).count('1'), x))`.

• **First number > n with same popcount:**
  Bit trick problem ("next permutation of bits") — used in low-level systems.

• **Count integers in [0, n] with exactly k bits set:**
  Could iterate and filter, or use combinatorial counting (digit DP on binary).

• **Build the array using only addition, no bitwise ops:**
  `ans[i] = ans[i // 2] + (i % 2)`. Equivalent.

• **Streaming version (n arrives, must answer queries about popcount of integers seen so far):**
  Precompute the array once; queries are O(1).

• **Variable bit-width (32-bit, 64-bit, BigInt):**
  Same recurrence works for any width. Python handles arbitrary precision natively.

• **Parallel/SIMD popcount:**
  Modern CPUs have a `POPCNT` instruction. C++ uses `__builtin_popcount`. Python's `int.bit_count()` (since 3.10) maps to it.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — O(n), last-bit recurrence
class Solution:
    def countBits(self, n: int) -> List[int]:
        ans = [0] * (n + 1)
        for i in range(1, n + 1):
            ans[i] = ans[i >> 1] + (i & 1)
        return ans
```

**Alternative O(n) — Brian Kernighan recurrence:**
```python
def countBits(n):
    ans = [0] * (n + 1)
    for i in range(1, n + 1):
        ans[i] = ans[i & (i - 1)] + 1
    return ans
```

**Editorial — O(n log n), Brian Kernighan per number:**
```python
def countBits(n):
    def pop_count(x):
        count = 0
        while x:
            x &= x - 1
            count += 1
        return count
    return [pop_count(i) for i in range(n + 1)]
```

**One-liner (slow but compact):**
```python
def countBits(n):
    return [bin(i).count('1') for i in range(n + 1)]
```

**Python 3.10+ built-in:**
```python
def countBits(n):
    return [i.bit_count() for i in range(n + 1)]
```

---

**Time:** O(n) | **Space:** O(1) excluding output

**Pattern flag:** "Compute property f for every integer in [0, n]" → first try to find a recurrence `f(i) = f(g(i)) + small_correction` where `g(i) < i`. Bit problems often have this structure via `i >> 1` or `i & (i-1)`.
