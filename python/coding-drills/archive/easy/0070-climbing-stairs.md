# 70. Climbing Stairs

**Difficulty:** Easy
**Pattern:** Dynamic Programming / Fibonacci
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

Count the number of distinct ways to climb a staircase of `n` steps, taking either 1 or 2 steps at a time.

**Key insight:** This is Fibonacci in disguise. To reach step `n`, your last move was either:
- A 1-step from step `n-1`, OR
- A 2-step from step `n-2`

So `ways(n) = ways(n-1) + ways(n-2)`. The base cases are `ways(0) = 1` (one way to "stand at the bottom") and `ways(1) = 1` (one move).

**Note on the editorial:** The provided solution uses naive recursion — it works but is O(2^n) and will time-out around n=40. The constraint `n <= 45` is suspicious; the intended solution is DP/iterative. I'll cover all three approaches.

---

## LAYER 1: Line-by-Line Explanation

**Editorial's approach (naive recursion — slow):**

```python
class Solution:
    def climbStairs(self, n: int) -> int:
        return self.climb_Stairs(0, n)          # Start at step 0, target n

    def climb_Stairs(self, i: int, n: int) -> int:
        if i > n:                                # Overshot — invalid path
            return 0
        if i == n:                               # Reached the top — 1 valid way
            return 1
        # Try both moves: +1 step or +2 steps
        return self.climb_Stairs(i + 1, n) + self.climb_Stairs(i + 2, n)
```

**The recursion tree branches at every step**, exploring all possible paths from 0 to n. Each path that lands exactly on `n` contributes 1; overshoots contribute 0. Total paths = the answer.

**Why it's slow:** The same subproblem `climb_Stairs(i, n)` gets recomputed many times. For n=10, `climb_Stairs(5, 10)` is called multiple times via different branches.

**Bottom-up DP (the recommended solution):**

```python
class Solution:
    def climbStairs(self, n: int) -> int:
        if n <= 2:
            return n                             # 1 way for n=1, 2 ways for n=2

        prev2, prev1 = 1, 2                      # ways(1)=1, ways(2)=2
        for _ in range(3, n + 1):
            curr = prev1 + prev2                 # ways(i) = ways(i-1) + ways(i-2)
            prev2 = prev1
            prev1 = curr
        return prev1
```

**Why we only keep two variables:** At step `i`, we only need `ways(i-1)` and `ways(i-2)`. Anything older is no longer needed, so O(1) space suffices.

---

## LAYER 2: Worked Examples

**Example 1: `n = 2` → 2**

Reach step 2 from:
- Step 1 (last move = +1)
- Step 0 (last move = +2)

Paths: `[1+1]`, `[2]` → 2 ways ✓

**Example 2: `n = 3` → 3**

| step i | ways(i) | reasoning |
|--------|---------|-----------|
| 0 | 1 | base: stay at bottom |
| 1 | 1 | only [1] |
| 2 | 2 | [1+1], [2] |
| 3 | 3 | ways(2) + ways(1) = 2 + 1 |

Paths: `[1+1+1]`, `[1+2]`, `[2+1]` → 3 ways ✓

**Example 3: `n = 5` → 8**

| i | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|
| ways(i) | 1 | 2 | 3 | 5 | 8 |

This is the Fibonacci sequence! Specifically, `ways(n) = Fib(n+1)`.

**Walking through the iterative solution for n=5:**

```
prev2=1, prev1=2                  (state after base case)
i=3: curr = 2+1 = 3 → prev2=2, prev1=3
i=4: curr = 3+2 = 5 → prev2=3, prev1=5
i=5: curr = 5+3 = 8 → prev2=5, prev1=8
return 8 ✓
```

**Recursion tree for naive solution (n=4) — illustrates the redundancy:**

```
                    climb(0,4)
                   /          \
              climb(1,4)      climb(2,4)
              /      \         /       \
        climb(2,4) climb(3,4) climb(3,4) climb(4,4)=1
            ...      ...        ...
```

Notice `climb(2,4)` and `climb(3,4)` each appear multiple times — that's the wasted work.

**Edge cases:**
- `n = 1` → 1 (only [1])
- `n = 0` → 1 (do nothing) — not in constraints but a clean base case
- `n = 45` → 1,836,311,903 (still fits in 32-bit int)

---

## LAYER 3: Key Insights

| Approach | Time | Space | Verdict |
|----------|------|-------|---------|
| Naive recursion | O(2^n) | O(n) call stack | Times out for large n |
| Recursion + memoization | O(n) | O(n) | Good, but extra memory |
| Bottom-up DP (array) | O(n) | O(n) | Clean, easy to extend |
| Bottom-up DP (two vars) | O(n) | O(1) | **Optimal** |
| Matrix exponentiation | O(log n) | O(1) | Overkill for n≤45 |
| Binet's formula (closed form) | O(1) | O(1) | Precision issues |

**Why this is Fibonacci:** The recurrence `f(n) = f(n-1) + f(n-2)` with `f(1)=1, f(2)=2` is shifted Fibonacci. Specifically `f(n) = Fib(n+1)` where `Fib(1)=1, Fib(2)=1`.

**The DP mindset:** Three pieces define every DP problem:
1. **State:** what does `dp[i]` represent? → "number of ways to reach step i"
2. **Transition:** how does `dp[i]` depend on smaller subproblems? → `dp[i] = dp[i-1] + dp[i-2]`
3. **Base case:** the smallest known answer → `dp[1]=1, dp[2]=2`

Master these three, and most Easy/Medium DP problems unlock.

**Why the editorial chose naive recursion:** It demonstrates the recurrence structure clearly, then most LeetCode editorials walk readers up through memoization → bottom-up DP. Treat naive recursion as Step 1 in your thought process, never as the final answer.

---

## LAYER 4: Interview Variations

• **You can take 1, 2, or 3 steps** (LC 1137 N-th Tribonacci):
  `dp[i] = dp[i-1] + dp[i-2] + dp[i-3]`. Same template, three previous values.

• **You can take any step ≤ k:**
  `dp[i] = sum(dp[i-1..i-k])`. Optimize with a sliding sum to keep it O(n).

• **Each step has a cost; minimize total cost** (LC 746 Min Cost Climbing Stairs):
  `dp[i] = cost[i] + min(dp[i-1], dp[i-2])`. Swap "sum" for "min".

• **Count paths but avoid certain steps** (broken stairs):
  Add a check: `if i in broken: dp[i] = 0` else apply recurrence.

• **Return all distinct paths instead of just the count:**
  Backtracking — at each step branch on +1 or +2, collect full paths. Time becomes O(2^n) because there are exponentially many paths.

• **Step costs vary AND you can take 1 or 2:**
  Same `min(dp[i-1] + cost[i-1], dp[i-2] + cost[i-2])` form.

• **Two-way staircase (can go up or down by 1):**
  Different problem — becomes a graph reachability question, not DP.

• **Compute for very large n (n = 10^18):**
  Use matrix exponentiation: `[[1,1],[1,0]]^n` in O(log n).

• **Modular arithmetic (n ≤ 10^9, answer mod 1e9+7):**
  Same DP, take mod after each addition: `dp[i] = (dp[i-1] + dp[i-2]) % MOD`.

• **You must end with a specific last step (e.g., a 2-step):**
  `ways_ending_with_2(n) = ways(n-2)`. Same DP, just shift which value to return.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — O(n) time, O(1) space
class Solution:
    def climbStairs(self, n: int) -> int:
        if n <= 2:
            return n
        prev2, prev1 = 1, 2
        for _ in range(3, n + 1):
            prev2, prev1 = prev1, prev1 + prev2
        return prev1
```

**Bottom-up DP with array (more verbose, easier to extend):**
```python
def climbStairs(n):
    if n <= 2:
        return n
    dp = [0] * (n + 1)
    dp[1], dp[2] = 1, 2
    for i in range(3, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```

**Top-down with memoization (closer to editorial style):**
```python
from functools import lru_cache
class Solution:
    def climbStairs(self, n: int) -> int:
        @lru_cache(None)
        def helper(i):
            if i > n: return 0
            if i == n: return 1
            return helper(i+1) + helper(i+2)
        return helper(0)
```

**Naive recursion (DON'T USE — for understanding only):**
```python
def climbStairs(n):
    if n <= 2: return n
    return climbStairs(n-1) + climbStairs(n-2)
```

---

**Time:** O(n) | **Space:** O(1)

**Pattern flag:** Whenever you see "count number of ways" + "each move adds a fixed amount to a counter" → it's a DP problem with Fibonacci-style recurrence. The two-variable rolling technique is the cleanest implementation.
