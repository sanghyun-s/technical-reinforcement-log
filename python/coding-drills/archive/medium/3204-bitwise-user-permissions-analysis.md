# 3204. Bitwise User Permissions Analysis

**Difficulty:** Medium  
**Pattern:** SQL / Bitwise Operations / Aggregate Functions  
**Date Solved:** 2026-05-16  
**Status:** ✅

---

## Understanding the Goal

Calculate two bitwise aggregates across all user permissions:
- **common_perms:** Bitwise AND of all permissions (features ALL users have)
- **any_perms:** Bitwise OR of all permissions (features ANY user has)

**Key insight:** SQL has `BIT_AND()` and `BIT_OR()` aggregate functions.

---

## LAYER 1: SQL Solution Explained

```sql
SELECT 
    BIT_AND(permissions) AS common_perms,
    BIT_OR(permissions) AS any_perms
FROM user_permissions;
```

**Logic:**
- `BIT_AND(permissions)`: Aggregate function that applies bitwise AND across all rows
- `BIT_OR(permissions)`: Aggregate function that applies bitwise OR across all rows
- No GROUP BY needed (calculating across entire table)
- Returns single row with both results

---

## LAYER 2: Worked Example

**Input:**
```
+---------+-------------+
| user_id | permissions |
+---------+-------------+
| 1       | 5           |
| 2       | 12          |
| 3       | 7           |
| 4       | 3           |
+---------+-------------+
```

**Bitwise AND (common_perms):**
```
5  = 0101
12 = 1100
7  = 0111
3  = 0011
    ----
AND = 0000 = 0
```

**Bitwise OR (any_perms):**
```
5  = 0101
12 = 1100
7  = 0111
3  = 0011
    ----
OR  = 1111 = 15
```

**Output:**
```
+-------------+-----------+
| common_perms | any_perms |
+-------------+-----------+
| 0            | 15        |
+-------------+-----------+
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n) - single pass through table |
| **Space** | O(1) - only storing two aggregate values |
| **MySQL functions** | `BIT_AND()`, `BIT_OR()` |
| **No filtering** | Operates on entire table |
| **Single row output** | Always returns 1 row |

---

## LAYER 4: Interview Variations

• **If asked "by user_id"** → Add `GROUP BY user_id`  
• **If asked "for users with id > 2"** → Add `WHERE user_id > 2`  
• **If asked "XOR instead"** → Use `BIT_XOR()` (MySQL 8.0+)  

---

## LAYER 5: Cheat Sheet

```sql
SELECT 
    BIT_AND(permissions) AS common_perms,
    BIT_OR(permissions) AS any_perms
FROM user_permissions;
```

---

**Time:** O(n) | **Space:** O(1)
