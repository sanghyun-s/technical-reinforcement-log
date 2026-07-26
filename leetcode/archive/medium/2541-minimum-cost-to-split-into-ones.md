# 2541. Minimum Cost to Split into Ones

**Difficulty:** Medium  
**Pattern:** Dynamic Programming / Greedy  
**Date Solved:** 2026-05-15  
**Status:** ✅

---

## Understanding the Goal

Split integer n into ones. Each split of x into (a,b) costs a*b. Find minimum total cost.

**Key insight:** Always split x into 1 and (x-1). This is optimal because 1*(x-1) minimizes the product.

---

## LAYER 1: Line-by-Line Explanation

```python
def minCost(self, n: int) -> int:
    # dp[i] = minimum cost to split i into ones
    dp = [0] * (n + 1)
    dp[1] = 0  # 1 is already one
    
    # Fill for 2 to n
    for i in range(2, n + 1):
        # Split into (1, i-1): cost = (i-1) + dp[i-1]
        dp[i] = (i - 1) + dp[i - 1]
    
    return dp[n]
```

---

## LAYER 2: Worked Examples

**n=3:**
```
Split 3 → (1,2): cost=2
Split 2 → (1,1): cost=1
Total: 2+1 = 3 ✓
```

**n=4:**
```
4 → (1,3): cost=3
3 → (1,2): cost=2
2 → (1,1): cost=1
Total: 3+2+1 = 6 ✓
```

---

## LAYER 3: Key Insights

| Aspect | Value |
|--------|-------|
| **Time** | O(n) or O(1) with closed form |
| **Space** | O(n) or O(1) |
| **Recurrence** | `dp[i] = (i-1) + dp[i-1]` |
| **Closed form** | `(n-1)*n/2` |

**Why always split into 1:** Minimizes product a*b where a+b=n.

---

## LAYER 4: Interview Variations

• **Maximum cost instead** → Split into ceil(n/2) and floor(n/2)  
• **Cost is a+b not a*b** → Always answer n-1 (n-1 splits needed)  
• **Split into k parts** → More complex DP: `dp[i][k]`  
• **Large n (10^6)** → Use closed form: `(n-1)*n//2`  

---

## LAYER 5: Cheat Sheet

**DP Approach:**
```python
def minCost(n):
    dp = [0] * (n + 1)
    for i in range(2, n + 1):
        dp[i] = (i - 1) + dp[i - 1]
    return dp[n]
```

**Closed Form (O(1)):**
```python
return (n - 1) * n // 2
```

**Key:** Pattern is sum 1+2+...+(n-1).

---

**Time:** O(n) | **Space:** O(n)
