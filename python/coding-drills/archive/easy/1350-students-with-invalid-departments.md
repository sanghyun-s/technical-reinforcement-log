# 1350. Students With Invalid Departments

**Difficulty:** Easy  
**Pattern:** SQL / JOIN / Anti-join  
**Date Solved:** 2026-05-16  
**Status:** ✅

---

## Understanding the Goal

Find students whose `department_id` does NOT exist in the `Departments` table.

**Key insight:** Use LEFT JOIN with NULL check to find non-matching records (anti-join pattern).

---

## LAYER 1: SQL Solution Explained

```sql
SELECT 
    s.id,
    s.name
FROM Students s
LEFT JOIN Departments d
    ON s.department_id = d.id
WHERE d.id IS NULL;
```

**Logic:**
1. `LEFT JOIN Departments` - Keep ALL students, match departments if they exist
2. `ON s.department_id = d.id` - Match on department_id
3. `WHERE d.id IS NULL` - Find rows where department doesn't exist (no match = NULL)

---

## LAYER 2: Worked Example

**Input:**
```
Departments:
+----+--------+
| id | name   |
+----+--------+
| 1  | EE     |
| 7  | CS     |
| 13 | BUS    |
+----+--------+

Students:
+----+-------+---------------+
| id | name  | department_id |
+----+-------+---------------+
| 23 | Alice | 1             | ✓ exists
| 1  | Bob   | 7             | ✓ exists
| 5  | Jen   | 13            | ✓ exists
| 2  | John  | 14            | ✗ NOT exists
| 4  | Jas   | 77            | ✗ NOT exists
| 3  | Steve | 74            | ✗ NOT exists
+----+-------+---------------+
```

**Output:**
```
+----+-------+
| id | name  |
+----+-------+
| 2  | John  |
| 4  | Jas   |
| 3  | Steve |
+----+-------+
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n log n) - join operation |
| **Space** | O(n) - result set |
| **Pattern** | Anti-join (find non-matching) |
| **NULL check** | Key to finding missing records |

---

## LAYER 4: Interview Variations

• **If asked "departments with no students"** → Reverse: RIGHT JOIN or NOT IN on Departments  
• **If asked "count of invalid students"** → Add `COUNT(*)` and GROUP BY department_id  
• **If asked "using NOT IN"** → `WHERE s.department_id NOT IN (SELECT id FROM Departments)`  

---

## LAYER 5: Cheat Sheet

**Approach 1: LEFT JOIN with NULL (Recommended)**
```sql
SELECT s.id, s.name
FROM Students s
LEFT JOIN Departments d ON s.department_id = d.id
WHERE d.id IS NULL;
```

**Approach 2: NOT IN subquery**
```sql
SELECT id, name
FROM Students
WHERE department_id NOT IN (SELECT id FROM Departments);
```

---

**Time:** O(n log n) | **Space:** O(n)
