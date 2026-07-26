# 1672. Richest Customer Wealth

**Difficulty:** Easy  
**Pattern:** Matrix / Array / Iteration  
**Date Solved:** 2026-05-16  
**Status:** ✅

---

## Understanding the Goal

Find the maximum total wealth among all customers.

**Key insight:** Sum each customer's accounts (row sum), then find the maximum.

---

## LAYER 1: Editorial Code Explained

```python
def maximumWealth(self, accounts: List[List[int]]) -> int:
    # Initialize the maximum wealth seen so far to 0
    max_wealth_so_far = 0
    
    # Iterate over each customer (each row)
    for account in accounts:
        # Calculate current customer's total wealth (sum of row)
        curr_customer_wealth = sum(account)
        
        # Update max if current wealth is greater
        max_wealth_so_far = max(max_wealth_so_far, curr_customer_wealth)
    
    # Return the maximum wealth found
    return max_wealth_so_far
```

**Logic:**
1. `max_wealth_so_far = 0` - Initialize to 0
2. For each customer (row): calculate `sum(account)`
3. Update `max_wealth_so_far` if current is greater
4. Return the maximum

---

## LAYER 2: Worked Examples

**Example 1: [[1,2,3],[3,2,1]]**

```
Customer 0: sum([1,2,3]) = 6
Customer 1: sum([3,2,1]) = 6

max_wealth_so_far progression:
  Start: 0
  After customer 0: max(0, 6) = 6
  After customer 1: max(6, 6) = 6

Result: 6 ✓
```

**Example 2: [[1,5],[7,3],[3,5]]**

```
Customer 0: sum([1,5]) = 6
Customer 1: sum([7,3]) = 10
Customer 2: sum([3,5]) = 8

max_wealth_so_far progression:
  Start: 0
  After customer 0: max(0, 6) = 6
  After customer 1: max(6, 10) = 10
  After customer 2: max(10, 8) = 10

Result: 10 ✓
```

**Example 3: [[2,8,7],[7,1,3],[1,9,5]]**

```
Customer 0: sum([2,8,7]) = 17
Customer 1: sum([7,1,3]) = 11
Customer 2: sum([1,9,5]) = 15

Result: 17 ✓
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(m × n) - iterate each customer, sum each account |
| **Space** | O(1) - only storing max value |
| **Problem type** | Simple iteration + aggregation |
| **Key operation** | Row sum (sum of each customer's accounts) |

---

## LAYER 4: Interview Variations

• **If asked "return customer id too"** → Track index with max wealth  
• **If asked "top k richest customers"** → Use heap or sort  
• **If asked "average wealth"** → Calculate sum/count  
• **If asked "without using sum()"** → Manual loop to add accounts  

---

## LAYER 5: Cheat Sheet

**Simple approach:**
```python
def maximumWealth(self, accounts):
    max_wealth_so_far = 0
    for account in accounts:
        curr_customer_wealth = sum(account)
        max_wealth_so_far = max(max_wealth_so_far, curr_customer_wealth)
    return max_wealth_so_far
```

**One-liner:**
```python
return max(sum(account) for account in accounts)
```

**With customer index:**
```python
max_wealth = 0
richest_customer = 0
for i, account in enumerate(accounts):
    wealth = sum(account)
    if wealth > max_wealth:
        max_wealth = wealth
        richest_customer = i
return max_wealth
```

---

**Time:** O(m × n) | **Space:** O(1)
