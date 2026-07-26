# 1920. Build Array from Permutation

**Difficulty:** Easy
**Pattern:** Array Simulation / In-Place Encoding Trick
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

For each index `i`, compute `ans[i] = nums[nums[i]]` — a one-step double dereference.

Two layers:
1. **Easy reading:** Direct formula application
2. **Follow-up (O(1) space):** Can't just overwrite `nums[i]` because values you still need will be destroyed. Solve with **bit packing** — encode both old and new values into one cell using modular arithmetic.

---

## LAYER 1: Line-by-Line Explanation

**Editorial one-liner:**

```python
class Solution:
    def buildArray(self, nums: List[int]) -> List[int]:
        n = len(nums)
        return [nums[nums[_]] for _ in range(n)]
```

The `_` is the loop index (Python convention for unused variable, though it IS used here as the index — unusual styling).

**Cleaner spelling:**

```python
class Solution:
    def buildArray(self, nums: List[int]) -> List[int]:
        return [nums[nums[i]] for i in range(len(nums))]
```

**Tightest Pythonic version:**

```python
class Solution:
    def buildArray(self, nums: List[int]) -> List[int]:
        return [nums[v] for v in nums]
```

"For each value v in nums, return nums[v]." Same indices, cleaner syntax. `nums[nums[i]]` can be rewritten as `nums[v] where v = nums[i]`.

**O(1) space — In-place bit packing (the follow-up):**

```python
class Solution:
    def buildArray(self, nums: List[int]) -> List[int]:
        n = len(nums)
        # Pass 1: pack (old, new) into each cell as old + n*new
        for i in range(n):
            nums[i] += n * (nums[nums[i]] % n)
        # Pass 2: extract new value
        for i in range(n):
            nums[i] //= n
        return nums
```

**The packing trick:**

Each cell stores TWO numbers using base-n arithmetic:
- Old value = `nums[i] % n` (low digit)
- New value = `nums[i] // n` (high digit)

When we write `nums[i] = old + n*new`:
- `% n` recovers `old`
- `// n` recovers `new`

This lets us write new values WITHOUT destroying old until done.

**Why `nums[nums[i]] % n`?** Because by the time we process index i, the cell at `nums[i]` may have already been packed. Taking `% n` recovers the original old value.

---

## LAYER 2: Worked Examples

**Example 1: `nums = [0,2,1,5,3,4]` → `[0,1,2,4,5,3]`**

| i | nums[i] | nums[nums[i]] |
|---|---------|---------------|
| 0 | 0 | nums[0] = 0 |
| 1 | 2 | nums[2] = 1 |
| 2 | 1 | nums[1] = 2 |
| 3 | 5 | nums[5] = 4 |
| 4 | 3 | nums[3] = 5 |
| 5 | 4 | nums[4] = 3 |

Result: `[0,1,2,4,5,3]` ✓

**Example 2: `nums = [5,0,1,2,3,4]` → `[4,5,0,1,2,3]`**

| i | nums[i] | nums[nums[i]] |
|---|---------|---------------|
| 0 | 5 | nums[5] = 4 |
| 1 | 0 | nums[0] = 5 |
| 2 | 1 | nums[1] = 0 |
| 3 | 2 | nums[2] = 1 |
| 4 | 3 | nums[3] = 2 |
| 5 | 4 | nums[4] = 3 |

This is "shift left by 1" applied twice → "shift left by 2."

**Why hint 2 matters — naive overwrite fails:**

Try `nums[i] = nums[nums[i]]` on Example 1:

| i | nums BEFORE | nums[i] | nums[nums[i]] | nums AFTER |
|---|-------------|---------|---------------|------------|
| 0 | [0,2,1,5,3,4] | 0 | nums[0]=0 | [0,2,1,5,3,4] |
| 1 | [0,2,1,5,3,4] | 2 | nums[2]=1 | [0,1,1,5,3,4] |
| 2 | [0,1,1,5,3,4] | 1 | nums[1]=1 ⚠️ | [0,1,1,5,3,4] |

At i=2, we read nums[1] which was JUST overwritten → wrong value (1 instead of 2).

**Bit-packing trace on Example 1, n=6:**

Pass 1:

| i | nums[i] (old) | target=nums[nums[i]]%6 | packed | nums after |
|---|---------------|-----------------------|--------|------------|
| 0 | 0 | nums[0]%6=0 | 0+6×0=0 | [0,2,1,5,3,4] |
| 1 | 2 | nums[2]%6=1 | 2+6×1=8 | [0,8,1,5,3,4] |
| 2 | 1 | nums[1]%6 → 8%6=2 ✓ | 1+6×2=13 | [0,8,13,5,3,4] |
| 3 | 5 | nums[5]%6=4 | 5+6×4=29 | [0,8,13,29,3,4] |
| 4 | 3 | nums[3]%6=29%6=5 ✓ | 3+6×5=33 | [0,8,13,29,33,4] |
| 5 | 4 | nums[4]%6=33%6=3 ✓ | 4+6×3=22 | [0,8,13,29,33,22] |

Pass 2 (`//= 6`):

| i | packed | // 6 |
|---|--------|------|
| 0 | 0 | 0 |
| 1 | 8 | 1 |
| 2 | 13 | 2 |
| 3 | 29 | 4 |
| 4 | 33 | 5 |
| 5 | 22 | 3 |

Final: `[0,1,2,4,5,3]` ✓

**Edge cases:**
- `nums = [0]`: only valid 1-element permutation → `[0]`
- Identity `[0,1,2,3]`: `ans[i] = nums[i] = i` → unchanged
- `[3,2,1,0]` (n=4): all swap with their mirror → `[0,1,2,3]` (identity, since reverse composed with itself is identity)

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| List comprehension | O(n) | O(n) result | **Default** |
| Plain loop with new array | O(n) | O(n) | Same complexity |
| **In-place bit packing** | **O(n)** | **O(1)** | **Solves follow-up** |
| Naive overwrite | O(n) | O(1) | ❌ WRONG |

**The bit-packing trick is the real lesson:**

The Easy version teaches list comprehensions; the follow-up teaches a much more important pattern: **storing two values in one cell using base-n encoding.**

Generalizes to other "in-place transform with dependencies":
- LC 41 First Missing Positive (uses negation marker)
- LC 287 Find Duplicate Number
- LC 442 Find All Duplicates (negation marker)
- Any problem with "modify in place, but new values conflict with reads"

**The general pattern:**

> If your value range is `[0, k)` and you need to store both old + new at same cell, use `cell = old + k*new`. Decode with `% k` and `// k`.

This is just base-k positional notation.

**Math:** For non-negative `x` with `0 ≤ x < k`:
- `(x + k*y) % k = x`
- `(x + k*y) // k = y`

Lossless as long as `y` doesn't overflow. Here values ∈ [0, n), so `n*n - 1` is max packed value. For n ≤ 1000, n² ≤ 10⁶ — well within int range.

**Python advantage:** Unbounded int precision means no overflow worries. C++/Java need to check `n*n` fits in int32.

**Permutation algebra interpretation:**

`nums` as function `f: index → value`. Then `nums[nums[i]] = f(f(i)) = f²`. Function composition with itself. Classic operation in group theory.

---

## LAYER 4: Interview Variations

• **Apply transform k times:** Naive O(n*k). Better: detect cycles, exploit periodicity. For k = 2^m, repeated squaring of permutation.
• **Find g with g(g(i)) == nums[i] (square root of permutation):** Cycle decomposition; even-cycles pair, odd-cycles direct.
• **Apply different permutation g (compute nums[g[i]]):** `[nums[g[i]] for i in range(n)]`.
• **In-place when values exceed range:** Bit-packing fails; use sentinel marker.
• **Transform only at even indices:** Filter by index parity.
• **Verify nums IS valid permutation first:** `sorted(nums) == list(range(len(nums)))`.
• **Cyclic indexing (i+k) % n:** Different — modular indexing.
• **2D grid version:** Cell values reference other cells; bit-packing works on row*cols + col.
• **Output inverse permutation:** `for i: ans[nums[i]] = i`.
• **Cycle detection:** Walk each index until return.
• **Memory-constrained C (n=10^9):** Bit-packing works if n*n fits 64-bit.
• **Result must be sorted:** Apply then sort, or recognize special permutations.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic one-liner
class Solution:
    def buildArray(self, nums: List[int]) -> List[int]:
        return [nums[v] for v in nums]
```

**Editorial style:**
```python
class Solution:
    def buildArray(self, nums: List[int]) -> List[int]:
        n = len(nums)
        return [nums[nums[i]] for i in range(n)]
```

**Explicit loop:**
```python
def buildArray(nums):
    ans = []
    for i in range(len(nums)):
        ans.append(nums[nums[i]])
    return ans
```

**O(1) space — bit-packing (follow-up):**
```python
class Solution:
    def buildArray(self, nums: List[int]) -> List[int]:
        n = len(nums)
        for i in range(n):
            nums[i] += n * (nums[nums[i]] % n)
        for i in range(n):
            nums[i] //= n
        return nums
```

**Map-style:**
```python
def buildArray(nums):
    return list(map(lambda v: nums[v], nums))
```

---

**Time:** O(n) | **Space:** O(n) result or O(1) extra with bit-packing

**Pattern flag:** "Compute new array values from old, where dependencies might destroy data" → either (1) use a fresh output array (simple, O(n) space) or (2) **bit-pack old + new in same cell using base-k encoding** when k = value_range. Encoding `cell = old + k*new`, decode with `% k` / `// k` is one of the most useful in-place tricks in competitive programming.
