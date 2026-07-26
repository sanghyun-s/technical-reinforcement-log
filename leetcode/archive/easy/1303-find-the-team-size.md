# 1303. Find the Team Size

**Difficulty:** Easy  
**Pattern:** SQL / Window Functions / GROUP BY  
**Date Solved:** 2026-05-16  
**Status:** ✅

---

## Understanding the Goal

For each employee, return their `employee_id` and the total count of employees in their team.

**Key insight:** Use window function `COUNT() OVER (PARTITION BY team_id)` to calculate team size per employee.

---

## LAYER 1: SQL Solution Explained

```sql
SELECT 
    employee_id,
    COUNT(*) OVER (PARTITION BY team_id) AS team_size
FROM Employee;
```

**Logic:**
1. `COUNT(*) OVER (PARTITION BY team_id)` - Count employees in each team
2. `PARTITION BY team_id` - Group by team, but keep all rows
3. Each row gets the COUNT of its team
4. Returns every employee with their team size

---

## LAYER 2: Worked Example

**Input:**
```
+-------------+------------+
| employee_id | team_id    |
+-------------+------------+
| 1           | 8          |
| 2           | 8          |
| 3           | 8          |
| 4           | 7          |
| 5           | 9          |
| 6           | 9          |
+-------------+------------+
```

**Window function breakdown:**
```
Partition by team_id = 8:
  Employee 1, 2, 3 → COUNT = 3
  
Partition by team_id = 7:
  Employee 4 → COUNT = 1
  
Partition by team_id = 9:
  Employee 5, 6 → COUNT = 2
```

**Output:**
```
+-------------+------------+
| employee_id | team_size  |
+-------------+------------+
| 1           | 3          |
| 2           | 3          |
| 3           | 3          |
| 4           | 1          |
| 5           | 2          |
| 6           | 2          |
+-------------+------------+
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n log n) - sorting for window function |
| **Space** | O(n) - all rows returned |
| **Window function** | Keeps all rows (unlike GROUP BY) |
| **Partition** | Groups logically without removing rows |

**Window Functions vs GROUP BY:**
- `GROUP BY`: Reduces rows, aggregates completely
- `WINDOW FUNCTION`: Keeps all rows, adds aggregate to each

---

## LAYER 4: Interview Variations

• **If asked "team_id and team_size"** → Add team_id to SELECT  
• **If asked "average salary per team"** → Use `AVG(salary) OVER (PARTITION BY team_id)`  
• **If asked "rank within team"** → Use `ROW_NUMBER() OVER (PARTITION BY team_id ORDER BY employee_id)`  

---

## LAYER 5: Cheat Sheet

```sql
SELECT 
    employee_id,
    COUNT(*) OVER (PARTITION BY team_id) AS team_size
FROM Employee;
```

---

**Time:** O(n log n) | **Space:** O(n)
