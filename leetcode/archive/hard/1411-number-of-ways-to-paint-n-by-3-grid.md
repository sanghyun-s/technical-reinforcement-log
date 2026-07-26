# 1411. Number of Ways to Paint N × 3 Grid

**Difficulty:** Hard ⭐
**Pattern:** Dynamic Programming / State Compression / Row-Pattern DP
**Date Solved:** 2026-06-14
**Status:** ✅

---

## Understanding the Goal

Count colorings of an n × 3 grid with 3 colors (R/Y/G) such that no two horizontally OR vertically adjacent cells share a color. Return modulo 10⁹ + 7.

**For n = 1:** 12 valid rows.
**For n = 5000:** answer is 30,228,214 — modular arithmetic essential.

**The key insight — split valid rows into TWO types:**

Any valid row of 3 cells `(c₁, c₂, c₃)` with `c₁ ≠ c₂` and `c₂ ≠ c₃` falls into exactly one of:

1. **ABA type** (e.g., R-Y-R) — first and third cells SAME color.
   Count: 3 × 2 = **6 patterns**
2. **ABC type** (e.g., R-Y-G) — all three DIFFERENT.
   Count: 3! = **6 patterns**

**Total per row:** 12 ✓

Define:
- `aba(k)` = number of valid k-row colorings ending with **ABA-type** row
- `abc(k)` = number ending with **ABC-type** row

The transitions depend ONLY on the TYPE of the row, not specific colors — by symmetry of the 3 colors.

---

## LAYER 1: Line-by-Line Explanation

### Approach 1: Two-State DP (compressed) — O(n) ⭐

```python
class Solution:
    def numOfWays(self, n: int) -> int:
        MOD = 10**9 + 7
        
        # Base case (n=1): 6 ABA + 6 ABC rows
        aba, abc = 6, 6
        
        # For each additional row, apply transition
        # Iterate n-1 times since we already have row 1
        for _ in range(n - 1):
            # Transition (derived in LAYER 2):
            #   ABA leads to 3 ABA + 2 ABC follow-ups
            #   ABC leads to 2 ABA + 2 ABC follow-ups
            # Tuple-swap captures snapshot — no temp variable
            aba, abc = (3 * aba + 2 * abc) % MOD, (2 * aba + 2 * abc) % MOD
        
        return (aba + abc) % MOD
```

**Why simultaneous tuple assignment matters:**

```python
# WRONG — sequential corrupts the recurrence
aba = (3 * aba + 2 * abc) % MOD     # aba updated
abc = (2 * aba + 2 * abc) % MOD     # uses NEW aba — WRONG
```

Python evaluates the right side fully before assigning. **Tuple assignment captures the snapshot.** Same idiom as Fibonacci: `a, b = b, a + b`.

**Why `% MOD` at every step:**

Without mod, numbers grow to ~12 × 5^4999 — astronomically large. Python handles big integers but arithmetic gets slow. Mod at every step keeps values bounded.

### Approach 2: Full-State DP — O(144n)

```python
class Solution:
    def numOfWays(self, n):
        MOD = 10**9 + 7
        # Enumerate all 12 valid row patterns
        patterns = [(a, b, c) for a in range(3) for b in range(3) for c in range(3)
                    if a != b and b != c]
        
        # Precompute compatibility matrix
        compat = [[j for j, p2 in enumerate(patterns)
                   if all(p1[i] != p2[i] for i in range(3))]
                  for p1 in patterns]
        
        # DP: dp[i] = ways ending with pattern i
        dp = [1] * 12
        
        for _ in range(n - 1):
            new_dp = [0] * 12
            for i in range(12):
                for j in compat[i]:
                    new_dp[j] = (new_dp[j] + dp[i]) % MOD
            dp = new_dp
        
        return sum(dp) % MOD
```

Brute enumerate all 12 patterns; build 12×12 compatibility. Useful when you don't see the ABA/ABC compression.

For n=5000: 12 × 12 × 5000 = 720K operations.

### Approach 3: Matrix Exponentiation — O(log n)

For very large n (10¹⁸), even O(n) is too slow:

```python
class Solution:
    def numOfWays(self, n):
        MOD = 10**9 + 7
        def mat_mult(A, B):
            return [[(A[0][0]*B[0][0] + A[0][1]*B[1][0]) % MOD,
                     (A[0][0]*B[0][1] + A[0][1]*B[1][1]) % MOD],
                    [(A[1][0]*B[0][0] + A[1][1]*B[1][0]) % MOD,
                     (A[1][0]*B[0][1] + A[1][1]*B[1][1]) % MOD]]
        def mat_pow(M, k):
            R = [[1, 0], [0, 1]]
            while k:
                if k & 1: R = mat_mult(R, M)
                M = mat_mult(M, M)
                k >>= 1
            return R
        M = mat_pow([[3, 2], [2, 2]], n - 1)
        return ((M[0][0] + M[0][1]) * 6 + (M[1][0] + M[1][1]) * 6) % MOD
```

For n=5000, log₂(5000) ≈ 12 — just 12 matrix multiplications.

---

## LAYER 2: Worked Examples

### Deriving the transition: ABA → next row possibilities

Current row ABA = R-Y-R. Next row `(x, y, z)` must satisfy:
- `x ≠ R, y ≠ Y, z ≠ R` (vertical adjacency)
- `x ≠ y, y ≠ z` (row internal)

Enumerate (using A=R, B=Y, C=G as third color):

| x | y | z | type |
|---|---|---|------|
| Y | R | Y | ABA |
| Y | R | G | ABC |
| Y | G | Y | ABA |
| G | R | Y | ABC |
| G | R | G | ABA |

**5 valid follow-ups: 3 ABA + 2 ABC.** By color symmetry, holds for any ABA row.

### Deriving ABC → next row possibilities

Current ABC = R-Y-G. Constraints: `x ∈ {Y,G}, y ∈ {R,G}, z ∈ {R,Y}`.

| x | y | z | type |
|---|---|---|------|
| Y | R | Y | ABA |
| Y | G | R | ABC |
| Y | G | Y | ABA |
| G | R | Y | ABC |

**4 valid follow-ups: 2 ABA + 2 ABC.**

### Recurrences

```
aba(k+1) = 3·aba(k) + 2·abc(k)
abc(k+1) = 2·aba(k) + 2·abc(k)
```

Base: aba(1) = abc(1) = 6.

### Running the DP

| k | aba(k) | abc(k) | total |
|---|--------|--------|-------|
| 1 | 6 | 6 | 12 |
| 2 | 30 | 24 | **54** |
| 3 | 138 | 108 | **246** |
| 4 | 630 | 492 | **1122** |
| 5 | 2874 | 2244 | **5118** |

For n = 1: **12** ✓
For n = 5000: mod-reduces along the way to **30,228,214** ✓

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **Two-state DP** ⭐ | **O(n)** | **O(1)** | **Preferred** |
| Full-state DP | O(144 × n) | O(12) | No insight needed |
| **Matrix exponentiation** | **O(log n)** | O(1) | For very large n |

**The "state compression" technique:**

Many DP problems have N states, but **transition structure is symmetric** — multiple states behave identically. Compress equivalent states into one.

Here, 12 patterns → 2 equivalence classes (ABA, ABC) because all 6 ABA patterns have identical transition counts; same for ABC.

This is the **"equivalence-class DP"** pattern — recognize symmetries, collapse states, reduce dimensionality.

**The "DP state vs DP transition" framework:**

1. **What is the state?** (minimal info to extend) — 90% of the work
2. **What is the transition?** (state(k) ← state(k-1))

For LC 1411:
- **State:** row type (ABA or ABC) + count
- **Transition:** linear combination based on color compatibility

For LC 1255 (Day 11, only other algorithmic Hard):
- **State:** bitmask of selected words
- **Transition:** add/skip each word

Both are DP; state spaces differ wildly. **State design is 90% of the work.**

**Modular arithmetic discipline:**

`% MOD` must appear at EVERY arithmetic operation that could produce values > MOD. In Python this is correctness; in C++/Java it would overflow at iteration ~30. **Practice modding throughout.**

**Why this is Hard, not Medium:**

LeetCode Hard usually = DP problems requiring **state design insight**. Medium DP has obvious state (LC 70 Climbing Stairs: `dp[i] = dp[i-1] + dp[i-2]`). Hard DP requires you to **invent the state representation** — exactly what you did here.

**Connection to past DP problems:**

| Problem | Day | DP type |
|---------|-----|---------|
| LC 70 Climbing Stairs | 8 | Linear/Fibonacci |
| LC 338 Counting Bits | 8 | Bit DP |
| LC 3751 Waviness | 11 | Digit DP |
| LC 894 FBTs | 11 | Structural/Catalan |
| LC 1255 Words from Letters | 11 | Bitmask subset |
| LC 1277 Squares | 11 | 2D Grid |
| **LC 1411 (this)** | **13** | **Row-Pattern State Compression** |

**Seven DP sub-patterns now on the log.** Today adds **state-compression DP** — fundamental for matrix-style / row-pattern problems.

**Eigenvalue note (bonus math):**

The 2×2 transition matrix `[[3,2],[2,2]]` has eigenvalues `(5 ± √17) / 2 ≈ 4.56, 0.44`. So aba(n), abc(n) grow as ~4.56^n. For n=5000, raw count ~10^3200 — hence modular arithmetic.

---

## LAYER 4: Interview Variations

• **n × k grid (general width):** Exponential state space in k. For k=4,5,6: enumerate patterns and compute compatibility.
• **m colors instead of 3:** Re-derive base counts.
• **Find actual colorings (not count):** Backtrack with explicit patterns.
• **K-th lex-smallest coloring:** Combine counting with positional enumeration.
• **n × 3 with forbidden cells:** Reduce valid patterns per row.
• **Max sum coloring (weighted cells):** Track best score per state.
• **Wrap-around (toroidal):** First and last row constrain each other.
• **Very large n (10¹⁸):** Matrix exponentiation mandatory.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Two-state DP (O(n), O(1))
class Solution:
    def numOfWays(self, n: int) -> int:
        MOD = 10**9 + 7
        aba, abc = 6, 6
        for _ in range(n - 1):
            aba, abc = (3 * aba + 2 * abc) % MOD, (2 * aba + 2 * abc) % MOD
        return (aba + abc) % MOD
```

**Full-state DP (no insight needed):**
```python
def numOfWays(n):
    MOD = 10**9 + 7
    patterns = [(a, b, c) for a in range(3) for b in range(3) for c in range(3)
                if a != b and b != c]
    compat = [[j for j, p2 in enumerate(patterns)
               if all(p1[i] != p2[i] for i in range(3))]
              for p1 in patterns]
    dp = [1] * 12
    for _ in range(n - 1):
        new_dp = [0] * 12
        for i in range(12):
            for j in compat[i]:
                new_dp[j] = (new_dp[j] + dp[i]) % MOD
        dp = new_dp
    return sum(dp) % MOD
```

**Matrix exponentiation (O(log n)):**
```python
def numOfWays(n):
    MOD = 10**9 + 7
    def mat_mult(A, B):
        return [[(A[0][0]*B[0][0] + A[0][1]*B[1][0]) % MOD,
                 (A[0][0]*B[0][1] + A[0][1]*B[1][1]) % MOD],
                [(A[1][0]*B[0][0] + A[1][1]*B[1][0]) % MOD,
                 (A[1][0]*B[0][1] + A[1][1]*B[1][1]) % MOD]]
    def mat_pow(M, k):
        R = [[1, 0], [0, 1]]
        while k:
            if k & 1: R = mat_mult(R, M)
            M = mat_mult(M, M)
            k >>= 1
        return R
    M = mat_pow([[3, 2], [2, 2]], n - 1)
    return ((M[0][0] + M[0][1]) * 6 + (M[1][0] + M[1][1]) * 6) % MOD
```

**Explicit base values (more readable):**
```python
def numOfWays(n):
    MOD = 10**9 + 7
    if n == 1: return 12
    aba = abc = 6
    for _ in range(n - 1):
        new_aba = (3 * aba + 2 * abc) % MOD
        new_abc = (2 * aba + 2 * abc) % MOD
        aba, abc = new_aba, new_abc
    return (aba + abc) % MOD
```

---

**Time:** O(n) (state-compressed) or O(log n) (matrix) | **Space:** O(1)

**Pattern flag:** "Count colorings/arrangements with adjacency constraints on a thin strip" → **row-pattern DP with state compression**. Enumerate valid row patterns, group by equivalence class (here: ABA vs ABC), derive transition counts, iterate.

The **state compression** is the Hard insight: 12 → 2 states via color symmetry. Three colors are interchangeable, so "ABA → ABA follow-ups" count is same for ANY specific ABA row.

**General DP framework:**
1. Design state (minimal info to extend) — 90% of work
2. Derive transition (state(k) ← state(k-1))
3. Identify base case
4. Apply modular arithmetic at every step for large-n
5. Consider matrix exponentiation if n exceeds O(n) feasibility

**Same DP framework** as LC 1255 (Day 11), just different state shape. Seven DP sub-patterns now in toolkit: linear/Fibonacci, bit DP, digit DP, structural/Catalan, bitmask subset, 2D grid, **row-pattern state-compression**.

🎯 **Second algorithmic Hard on the log** — both DP, both requiring state design insight. The DP toolkit matures.
