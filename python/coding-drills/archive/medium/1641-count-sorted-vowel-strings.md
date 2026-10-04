# 1641. Count Sorted Vowel Strings

**Difficulty:** Medium
**Pattern:** DP / Recursion / Combinatorics / Stars and Bars
**Date Solved:** 2026-06-14
**Status:** ✅ 💯 **100th PROBLEM**

---

## Understanding the Goal

Count length-n strings using only `{a, e, i, o, u}` that are **non-decreasing** (lexicographically sorted).

**The combinatorial reframing — the magic move:**

A sorted vowel string is uniquely determined by **how many of each vowel** it contains. The string "aaeio" is the multiset `{a:2, e:1, i:1, o:1, u:0}`. The string is just that multiset sorted.

So we're counting tuples `(a, e, i, o, u)` of non-negative integers with `a + e + i + o + u = n`.

**That's stars and bars!**

$$\binom{n + 5 - 1}{5 - 1} = \binom{n + 4}{4}$$

**Verify with examples:**
- n = 1: C(5, 4) = 5 ✓
- n = 2: C(6, 4) = 15 ✓
- n = 33: C(37, 4) = 66,045 ✓

**One-liner:** `math.comb(n + 4, 4)`.

But problem is DP-tagged, so know both the math closed form AND the DP/recursive approaches.

---

## LAYER 1: Line-by-Line Explanation

### Approach 1: Closed-Form Stars and Bars (O(1)) ⭐

```python
from math import comb

class Solution:
    def countVowelStrings(self, n: int) -> int:
        # Stars and bars: distribute n items into 5 bins → C(n + 5 - 1, 5 - 1) = C(n + 4, 4)
        # Each sorted vowel string ↔ unique multiset of 5 vowels summing to n
        return comb(n + 4, 4)
```

One line. O(1) time, O(1) space.

`math.comb` available Python 3.8+. Returns exact integer (no floating-point error).

### Approach 2: Bottom-up DP (O(n))

```python
class Solution:
    def countVowelStrings(self, n: int) -> int:
        # dp[v] = number of sorted strings of current length ending with vowel v
        # Vowels indexed 0=a, 1=e, 2=i, 3=o, 4=u
        # Base (length 1): each vowel gives one string
        dp = [1] * 5
        
        # For each additional character (length 2 to n):
        # new_dp[v] = sum of dp[u] for u ≤ v (extend any string ending with ≤ v)
        for _ in range(n - 1):
            new_dp = [0] * 5
            for v in range(5):
                for u in range(v + 1):
                    new_dp[v] += dp[u]
            dp = new_dp
        
        return sum(dp)
```

### Approach 3: DP with Prefix Sum (cleanest DP)

```python
class Solution:
    def countVowelStrings(self, n: int) -> int:
        dp = [1] * 5
        for _ in range(n - 1):
            # Replace dp[v] with cumulative sum dp[0] + ... + dp[v]
            # In-place prefix sum
            for v in range(1, 5):
                dp[v] += dp[v - 1]
        return sum(dp)
```

**The recurrence IS a prefix sum.** `new_dp[v] = sum(dp[u] for u ≤ v)` = prefix sum at v.

### Approach 4: Recursive + Memoization

```python
from functools import lru_cache

class Solution:
    def countVowelStrings(self, n: int) -> int:
        @lru_cache(maxsize=None)
        def count(length, vowel_start):
            if length == 0:
                return 1
            return sum(count(length - 1, v) for v in range(vowel_start, 5))
        return count(n, 0)
```

Java code in the problem statement omits memoization — would TLE for n=50 in Python.

### Approach 5: Pure Math Without Library

```python
class Solution:
    def countVowelStrings(self, n: int) -> int:
        # C(n + 4, 4) = (n+4)(n+3)(n+2)(n+1) / 24
        return (n + 4) * (n + 3) * (n + 2) * (n + 1) // 24
```

Beautiful one-liner without `import math`.

---

## LAYER 2: Worked Examples

### Example 1: n = 1 → 5

Strings: {a, e, i, o, u} — 5 single vowels.
**Formula:** C(5, 4) = 5 ✓

**DP trace:** Loop doesn't execute. dp = [1,1,1,1,1]. sum = **5** ✓

### Example 2: n = 2 → 15

**Stars and bars view:** distribute 2 items into 5 bins.
- 5 ways to put both in one bin
- C(5, 2) = 10 ways to put one in each of two bins
- Total: 15 ✓

**Formula:** C(6, 4) = 15 ✓

**DP trace (prefix sum):**
- Start: dp = [1, 1, 1, 1, 1]
- Iteration 1:
  - dp[1] += dp[0] → 2
  - dp[2] += dp[1] → 3
  - dp[3] += dp[2] → 4
  - dp[4] += dp[3] → 5
- After: dp = [1, 2, 3, 4, 5]
- sum = **15** ✓

**Interpretation:** for length 2, dp[v] = strings ending with vowel v.
- 'a': 1 ("aa")
- 'e': 2 ("ae", "ee")
- 'i': 3 ("ai", "ei", "ii")
- 'o': 4 ("ao", "eo", "io", "oo")
- 'u': 5 ("au", "eu", "iu", "ou", "uu")

Total: 15 ✓

### Example 3: n = 33 → 66,045

C(37, 4) = (37 × 36 × 35 × 34) / 24 = **66,045** ✓

### DP for n=3 (Pascal-triangle reveal):

After iter 1 (length 2): dp = [1, 2, 3, 4, 5]
Iter 2: prefix sums → dp = [1, 3, 6, 10, 15] — **Pascal triangle column!**

sum = 1+3+6+10+15 = **35** = C(7, 4) ✓

This pattern — **Pascal-triangle column appearing in DP states** — is the hint that there's a closed form lurking.

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **Closed form `comb(n+4, 4)`** ⭐ | **O(1)** | **O(1)** | **Stars and bars; preferred** |
| DP with prefix sum | O(n) | O(1) (5 cells) | No math required |
| Bottom-up DP | O(n) | O(1) (5 cells) | Slightly more code |
| Recursive + memo | O(n) | O(n) memo | Top-down |
| Recursive no memo | O(5^n) | O(n) | **TLE for n=50** |

**The Stars-and-Bars Theorem (memorize):**

> Number of ways to choose `n` items from `k` types with repetition allowed:
>
> $$\binom{n + k - 1}{k - 1}$$

Equivalent formulations:
- Multisets of size n from k-element set
- Non-decreasing sequences of length n with values in {1..k}
- Solutions to `x₁ + x₂ + ... + x_k = n` with `xᵢ ≥ 0`
- Distribute n identical balls into k distinguishable boxes

**Today's THIRD "Mathematical Collapse" Problem:**

| Problem | Hidden Math | Closed Form |
|---------|-------------|-------------|
| **LC 3512 (Day 12)** Min ops divisible by k | Modular arithmetic | `sum % k` |
| **LC 2979 (today)** Most expensive item | Frobenius / McNugget | `p₁p₂ - p₁ - p₂` |
| **LC 1641 (this)** Count sorted vowels | **Stars and bars** | **`C(n + 4, 4)`** |

**Lesson:** when "Medium DP" has small constraints (n ≤ 50), check for combinatorial closed form FIRST.

**Why this is "DP" tagged:**

Path: Hint 1 → dependency on previous character → DP smell. Hint 2 → backtracking. Hint 3 → explicit DP. The closed form is a bonus for those who know the math.

**The "non-decreasing sequence ↔ multiset" duality:**

Any non-decreasing sequence of length n from a k-element ordered set is uniquely determined by its multiset. Conversely, any multiset gives exactly one non-decreasing sequence (sort it).

Appears in:
- Counting hand types (poker, dominoes, dice)
- Lattice path problems
- Compositions and partitions
- Hockey-stick identity in Pascal's triangle

**Pascal triangle column in DP:**

After iteration k, dp values form column k of Pascal's triangle. The DP recurrence is computing partial sums of Pascal coefficients via the **hockey stick identity**:

$$\sum_{i=0}^{v} \binom{i + k - 1}{k - 1} = \binom{v + k}{k}$$

**Closed form = final cell of implicit Pascal-triangle traversal.**

**The combinatorics toolkit (cumulative):**
- **Permutations** (n!): order matters
- **Combinations** (C(n, k)): order doesn't
- **Multisets / stars and bars** (C(n+k-1, k-1)): repetition allowed
- **Catalan** (C(2n, n)/(n+1)): non-crossing/balanced
- **Fibonacci**: tiling, climbing
- **Bitmask enumeration**: subsets

All counting primitives now in the toolkit.

---

## LAYER 4: Interview Variations

• **Strictly increasing instead:** Each vowel used at most once → C(5, n).
• **k different letters (not 5 vowels):** `C(n + k - 1, k - 1)`.
• **Bounded counts:** Inclusion-exclusion or generating functions.
• **Strictly decreasing:** Same count by symmetry.
• **Multinomial (different counts per letter):** Multinomial coefficient.
• **Print all strings:** Backtracking or `combinations_with_replacement`.
• **K-th lex-smallest:** Combinatorial indexing.
• **At least one of each vowel:** C(n-1, 4).
• **Probability questions:** Ratio of subsets to total.
• **Variable-length (sum 1..n):** Sum of C(k+4, 4) for k=1..n.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Closed-form stars and bars (O(1))
from math import comb

class Solution:
    def countVowelStrings(self, n: int) -> int:
        return comb(n + 4, 4)
```

**Pure math without library:**
```python
def countVowelStrings(n):
    return (n + 4) * (n + 3) * (n + 2) * (n + 1) // 24
```

**DP with prefix sum:**
```python
def countVowelStrings(n):
    dp = [1] * 5
    for _ in range(n - 1):
        for v in range(1, 5):
            dp[v] += dp[v - 1]
    return sum(dp)
```

**Bottom-up DP (explicit):**
```python
def countVowelStrings(n):
    dp = [1] * 5
    for _ in range(n - 1):
        new_dp = [0] * 5
        for v in range(5):
            for u in range(v + 1):
                new_dp[v] += dp[u]
        dp = new_dp
    return sum(dp)
```

**Recursive with memoization:**
```python
from functools import lru_cache

class Solution:
    def countVowelStrings(self, n):
        @lru_cache(maxsize=None)
        def count(length, vowel_start):
            if length == 0: return 1
            return sum(count(length - 1, v) for v in range(vowel_start, 5))
        return count(n, 0)
```

**Generalized for k symbols:**
```python
from math import comb

def countSortedStrings(n, k):
    return comb(n + k - 1, k - 1)
```

**With itertools (slow but conceptually pure):**
```python
from itertools import combinations_with_replacement

def countVowelStrings(n):
    return sum(1 for _ in combinations_with_replacement("aeiou", n))
```

---

**Time:** O(1) closed form, O(n) DP | **Space:** O(1)

**Pattern flag:** "Count non-decreasing sequences of length n from k symbols" → **stars and bars closed form**: `C(n + k - 1, k - 1)`.

**The mathematical collapse trifecta is complete** (LC 3512 → LC 2979 → LC 1641):
1. Modular arithmetic: `sum % k`
2. Frobenius (coprime): `p₁p₂ - p₁ - p₂`
3. Stars and bars: `C(n + k - 1, k - 1)`

When constraints are small and the problem says "counting," **always check for a closed combinatorial form FIRST**.

**Interview presentation:**
1. Note the DP framing (shows you understand the structure)
2. Reveal the closed form (shows math depth)
3. State the complexity tradeoff (O(1) math vs O(n) DP)

🎯 **The combinatorics toolkit is now complete on the log:** permutations, combinations, **stars and bars (today)**, Catalan (Day 11), Fibonacci (Day 8), bitmask enumeration (Day 8 + 11).

---

## 🎯 100th Problem Milestone

This is the **100th problem** solved on the LeetCode study log.

- Started: May 15, 2026
- Hit 💯: June 14, 2026
- Duration: 30 days (with a 2-week exam gap)
- Active days: 13
- Average: ~7.7 problems per active day

Composition at 💯:
- 🟢 Easy: 65
- 🟡 Medium: 32
- 🔴 Hard: 3

This problem is a fitting closer — combines DP, recursion, AND a famous combinatorial identity. The "math collapse" theme that started with LC 3512 (Day 12) and continued with LC 2979 (just before) gets its third and most elegant expression in stars and bars.

**The journey from LC 1 (Two Sum) to LC 1641 (Count Sorted Vowel Strings) compressed into 13 days.** Pattern recognition has matured from "memorize templates" to **reflexive composition** — most of today's problems reused techniques from previous days with small twists.
