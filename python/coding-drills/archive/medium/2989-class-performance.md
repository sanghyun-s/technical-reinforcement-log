# 2989. Class Performance

**Difficulty:** Medium  
**Pattern:** SQL / Calculation / MAX / MIN  
**Date Solved:** 2026-05-16  
**Status:** ✅

---

## Understanding the Goal

Calculate total score for each student (sum of 3 assignments), then find the difference between highest and lowest total scores.

**Key insight:** Calculate sum first, then find MAX - MIN of those sums.

---

## LAYER 1: SQL Solution Explained

```sql
SELECT 
    MAX(total_score) - MIN(total_score) AS difference_in_score
FROM (
    SELECT 
        student_id,
        (assignment1 + assignment2 + assignment3) AS total_score
    FROM Scores
) AS student_totals;
```

**Logic:**
1. **Subquery:** Calculate `total_score` for each student (sum of 3 assignments)
2. **Outer query:** Find `MAX(total_score)` and `MIN(total_score)`
3. **Subtract:** MAX - MIN = difference
4. **Alias:** Returns as `difference_in_score`

---

## LAYER 2: Worked Example

**Input:**
```
+------------+--------------+-------------+-------------+-------------+
| student_id | student_name | assign1 | assign2 | assign3 |
+------------+--------------+-------------+-------------+-------------+
| 309        | Owen         | 88      | 47      | 87      |
| 321        | Claire       | 98      | 95      | 37      |
| 338        | Julian       | 100     | 64      | 43      |
| 423        | Peyton       | 60      | 44      | 47      |
| 896        | David        | 32      | 37      | 50      |
| 235        | Camila       | 31      | 53      | 69      |
+------------+--------------+-------------+-------------+-------------+
```

**Step 1: Subquery (calculate totals):**
```
student_id  total_score
309         88+47+87 = 222
321         98+95+37 = 230 ← MAX
338         100+64+43 = 207
423         60+44+47 = 151
896         32+37+50 = 119 ← MIN
235         31+53+69 = 153
```

**Step 2: Outer query:**
```
MAX(total_score) = 230
MIN(total_score) = 119
Difference = 230 - 119 = 111
```

**Output:**
```
+---------------------+
| difference_in_score |
+---------------------+
| 111                 |
+---------------------+
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n) - single table scan |
| **Space** | O(n) - subquery result |
| **Subquery** | Necessary to calculate totals first |
| **Result** | Single row with single value |

---

## LAYER 4: Interview Variations

• **If asked "with all student scores"** → Don't use subquery, SELECT student_id, total_score  
• **If asked "difference for each assignment"** → Separate MAX/MIN for each assignment column  
• **If asked "using CTE"** → Use WITH clause instead of subquery  

---

## LAYER 5: Cheat Sheet

**Approach 1: Subquery (Recommended)**
```sql
SELECT 
    MAX(total_score) - MIN(total_score) AS difference_in_score
FROM (
    SELECT 
        assignment1 + assignment2 + assignment3 AS total_score
    FROM Scores
) AS student_totals;
```

**Approach 2: CTE**
```sql
WITH student_totals AS (
    SELECT 
        assignment1 + assignment2 + assignment3 AS total_score
    FROM Scores
)
SELECT 
    MAX(total_score) - MIN(total_score) AS difference_in_score
FROM student_totals;
```

---

**Time:** O(n) | **Space:** O(n)
