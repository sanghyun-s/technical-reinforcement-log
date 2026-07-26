# 2979. Most Expensive Item That Can Not Be Bought

**Difficulty:** Medium
**Pattern:** Dynamic Programming / Number Theory / Frobenius Number / Closed Form
**Date Solved:** 2026-06-14
**Status:** ✅

---

## Understanding the Goal

Given two distinct primes `p₁` and `p₂`, find the largest positive integer that CANNOT be written as `a·p₁ + b·p₂` for non-negative integers `a, b`.

This is the **Frobenius coin problem** for two denominations. Since distinct primes are coprime (gcd = 1):

**The Chicken McNugget Theorem:**

> For two coprime positive integers `m, n`, the largest integer NOT representable as `am + bn` (a, b ≥ 0) is:
>
> **`m·n - m - n`**

So the answer is one expression: `primeOne * primeTwo - primeOne - primeTwo`.

**But the problem is tagged DP** — so know both the math AND the DP approach. The DP generalizes to non-coprime cases and 3+ denominations.

---

## LAYER 1: Line-by-Line Explanation

### Approach 1: Chicken McNugget Theorem (O(1)) ⭐

```python
class Solution:
    def mostExpensiveItem(self, primeOne: int, primeTwo: int) -> int:
        # Frobenius number for coprime m, n is mn - m - n
        # Distinct primes are coprime; formula applies
        return primeOne * primeTwo - primeOne - primeTwo
```

One line. O(1).

### Approach 2: DP Bottom-Up

```python
class Solution:
    def mostExpensiveItem(self, primeOne, primeTwo):
        # Hint 1: any x > primeOne * primeTwo can be bought
        # So check only 1..primeOne*primeTwo
        limit = primeOne * primeTwo
        
        # dp[i] = True if we can form i as non-negative combo of p1, p2
        dp = [False] * (limit + 1)
        dp[0] = True
        
        # Hint 2: if dp[i] is reachable, dp[i + p1] and dp[i + p2] are too
        for i in range(limit + 1):
            if dp[i]:
                if i + primeOne <= limit:
                    dp[i + primeOne] = True
                if i + primeTwo <= limit:
                    dp[i + primeTwo] = True
        
        # Largest i with dp[i] = False
        for i in range(limit, 0, -1):
            if not dp[i]:
                return i
        return -1
```

**The reachability DP template:**

```python
dp = [False] * (limit + 1)
dp[0] = True
for i in range(limit + 1):
    if dp[i]:
        for step in steps:
            if i + step <= limit:
                dp[i + step] = True
```

Reusable for "can we reach X using steps from S?" problems. Coin change, gas station, etc.

### Approach 3: DP Backward Recurrence

```python
class Solution:
    def mostExpensiveItem(self, primeOne, primeTwo):
        limit = primeOne * primeTwo
        dp = [False] * (limit + 1)
        dp[0] = True
        for i in range(1, limit + 1):
            if (i >= primeOne and dp[i - primeOne]) or \
               (i >= primeTwo and dp[i - primeTwo]):
                dp[i] = True
        for i in range(limit, 0, -1):
            if not dp[i]:
                return i
        return -1
```

### Approach 4: BFS reachability

```python
from collections import deque

class Solution:
    def mostExpensiveItem(self, primeOne, primeTwo):
        limit = primeOne * primeTwo
        reachable = [False] * (limit + 1)
        reachable[0] = True
        queue = deque([0])
        while queue:
            curr = queue.popleft()
            for step in (primeOne, primeTwo):
                nxt = curr + step
                if nxt <= limit and not reachable[nxt]:
                    reachable[nxt] = True
                    queue.append(nxt)
        for i in range(limit, 0, -1):
            if not reachable[i]:
                return i
        return -1
```

---

## LAYER 2: Worked Examples

### Example 1: `primeOne = 2, primeTwo = 5` → 3

**Formula:** 2 × 5 - 2 - 5 = 10 - 7 = **3** ✓

**DP trace:** limit = 10. Mark reachable:

Start with dp[0] = True. Propagate +2 and +5:

```
i:    0  1  2  3  4  5  6  7  8  9  10
dp:   T  F  T  F  T  T  T  T  T  T  T
```

Unreachable: 1, 3. Largest: **3** ✓

### Example 2: `primeOne = 5, primeTwo = 7` → 23

**Formula:** 5 × 7 - 5 - 7 = 35 - 12 = **23** ✓

Unreachable values: `[1, 2, 3, 4, 6, 8, 9, 11, 13, 16, 18, 23]` — 12 of them = `(5-1)(7-1)/2`. ✓

### Example 3: `primeOne = 3, primeTwo = 7` (not in problem)

Formula: 21 - 10 = 11.
Unreachable: 1, 2, 4, 5, 8, 11. Count = 6 = `(3-1)(7-1)/2`. ✓

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **Chicken McNugget** ⭐ | **O(1)** | **O(1)** | **Requires theorem knowledge** |
| DP bottom-up | O(p₁ × p₂) | O(p₁ × p₂) | General; no theorem needed |
| BFS reachability | O(p₁ × p₂) | O(p₁ × p₂) | Equivalent |

**The Chicken McNugget Theorem:**

> For coprime positive integers `m, n`:
> - **Frobenius number:** g(m, n) = m·n − m − n
> - **Count of unreachable positive integers:** (m−1)(n−1)/2

**Why coprime matters:**

If `gcd(m, n) = d > 1`, only multiples of d are reachable. Numbers not divisible by d are NEVER reachable — infinitely many unreachable, so "largest" is undefined.

Example: m=4, n=6 (gcd=2). Only even numbers reachable. **No Frobenius number exists** for non-coprime.

**Distinct primes are coprime** by definition (only common factor is 1). So the formula always applies for this problem.

**Frobenius for 3+ denominations:**

**NO closed form exists in general.** Active research area. Special cases (arithmetic progressions, etc.) have formulas; general algorithms exist (Apéry sets). For LeetCode problems with 3+ denominations, use **DP/BFS**.

**Why this is "DP" tagged despite the formula:**

Hints 1-3 lead through DP, then Hint 4 reveals the theorem. Both solutions are valid. DP approach generalizes; formula is elegant. **Know both.**

**Hint 1 sanity check — why upper bound is `p₁ × p₂`:**

By Bezout, any V > p₁·p₂ - p₁ - p₂ is representable. So we only need to check up to p₁·p₂. Constraint `primeOne * primeTwo < 10⁵` keeps DP fast.

**Today's THIRD "Mathematical Collapse" Problem will be LC 1641 later** (stars and bars).

**The "two solutions" interview signal:**

If a problem has BOTH DP and closed form:
1. **Start with DP** — shows you understand structure
2. **Then mention closed form** — shows math depth

Recruiters love seeing both. Demonstrates breadth (algorithm) and depth (math).

---

## LAYER 4: Interview Variations

• **3+ denominations:** No closed form. Use DP. Limit harder to compute.
• **Count non-representable:** For 2 coprime: `(m-1)(n-1)/2`. General: DP + count.
• **List all non-representable:** DP + filter.
• **Non-coprime:** Undefined (infinitely many). Return -1 or all multiples of gcd not representable.
• **MIN coins to make V (LC 322):** Coin change DP — `dp[V] = min(dp[V - coin] + 1)`.
• **Count ways to make V (LC 518):** DP — `dp[V] += dp[V - coin]`.
• **K-th smallest non-representable:** Sort and pick.
• **With max coin counts:** Knapsack DP.
• **Frobenius for arithmetic progression:** Closed form exists.
• **Very large primes:** Formula scales; DP doesn't.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Chicken McNugget Theorem
class Solution:
    def mostExpensiveItem(self, primeOne: int, primeTwo: int) -> int:
        return primeOne * primeTwo - primeOne - primeTwo
```

**DP bottom-up:**
```python
def mostExpensiveItem(primeOne, primeTwo):
    limit = primeOne * primeTwo
    dp = [False] * (limit + 1)
    dp[0] = True
    for i in range(limit + 1):
        if dp[i]:
            if i + primeOne <= limit:
                dp[i + primeOne] = True
            if i + primeTwo <= limit:
                dp[i + primeTwo] = True
    for i in range(limit, 0, -1):
        if not dp[i]:
            return i
    return -1
```

**DP backward recurrence:**
```python
def mostExpensiveItem(primeOne, primeTwo):
    limit = primeOne * primeTwo
    dp = [False] * (limit + 1)
    dp[0] = True
    for i in range(1, limit + 1):
        if (i >= primeOne and dp[i - primeOne]) or (i >= primeTwo and dp[i - primeTwo]):
            dp[i] = True
    return next(i for i in range(limit, 0, -1) if not dp[i])
```

**Variation: count non-representable:**
```python
def countNonRepresentable(primeOne, primeTwo):
    return (primeOne - 1) * (primeTwo - 1) // 2
```

**Variation: list all non-representable:**
```python
def listNonRepresentable(primeOne, primeTwo):
    limit = primeOne * primeTwo
    dp = [False] * (limit + 1)
    dp[0] = True
    for i in range(limit + 1):
        if dp[i]:
            if i + primeOne <= limit: dp[i + primeOne] = True
            if i + primeTwo <= limit: dp[i + primeTwo] = True
    return [i for i in range(1, limit + 1) if not dp[i]]
```

**Generalized Frobenius for any coprime pair:**
```python
from math import gcd

def frobenius(m, n):
    if gcd(m, n) != 1:
        return -1   # Frobenius doesn't exist
    return m * n - m - n
```

---

**Time:** O(1) closed form, O(p₁ × p₂) DP | **Space:** O(1) or O(p₁ × p₂)

**Pattern flag:** Two-denomination Frobenius → **`m·n - m - n`** for coprime `m, n` (Chicken McNugget Theorem). For non-coprime or 3+ denominations: DP/BFS reachability.

The **"reachability DP"** template reusable for any "can we reach X using non-negative combinations of S?" problem.

**Interview move:**
1. Start with DP (shows you can solve general reachability)
2. Mention the closed form (shows math depth)
3. Note that for 3+ denominations no closed form exists

🎯 **Second "mathematical collapse" problem on the log** (after LC 3512 from Day 12). Lesson: when a problem looks DP but has small constraints + math hint, **check for closed form before DP**.
