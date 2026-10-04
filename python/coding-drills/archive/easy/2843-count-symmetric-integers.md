# 2843. Count Symmetric Integers

**Difficulty:** Easy
**Pattern:** Enumeration / Digit Manipulation
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

For each number in `[low, high]`, check if it has an even number of digits AND the sum of the first half equals the sum of the second half. Count how many qualify.

Constraints cap `high` at 10⁴, so the only candidates are:
- **2-digit numbers** (10–99): symmetric iff digit1 == digit2
- **4-digit numbers** (1000–9999): symmetric iff sum of first 2 digits == sum of last 2 digits
- **1-digit** (1–9) and **3-digit** (100–999): always asymmetric (odd digit count)

**Two clean approaches:**
1. **String approach** (Pythonic, generalizes to any digit count): convert to string, slice in half, compare digit sums
2. **Arithmetic approach** (editorial): extract digits via `//` and `%`, handle 2-digit and 4-digit cases separately

The arithmetic version uses a slick trick for 2-digit numbers: a 2-digit number is symmetric iff it's divisible by 11 (since `10a + a = 11a`).

---

## LAYER 1: Line-by-Line Explanation

**Editorial — Arithmetic approach with digit extraction:**

```python
class Solution:
    def countSymmetricIntegers(self, low: int, high: int) -> int:
        res = 0
        for a in range(low, high + 1):
            # 2-digit numbers: 11, 22, ..., 99 → multiples of 11 in [10, 99]
            if a < 100 and a % 11 == 0:
                res += 1
            # 4-digit numbers: compare sum of first 2 digits to last 2 digits
            if 1000 <= a < 10000:
                left = a // 1000 + a % 1000 // 100
                right = a % 100 // 10 + a % 10
                if left == right:
                    res += 1
        return res
```

**Decoding the digit extraction (for `a = 1234`):**

| expression | meaning | result |
|------------|---------|--------|
| `a // 1000` | thousands digit | 1 |
| `a % 1000` | last 3 digits | 234 |
| `a % 1000 // 100` | hundreds digit | 2 |
| `a % 100` | last 2 digits | 34 |
| `a % 100 // 10` | tens digit | 3 |
| `a % 10` | ones digit | 4 |

So `left = 1 + 2 = 3`, `right = 3 + 4 = 7`. Not symmetric. ✓

**The 11-trick for 2-digit numbers:**

A 2-digit number with digits `(a, a)` (same digit twice) is `10a + a = 11a`. So 2-digit symmetric numbers are exactly the multiples of 11 in [11, 99]: 11, 22, 33, 44, 55, 66, 77, 88, 99 — nine numbers.

**Important:** This trick is special to 2-digit numbers because "digit sums equal" simplifies to "digits equal" (single digits on each side). It does NOT extend to 4-digit numbers — e.g., 1203 is symmetric (1+2 = 0+3) but not a multiple of 1001 or anything simple.

**Pythonic string approach:**

```python
class Solution:
    def countSymmetricIntegers(self, low: int, high: int) -> int:
        count = 0
        for x in range(low, high + 1):
            s = str(x)
            if len(s) % 2 == 0:
                half = len(s) // 2
                if sum(map(int, s[:half])) == sum(map(int, s[half:])):
                    count += 1
        return count
```

- `str(x)` converts to digit string
- `len(s) % 2 == 0` filters out odd-length numbers immediately
- `s[:half]` and `s[half:]` slice into two halves
- `map(int, ...)` turns each character digit into an int
- `sum(...)` aggregates each half

This version generalizes — it would work for 6-digit or 8-digit numbers too, no rewrite needed.

---

## LAYER 2: Worked Examples

**Example 1: `low = 1, high = 100` → 9**

Iterate 1..100. Only 2-digit candidates matter.

Multiples of 11 in [10, 99]: 11, 22, 33, 44, 55, 66, 77, 88, 99 → **9** ✓

(100 itself is 3 digits → automatically asymmetric.)

**Example 2: `low = 1200, high = 1230` → 4**

All 4-digit. Compute left and right halves:

| x | digits | left (d1+d2) | right (d3+d4) | symmetric? |
|---|--------|--------------|---------------|------------|
| 1200 | 1,2,0,0 | 3 | 0 | ❌ |
| 1201 | 1,2,0,1 | 3 | 1 | ❌ |
| 1202 | 1,2,0,2 | 3 | 2 | ❌ |
| **1203** | 1,2,0,3 | 3 | 3 | ✅ |
| 1204 | 1,2,0,4 | 3 | 4 | ❌ |
| ... | ... | ... | ... | ... |
| **1212** | 1,2,1,2 | 3 | 3 | ✅ |
| ... | ... | ... | ... | ... |
| **1221** | 1,2,2,1 | 3 | 3 | ✅ |
| ... | ... | ... | ... | ... |
| **1230** | 1,2,3,0 | 3 | 3 | ✅ |

Four matches: 1203, 1212, 1221, 1230 → **4** ✓

**Walkthrough of arithmetic on `a = 1203`:**
- `left = 1203 // 1000 + 1203 % 1000 // 100 = 1 + (203 // 100) = 1 + 2 = 3`
- `right = 1203 % 100 // 10 + 1203 % 10 = (3 // 10) + 3 = 0 + 3 = 3`
- `left == right` → counted ✓

**Walkthrough of string approach on `a = 1212`:**
- `s = "1212"`, `len = 4`, `half = 2`
- `s[:2] = "12"` → digit sum = 1+2 = 3
- `s[2:] = "12"` → digit sum = 1+2 = 3
- Equal → counted ✓

**Edge cases:**
- `low = high = 11` → 1 (just 11)
- `low = high = 100` → 0 (3-digit, never symmetric)
- `low = 1, high = 9` → 0 (all 1-digit)
- `low = high = 9999` → 1 (9+9 = 9+9 ✓)

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Editorial (arithmetic + 11-trick) | O(n) where n = high - low | O(1) | Fast in practice |
| String slicing | O(n × d) where d = digit count | O(d) per iteration | Cleanest, generalizes |
| Pure 11-trick + 4-digit math | O(n) | O(1) | Same as editorial |
| Pre-compute and filter (counting approach) | O(d²) precompute + O(1) query | O(d²) | Overkill for n ≤ 10⁴ |

**Why divisibility-by-11 works ONLY for 2-digit:**

For 2-digit `ab`: value = `10a + b`. Symmetric means `a == b`, i.e., value = `11a`. So divisibility by 11 in [10, 99] captures exactly the symmetric ones.

For 4-digit `abcd`: value = `1000a + 100b + 10c + d`. Symmetric means `a + b == c + d`, which does NOT translate to divisibility by any single number. (For instance, 1203 satisfies the digit-sum condition but `1203 / 11 ≈ 109.4`, not integer.)

So the 11-trick is a clever 2-digit shortcut, not a general technique. The editorial uses it just because it's slightly faster than digit extraction for the 2-digit case.

**Why string approach is interview-preferable:**

- Generalizes to any digit count without rewriting
- Reads almost like the problem statement
- Avoids the trap of memorizing the 11-trick incorrectly
- Slightly slower (string conversion) but well within time limits

The arithmetic approach is more "C-style" — useful in languages without easy string operations, or when you really need every cycle.

**Why we don't need a smarter algorithm:**

Constraints cap `high - low + 1` at ~10⁴. Per-number work is O(1) (constant digit count). Total: ~10⁴ ops. Even Python handles this instantly.

For larger ranges (e.g., `high ≤ 10^18`), you'd need **digit DP** — count symmetric numbers in [0, n] using a state machine over digits. That's a Medium/Hard variation; the constraint here keeps it Easy.

---

## LAYER 4: Interview Variations

• **Symmetric integers with 6 or 8 digits** (range expanded to 10^6 or 10^8):
  String approach generalizes for free — just remove the hardcoded 4-digit handling. Arithmetic approach needs more cases.

• **Range up to 10^18** (way too large to iterate):
  Use **digit DP**. State: (position, digits placed so far, running sum_left - sum_right, tight constraint). Returns count of valid completions.

• **Sum equals half-sum (not equal halves):**
  Different variant — `sum(first_n) == sum(last_n) == total/2`. Easy modification.

• **Palindromic integers** (digits read the same forwards/backwards):
  Different problem — character-level symmetry, not sum-level. `s == s[::-1]`.

• **k-symmetric** (any contiguous k-digit window has matching sum on either side):
  Much harder. May require sliding-window digit sums.

• **Count symmetric numbers in [low, high] with odd digit count** (force "padded" symmetry):
  Depends on definition. Could be: middle digit free, surrounding halves balanced.

• **Find the n-th symmetric integer:**
  Iterate count up to n, or use math to construct directly (for 2-digit: 11, 22, ..., 99 are positions 1–9; for 4-digit: enumerate combinations of digit sums).

• **Symmetric in base b instead of base 10:**
  Replace `str()` with base-b conversion. Same logic.

• **Sum of digits equal AND product of digits equal:**
  Add a second check. String approach trivially extends.

• **Find max digit-sum among symmetric integers in [low, high]:**
  Track max alongside counter.

• **Symmetric integers that are also prime:**
  Add a `is_prime(x)` check. Slower but still tractable for small ranges.

• **Streaming version (numbers arrive over time, count symmetric so far):**
  Maintain counter; check each new number in O(d). O(1) query.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — string approach, generalizes to any digit count
class Solution:
    def countSymmetricIntegers(self, low: int, high: int) -> int:
        count = 0
        for x in range(low, high + 1):
            s = str(x)
            if len(s) % 2 == 0:
                half = len(s) // 2
                if sum(map(int, s[:half])) == sum(map(int, s[half:])):
                    count += 1
        return count
```

**Editorial — Arithmetic + 11-trick:**
```python
def countSymmetricIntegers(low, high):
    res = 0
    for a in range(low, high + 1):
        if a < 100 and a % 11 == 0:
            res += 1
        if 1000 <= a < 10000:
            left = a // 1000 + a % 1000 // 100
            right = a % 100 // 10 + a % 10
            if left == right:
                res += 1
    return res
```

**Cleaner one-liner with helper:**
```python
def countSymmetricIntegers(low, high):
    def is_sym(x):
        s = str(x)
        if len(s) % 2: return False
        h = len(s) // 2
        return sum(map(int, s[:h])) == sum(map(int, s[h:]))
    return sum(is_sym(x) for x in range(low, high + 1))
```

**Hybrid (11-trick for 2-digit, string for 4-digit):**
```python
def countSymmetricIntegers(low, high):
    count = 0
    for x in range(low, high + 1):
        if 10 <= x < 100 and x % 11 == 0:
            count += 1
        elif 1000 <= x < 10000:
            s = str(x)
            if int(s[0]) + int(s[1]) == int(s[2]) + int(s[3]):
                count += 1
    return count
```

---

**Time:** O((high - low) × d) where d = digit count
**Space:** O(d) for string conversion

**Pattern flag:** "Check a digit-level property for every number in a range" → first try brute-force enumeration with string conversion (cleanest). Only optimize to digit DP when the range is too large to iterate. The 11-divisibility trick for 2-digit symmetry is a neat micro-optimization, not a generalizable technique.
