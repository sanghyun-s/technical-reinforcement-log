# 1863. Sum of All Subset XOR Totals

**Difficulty:** Easy
**Pattern:** Enumeration / Bit Manipulation / Combinatorics
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

Given an array, sum the XOR-totals over **every possible subset** (including empty). For `n` elements there are `2^n` subsets — each contributes its XOR-of-all-elements to the answer.

**Key insight (the bitwise magic):** The answer equals `OR(nums) << (n - 1)` — that's it.

Let me unpack why. Look at any **bit position** (say bit `b`):
- If NO element has bit `b` set, no subset's XOR has bit `b` set → contributes 0.
- If at least ONE element has bit `b` set, then **exactly half of all subsets** XOR to a value with bit `b` set. With `n` elements, that's `2^(n-1)` subsets contributing `2^b` each.

So the contribution of bit `b` to the total = (bit `b` appears in OR of array) × `2^(n-1)` × `2^b`.

Sum across all bits: `OR(nums) × 2^(n-1)` = `OR(nums) << (n - 1)`.

The editorial uses recursive enumeration to teach subset generation — that's the pedagogical path. The bitwise shortcut is what you'd write in production.

---

## LAYER 1: Line-by-Line Explanation

**Editorial — Recursive subset enumeration:**

```python
class Solution:
    def subsetXORSum(self, nums):
        def generate_subsets(nums, index, subset, subsets):
            # Base case: we've decided include/exclude for every element
            if index == len(nums):
                subsets.append(subset[:])     # copy current subset
                return

            # Branch 1: include nums[index]
            subset.append(nums[index])
            generate_subsets(nums, index + 1, subset, subsets)
            subset.pop()                       # backtrack

            # Branch 2: exclude nums[index]
            generate_subsets(nums, index + 1, subset, subsets)

        subsets = []
        generate_subsets(nums, 0, [], subsets)

        # Sum XOR-totals across all subsets
        result = 0
        for subset in subsets:
            xor_total = 0
            for num in subset:
                xor_total ^= num
            result += xor_total
        return result
```

**Why `subset[:]` (the copy):** `subset` is a single shared list that we mutate via append/pop as we recurse. Without `[:]`, all subsets in `subsets` would point to the same list, ending up empty when recursion finishes.

**The recursion tree:** Each call splits into "take this element" / "skip this element" → a binary tree of depth `n` and `2^n` leaves. Each leaf is a complete subset.

**One-line bitwise solution (the elegant one):**

```python
class Solution:
    def subsetXORSum(self, nums: List[int]) -> int:
        from functools import reduce
        from operator import or_
        return reduce(or_, nums) << (len(nums) - 1)
```

- `reduce(or_, nums)` — fold the OR operator across the array: `nums[0] | nums[1] | ... | nums[n-1]`
- `<< (n - 1)` — multiply by `2^(n-1)`
- Combined: `OR(nums) × 2^(n-1)`

**Iterative XOR aggregation (efficient middle ground):**

```python
class Solution:
    def subsetXORSum(self, nums: List[int]) -> int:
        total_or = 0
        for x in nums:
            total_or |= x
        return total_or << (len(nums) - 1)
```

Same as the reduce version but without imports — equally fast, more readable.

---

## LAYER 2: Worked Examples

**Example 1: `nums = [1, 3]` → 6**

*Enumeration approach:*
| subset | XOR |
|--------|-----|
| [] | 0 |
| [1] | 1 |
| [3] | 3 |
| [1,3] | 1 ^ 3 = 2 |

Sum: 0 + 1 + 3 + 2 = 6 ✓

*Bitwise approach:*
- `OR(1, 3) = 0b01 | 0b11 = 0b11 = 3`
- `n = 2`, `2^(n-1) = 2`
- Answer: `3 << 1 = 6` ✓

*Bit-by-bit verification:*
- **Bit 0** (value 1): set in both `1` and `3`. So bit 0 is "available." Half of 4 subsets have XOR with bit 0 set → 2 subsets × 1 = **2**
- **Bit 1** (value 2): set in `3` only. Half of 4 subsets have XOR with bit 1 set → 2 subsets × 2 = **4**
- **Bit 2+**: not set in any element → 0

Total: 2 + 4 = 6 ✓

**Example 2: `nums = [5, 1, 6]` → 28**

*Bitwise approach:*
- `OR(5, 1, 6) = 0b101 | 0b001 | 0b110 = 0b111 = 7`
- `n = 3`, `2^(n-1) = 4`
- Answer: `7 << 2 = 28` ✓

*Bit-by-bit verification:*
- **Bit 0** (value 1): set in `5` and `1`. With n=3, half of 8 = 4 subsets have bit 0 set in their XOR → 4 × 1 = **4**
- **Bit 1** (value 2): set in `6` only. 4 subsets → 4 × 2 = **8**
- **Bit 2** (value 4): set in `5` and `6`. 4 subsets → 4 × 4 = **16**

Total: 4 + 8 + 16 = 28 ✓

**Why exactly half of all subsets have bit b set (the key claim):**

Suppose at least one element has bit b set; call it `x`. Pair up subsets like this:
- For every subset `S` that does NOT contain `x`, pair it with `S ∪ {x}`.
- The XORs of these two subsets differ in exactly bit b (XOR-ing in `x` flips bit b, since `x` has bit b set).
- So exactly one of each pair has bit b set in its XOR.
- The pairing covers all `2^n` subsets without overlap → exactly `2^(n-1)` subsets have bit b set.

This is a beautiful proof using a bijection.

**Example 3: `nums = [3,4,5,6,7,8]` → 480**

`3|4 = 7`, `7|5 = 7`, `7|6 = 7`, `7|7 = 7`, `7|8 = 15`. So `OR = 15`.

`n = 6`, `2^(n-1) = 32`. Answer: `15 × 32 = 480` ✓

**Recursion trace for `nums = [1, 3]` (editorial approach):**

```
gen(0, [])
├── append 1, gen(1, [1])
│   ├── append 3, gen(2, [1,3]) → save [1,3]
│   └── (pop 3), gen(2, [1]) → save [1]
├── (pop 1), gen(1, [])
│   ├── append 3, gen(2, [3]) → save [3]
│   └── (pop 3), gen(2, []) → save []
```

Subsets collected: `[[1,3], [1], [3], []]` ✓

**Edge cases:**
- Single element `nums = [7]`: `OR = 7`, `n-1 = 0`, answer = `7 << 0 = 7`. Check: subsets `[]` and `[7]` → 0 + 7 = 7 ✓
- Powers of 2 `nums = [1,2,4]`: `OR = 7`, `n-1 = 2`, answer = `7 × 4 = 28`.
- All zeros `nums = [0,0,0]` (not in constraints but illustrative): `OR = 0`, answer = 0.

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Recursive enumeration (editorial) | O(n × 2^n) | O(n × 2^n) for storage | Teaches subsets |
| Iterative bitmask enumeration | O(n × 2^n) | O(1) extra | Same complexity, less memory |
| Recursive XOR-accumulation (no storage) | O(2^n) | O(n) recursion stack | Removes the per-subset list |
| **Bitwise OR trick** | **O(n)** | **O(1)** | **Optimal — the magic solution** |

**The brilliant observation (worth deeply internalizing):**

For ANY bit position, the contribution to the total sum depends on a single yes/no: *does any element have this bit set?* If yes, that bit contributes `2^(n-1) × 2^b` to the total. If no, it contributes 0.

The OR of all elements is exactly the "yes" set: bit b in OR = 1 iff some element has bit b set.

Multiplying OR by `2^(n-1)` (which is `<< (n-1)`) is a single instruction.

**Why the editorial doesn't use this trick:** Pedagogy. The problem is filed under "Enumeration" — the intent is to practice generating subsets via recursion. The bitwise trick is a *consequence* of analyzing the problem deeply, not a way to learn enumeration.

**Iterative bitmask enumeration (worth knowing for subset problems):**

```python
def subsetXORSum(nums):
    n = len(nums)
    total = 0
    for mask in range(1 << n):
        xor = 0
        for i in range(n):
            if mask & (1 << i):
                xor ^= nums[i]
        total += xor
    return total
```

Each integer from 0 to `2^n - 1` represents a subset: bit i means "include nums[i]." Cleaner than recursion. Same O(n × 2^n).

**General pattern this teaches:**

When asked to aggregate over all subsets:
1. **Try enumeration first** (works for small n ≤ 20).
2. **Then analyze per-bit contribution** — does each bit have a clean formula?
3. **Then look for a global identity** like OR/AND/SUM that captures the answer.

Many subset-sum and subset-XOR problems collapse into one-line formulas this way.

---

## LAYER 4: Interview Variations

• **Sum of all subset SUMS** (not XOR):
  Each element appears in exactly half of all subsets. Answer = `sum(nums) × 2^(n-1)`.

• **Sum of all subset AND-totals:**
  Bit b appears in XOR of subset S iff ALL of S has bit b. For bit b set in element count `k`, the subsets where AND has bit b set = `2^k` (any subset of just those k elements, plus optional inclusion of zero-bit-b elements... wait, no — they wipe the AND). Trickier — bit b is set in subset's AND iff every chosen element has bit b. Need precise counting; not a simple OR identity.

• **Sum of all subset OR-totals:**
  Bit b in OR of subset = 1 iff at least one chosen element has bit b. Count subsets containing ≥1 element with bit b: `2^n - 2^(n-k_b)` where k_b is the count of elements with bit b set. Sum: `sum over b of 2^b × (2^n - 2^(n-k_b))`.

• **Maximum XOR-total over all subsets** (LC 1829 / Gaussian elimination):
  Different problem — uses linear algebra over GF(2).

• **Minimum XOR-total over all NON-EMPTY subsets:**
  Always 0 if any duplicate exists (take two of the same element); otherwise the smallest single element.

• **Count subsets with XOR-total = k:**
  DP over bits or Walsh-Hadamard transform. Much harder.

• **Sum of subset products:**
  `prod((1 + a_i)) - 1` if you include only non-empty. (1 + x_i) expands to "include or exclude x_i" for each i, the -1 removes the empty subset.

• **Constraints push to n = 100:**
  Enumeration explodes (2^100). The OR trick still works in O(n) — that's why having both approaches in your toolkit matters.

• **What if subsets must be contiguous (subarrays)?**
  Completely different problem (no longer 2^n subsets, only n × (n+1) / 2). Need prefix XOR tricks.

• **Weighted XOR sum (each subset has a weight w(S)):**
  Depends on weight function. Often requires DP if weights are arbitrary.

• **XOR sum over multisets (with duplicates) where each element has bounded multiplicity:**
  Extends OR identity by counting multiplicities.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — O(n), bitwise OR identity
class Solution:
    def subsetXORSum(self, nums: List[int]) -> int:
        running_or = 0
        for x in nums:
            running_or |= x
        return running_or << (len(nums) - 1)
```

**One-liner with reduce:**
```python
from functools import reduce
from operator import or_
def subsetXORSum(nums):
    return reduce(or_, nums) << (len(nums) - 1)
```

**Iterative bitmask enumeration — O(n × 2^n):**
```python
def subsetXORSum(nums):
    n = len(nums)
    total = 0
    for mask in range(1 << n):
        xor = 0
        for i in range(n):
            if mask & (1 << i):
                xor ^= nums[i]
        total += xor
    return total
```

**Recursive enumeration (editorial style, simplified):**
```python
def subsetXORSum(nums):
    def helper(i, current_xor):
        if i == len(nums):
            return current_xor
        return helper(i + 1, current_xor) + helper(i + 1, current_xor ^ nums[i])
    return helper(0, 0)
```

This is much cleaner than the editorial's "build all subsets then iterate" — it XORs on the fly during recursion.

---

**Time:** O(n) | **Space:** O(1)

**Pattern flag:** Aggregating across all subsets of an n-element array (n ≤ 20)? First instinct: enumerate via bitmask `for mask in range(1 << n)`. Then ask: *can I compute per-bit contribution independently?* If yes, you'll often collapse to a one-line bitwise formula like this one. The OR-shift identity is a classic.
