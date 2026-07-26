# 3751. Total Waviness of Numbers in Range I

**Difficulty:** Medium
**Pattern:** Dynamic Programming / Digit DP (advanced) — Brute Force OK at given constraints
**Date Solved:** 2026-06-09
**Status:** ✅

---

## Understanding the Goal

For each number in `[num1, num2]`, count its **waviness** — number of peaks (digit strictly greater than both neighbors) and valleys (digit strictly less than both). Sum all waviness values across the range.

**Constraint reveals the expected approach:** `num1, num2 ≤ 10⁵` ≈ 100,000 numbers. **Brute force is sufficient** — the hint says "Use bruteforce."

**But editorial uses Digit DP** — an advanced template for "count/sum over a range by digit structure." Scales to `num2 ≤ 10¹⁵` (the Waviness II problem). Worth learning even when not strictly needed.

This MD covers BOTH approaches:
1. **Brute force** — primary for given constraints
2. **Digit DP** — editorial; scales to harder versions

---

## LAYER 1: Line-by-Line Explanation

### Approach 1: Brute Force (Pythonic, fits constraints)

```python
class Solution:
    def totalWaviness(self, num1: int, num2: int) -> int:
        def waviness(n: int) -> int:
            s = str(n)                          # Convert to string for digit access
            if len(s) < 3:                      # Need ≥ 3 digits for any peak/valley
                return 0
            
            count = 0
            # Iterate over MIDDLE digits only (skip first and last)
            for i in range(1, len(s) - 1):
                # Peak: strictly greater than both neighbors
                if s[i] > s[i-1] and s[i] > s[i+1]:
                    count += 1
                # Valley: strictly less than both neighbors
                elif s[i] < s[i-1] and s[i] < s[i+1]:
                    count += 1
            
            return count
        
        return sum(waviness(n) for n in range(num1, num2 + 1))
```

**Why string comparison works:** `'5' > '3'` → True (char ordering matches digit ordering for '0'-'9'). No conversion needed.

**Complexity:** O((num2 - num1) × log(num2)) ≈ 6 × 10⁵ ops — instant.

### Approach 1 variant: Using `zip` for adjacent triples

```python
class Solution:
    def totalWaviness(self, num1: int, num2: int) -> int:
        def waviness(n):
            s = str(n)
            if len(s) < 3:
                return 0
            # zip(s, s[1:], s[2:]) yields adjacent triples (a, b, c)
            # b is peak if a < b > c; b is valley if a > b < c
            return sum(
                1
                for a, b, c in zip(s, s[1:], s[2:])
                if (a < b and b > c) or (a > b and b < c)
            )
        return sum(waviness(n) for n in range(num1, num2 + 1))
```

Extends adjacent-pair template to **adjacent triples** — `zip(s, s[1:], s[2:])` is the natural generalization.

### Approach 2: Digit DP (editorial — advanced)

**The framework — range sum decomposition:**

```
answer(num1, num2) = solve(num2) - solve(num1 - 1)
```

Where `solve(N)` computes the sum over `[0, N]`. The prefix-sum trick on integers.

**The state:**

| Variable | Meaning |
|----------|---------|
| `pos` | Current digit position (0 = most significant) |
| `prev` | Digit TWO positions before |
| `curr` | Digit ONE position before (being checked) |
| `isLimit` | True if all prior digits matched upper bound → restricts current ≤ s[pos] |
| `isLeading` | True if all prior digits were leading zeros |

**Why we need `prev` AND `curr`:**

To decide if `curr` is a peak/valley, need digit BEFORE (`prev`) and digit AFTER (the one being placed). So we look one back to decide if the prev-prev position was a peak/valley.

**The editorial's annotated code:**

```python
from functools import lru_cache

class Solution:
    def totalWaviness(self, num1: int, num2: int) -> int:
        # Range trick: sum over [num1, num2] = solve(num2) - solve(num1 - 1)
        def solve(num: int) -> int:
            if num < 100:
                return 0
            
            s = str(num)
            n = len(s)
            
            @lru_cache(None)
            def dfs(pos: int, prev: int, curr: int, isLimit: bool, isLeading: bool):
                if pos == n:
                    return 1, 0                 # (count=1 valid, waviness=0)
                
                cnt = 0
                waviness = 0
                up = int(s[pos]) if isLimit else 9
                
                for digit in range(up + 1):
                    newLeading = isLeading and (digit == 0)
                    newPrev = curr
                    newCurr = -1 if newLeading else digit
                    
                    subCnt, subSum = dfs(
                        pos + 1, newPrev, newCurr,
                        isLimit and (digit == up), newLeading
                    )
                    
                    # Check if prev-prev (curr) is a peak/valley
                    if not newLeading and prev >= 0 and curr >= 0:
                        if (prev < curr and curr > digit) or (prev > curr and curr < digit):
                            # Each sub-number gets +1 waviness
                            waviness += subCnt
                    
                    cnt += subCnt
                    waviness += subSum
                
                return cnt, waviness
            
            _, totalSum = dfs(0, -1, -1, True, True)
            return totalSum
        
        return solve(num2) - solve(num1 - 1)
```

**The "(count, sum)" insight:**

Typical digit-DP counts. Here we **sum** a property. So `dfs` returns `(cnt, sum)`. When a peak/valley is found at the prev-prev position, every sub-number from here has +1 waviness — add `subCnt` to running `waviness` total.

---

## LAYER 2: Worked Examples

### Example 1: `num1 = 120, num2 = 130` → 3

| n | digits | analysis | waviness |
|---|--------|----------|----------|
| 120 | 1,2,0 | 1<2>0 → peak | 1 |
| 121 | 1,2,1 | 1<2>1 → peak | 1 |
| 122-129 | various | no peak/valley | 0 each |
| 130 | 1,3,0 | 1<3>0 → peak | 1 |

Sum: **3** ✓

### Example 2: `num1 = 198, num2 = 202` → 3

| n | analysis | waviness |
|---|----------|----------|
| 198 | 1<9>8 → peak | 1 |
| 199 | 1<9=9 → no | 0 |
| 200 | 2>0=0 → no | 0 |
| 201 | 2>0<1 → valley | 1 |
| 202 | 2>0<2 → valley | 1 |

Sum: **3** ✓

### Example 3: `n = 4848` → 2 (a 4-digit number)

| position | digit | neighbors | peak/valley? |
|----------|-------|-----------|---------------|
| 0 | 4 | (none, 8) | skip (first) |
| 1 | 8 | (4, 4) | 4<8>4 → **peak** |
| 2 | 4 | (8, 8) | 8>4<8 → **valley** |
| 3 | 8 | (4, none) | skip (last) |

Total: **2** ✓

### Edge cases

- **All numbers < 100:** Waviness 0. Early return.
- **Repeated digits:** `122` — equal neighbor → not peak/valley
- **Plateaus:** Strict inequalities required
- **num1 == num2:** Just one number's waviness

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Brute force | O((R - L) × log R) | O(1) | **Sufficient for n ≤ 10⁵** |
| Digit DP | O(n × 10 × 10 × 4) ≈ O(n) | O(n × 100) | **Scales to 10¹⁵+** |

**Why this is a "new pattern family":**

You've drilled hash maps, sorting, two pointers, prefix sums, tree DFS, bit manipulation, simulation. **DP** is its own family — and **digit DP** is a specialized branch.

General DP template:
1. **Define state** — minimal info to recurse
2. **Define transitions** — build bigger from smaller
3. **Memoize** — cache results
4. **Handle edge cases**

Digit DP adds:
- **isLimit (tight constraint):** prevents constructing numbers > upper bound
- **isLeading (leading zeros):** distinguishes "100" from "0100"

**Why constraints matter:**

`num2 ≤ 10⁵`: ~100K numbers, ≤ 6 digits each. Brute force = 600K ops — instant.

If `num2 ≤ 10¹⁵`: brute force = 10¹⁵ ops — impossible. Digit DP works in O(15 × 100) = 1500 states.

**The hint "Use bruteforce" is a tell:** constraints permit it.

**The range trick — universal pattern:**

```
f(L, R) = solve(R) - solve(L - 1)
```

Used in prefix sums on arrays, range counting (LC 357, 902, 1012), this problem. Always consider when problem asks "compute X over [L, R]."

**The "(count, sum)" return pattern:**

Standard digit DP counts. To **sum**: return tuple `(count, sum_so_far)`. When a contributing event happens (peak/valley), each sub-number gets +1, so add `subCnt` to running sum.

Generalizes:
- Count numbers with digit sum K: return count
- Sum digit-sums over range: return (count, total_digit_sum)
- Sum of waviness over range: return (count, total_waviness)

**Memoization bound:**

State space: `(pos, prev, curr, isLimit, isLeading)` = 16 × 11 × 11 × 2 × 2 ≈ 7500 unique states. Each computed once. Per state: O(10) work. Total: ~75K ops per `solve` call.

**`isLimit` intuition:**

Constructing ≤ 130 digit-by-digit:
- Place 0: next digit ANYTHING (already < 130)
- Place 1: next digit ≤ 3 (matched leading digit)

`isLimit` tracks "still on tight upper bound." Deviate by one digit → all subsequent are unconstrained.

**`isLeading` intuition:**

Without it, "5" modeled as "0005" — would wrongly detect "peaks" in leading zeros. Marks "haven't started yet."

---

## LAYER 4: Interview Variations

• **Total Waviness II (similar):** Same problem, `num ≤ 10¹⁵`. Forces digit DP.
• **Count with at LEAST K peaks/valleys:** Add "waviness so far" to state.
• **Count with EXACTLY K:** Strict equality at base case.
• **Count "wavy" numbers (alternating up-down):** Stricter; same DP shape.
• **Sum of digit sums in range:** Simpler — track running digit sum.
• **Numbers without consecutive repeats:** Track previous digit; reject if equal.
• **No '4' or '7' (LC 902):** Restrict digit choices.
• **Strictly increasing digits:** Restrict to > prev.
• **Digit sum divisible by K:** Track digit sum mod K.
• **Brute force for small ranges:** Always test brute force first — verification baseline.
• **Inclusive vs exclusive:** Careful with `solve(L - 1)`. If L=0, handle as zero.

---

## LAYER 5: Cheat Sheet

```python
# Preferred for these constraints — Brute Force
class Solution:
    def totalWaviness(self, num1: int, num2: int) -> int:
        def waviness(n):
            s = str(n)
            if len(s) < 3:
                return 0
            return sum(
                1
                for i in range(1, len(s) - 1)
                if (s[i] > s[i-1] and s[i] > s[i+1])
                or (s[i] < s[i-1] and s[i] < s[i+1])
            )
        return sum(waviness(n) for n in range(num1, num2 + 1))
```

**With adjacent-triple zip:**
```python
def totalWaviness(num1, num2):
    def waviness(n):
        s = str(n)
        if len(s) < 3:
            return 0
        return sum(
            1 for a, b, c in zip(s, s[1:], s[2:])
            if (a < b > c) or (a > b < c)
        )
    return sum(waviness(n) for n in range(num1, num2 + 1))
```

**Editorial — Digit DP:**
```python
from functools import lru_cache

class Solution:
    def totalWaviness(self, num1: int, num2: int) -> int:
        def solve(num):
            if num < 100:
                return 0
            s = str(num)
            n = len(s)
            
            @lru_cache(None)
            def dfs(pos, prev, curr, isLimit, isLeading):
                if pos == n:
                    return 1, 0
                cnt = 0
                waviness = 0
                up = int(s[pos]) if isLimit else 9
                for digit in range(up + 1):
                    newLeading = isLeading and (digit == 0)
                    newPrev = curr
                    newCurr = -1 if newLeading else digit
                    subCnt, subSum = dfs(
                        pos + 1, newPrev, newCurr,
                        isLimit and (digit == up), newLeading
                    )
                    if not newLeading and prev >= 0 and curr >= 0:
                        if (prev < curr and curr > digit) or (prev > curr and curr < digit):
                            waviness += subCnt
                    cnt += subCnt
                    waviness += subSum
                return cnt, waviness
            
            _, total = dfs(0, -1, -1, True, True)
            return total
        
        return solve(num2) - solve(num1 - 1)
```

---

**Time:** O((R - L) × log R) brute, O(D × 100) digit DP | **Space:** O(1) brute, O(D × 100) DP

**Pattern flag:** "Count or sum a property over a range of integers" → two tiers:
1. **Constraint ≤ 10⁶:** Brute force.
2. **Constraint ≥ 10⁸:** **Digit DP** — `solve(R) - solve(L-1)`; recurse on positions with state `(pos, ..., isLimit, isLeading)`. Memoize.

The "adjacent triple" template `zip(s, s[1:], s[2:])` extends adjacent-pair (7+ encounters). For sum-not-count digit DP, return `(count, sum)` tuple — events add `subCnt` to running sum.

⭐ **First Digit DP** on the log. Drill the template via LC 233, 357, 902, 1012 in review days.
