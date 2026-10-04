# 1757. Recyclable and Low Fat Products

**Difficulty:** Easy  
**Pattern:** SQL / WHERE / AND  
**Date Solved:** 2026-05-16  
**Status:** ✅

---

## Understanding the Goal

Find product IDs where BOTH `low_fats = 'Y'` AND `recyclable = 'Y'`.

**Key insight:** Simple WHERE clause with AND condition.

---

## LAYER 1: SQL Solution Explained

```sql
SELECT product_id
FROM Products
WHERE low_fats = 'Y' AND recyclable = 'Y';
```

**Logic:**
1. `SELECT product_id` - Return only product IDs
2. `FROM Products` - From Products table
3. `WHERE low_fats = 'Y'` - Product must be low fat
4. `AND recyclable = 'Y'` - AND product must be recyclable
5. Both conditions must be TRUE (AND operator)

---

## LAYER 2: Worked Example

**Input:**
```
+-------------+----------+------------+
| product_id  | low_fats | recyclable |
+-------------+----------+------------+
| 0           | Y        | N          |
| 1           | Y        | Y          | ✓ Match
| 2           | N        | Y          |
| 3           | Y        | Y          | ✓ Match
| 4           | N        | N          |
+-------------+----------+------------+
```

**Evaluation:**
```
Product 0: low_fats='Y' ✓ AND recyclable='N' ✗ → EXCLUDE
Product 1: low_fats='Y' ✓ AND recyclable='Y' ✓ → INCLUDE
Product 2: low_fats='N' ✗ AND recyclable='Y' ✓ → EXCLUDE
Product 3: low_fats='Y' ✓ AND recyclable='Y' ✓ → INCLUDE
Product 4: low_fats='N' ✗ AND recyclable='N' ✗ → EXCLUDE
```

**Output:**
```
+-------------+
| product_id  |
+-------------+
| 1           |
| 3           |
+-------------+
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n) - single table scan |
| **Space** | O(k) - k matching products |
| **Conditions** | Both must be true (AND) |

---

## LAYER 4: Interview Variations

• **If asked "low fat OR recyclable"** → Change AND to OR  
• **If asked "not recyclable"** → Use `recyclable = 'N'`  
• **If asked "count of products"** → Use `COUNT(product_id)`  

---

## LAYER 5: Cheat Sheet

```sql
SELECT product_id
FROM Products
WHERE low_fats = 'Y' AND recyclable = 'Y';
```

---

**Time:** O(n) | **Space:** O(k)
