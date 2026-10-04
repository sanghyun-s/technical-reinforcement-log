# 2356. Number of Unique Subjects Taught by Each Teacher

**Difficulty:** Easy
**Pattern:** SQL / GROUP BY + COUNT DISTINCT
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

For each teacher, count how many **distinct** subjects they teach. A subject may appear multiple times in their rows (taught in different departments) — we only count it once per teacher.

**Key insight:** Two pieces of SQL syntax this problem tests:
1. **`GROUP BY teacher_id`** — collapse rows so each teacher becomes one output row
2. **`COUNT(DISTINCT subject_id)`** — within each group, dedupe `subject_id` before counting

The "teacher teaches subject_id in dept_id" framing is a red herring — `dept_id` doesn't matter. The schema allows the same teacher to record the same subject in multiple departments, which is exactly why `DISTINCT` is necessary.

---

## LAYER 1: Line-by-Line Explanation

**Standard SQL solution:**

```sql
SELECT
    teacher_id,
    COUNT(DISTINCT subject_id) AS cnt
FROM Teacher
GROUP BY teacher_id;
```

- **`SELECT teacher_id, COUNT(DISTINCT subject_id) AS cnt`** — grouping key + distinct count
- **`FROM Teacher`** — source table
- **`GROUP BY teacher_id`** — collapse rows by teacher

**Why `DISTINCT` is inside `COUNT()`:**

- `COUNT(DISTINCT subject_id)` = count unique subject_id values in group
- `COUNT(subject_id)` = count rows (wrong — same subject in different depts inflates)
- `SELECT DISTINCT COUNT(...)` = dedupes the COUNT values themselves (incorrect)

**Pandas equivalent:**

```python
import pandas as pd

def count_unique_subjects(teacher: pd.DataFrame) -> pd.DataFrame:
    return (
        teacher.groupby('teacher_id')['subject_id']
               .nunique()
               .reset_index(name='cnt')
    )
```

`.nunique()` is Pandas's `COUNT(DISTINCT ...)`.

---

## LAYER 2: Worked Examples

**Example 1: Walking through the given input**

| teacher_id | subject_id | dept_id |
|------------|------------|---------|
| 1 | 2 | 3 |
| 1 | 2 | 4 |
| 1 | 3 | 3 |
| 2 | 1 | 1 |
| 2 | 2 | 1 |
| 2 | 3 | 1 |
| 2 | 4 | 1 |

**Group by `teacher_id`:**

Group 1: subject_ids {2, 2, 3} → DISTINCT {2, 3} → COUNT **2** ✓
Group 2: subject_ids {1, 2, 3, 4} → DISTINCT {1, 2, 3, 4} → COUNT **4** ✓

| teacher_id | cnt |
|------------|-----|
| 1 | 2 |
| 2 | 4 |

**What without `DISTINCT`?**

| teacher_id | cnt (WRONG) |
|------------|-------------|
| 1 | 3 |
| 2 | 4 |

Teacher 1 inflated because subject 2 counted twice (different depts).

**Edge cases:**
- Teacher in one row: COUNT = 1
- Teacher same subject in 5 depts: 5 rows but DISTINCT = 1
- Empty table: returns 0 rows
- NULL subject_id: `COUNT(DISTINCT ...)` ignores NULLs

---

## LAYER 3: Key Insights

| Approach | Complexity | Notes |
|----------|------------|-------|
| `GROUP BY` + `COUNT(DISTINCT)` | O(n) hash aggregation | **Default, optimal** |
| Subquery `SELECT DISTINCT` then count | O(n) | Verbose |
| Self-join | O(n²) | Never use here |
| Window functions | O(n log n) | Overkill |

**Foundational SQL pattern:**

`GROUP BY x + COUNT(DISTINCT y)` is one of the top 5 most-used SQL idioms in analytics:
- "Unique users per page?" → `GROUP BY page, COUNT(DISTINCT user_id)`
- "Unique products per customer?" → `GROUP BY customer_id, COUNT(DISTINCT product_id)`
- "Distinct cities per country?" → `GROUP BY country, COUNT(DISTINCT city)`

**The subquery alternative (equivalent):**

```sql
SELECT teacher_id, COUNT(*) AS cnt
FROM (SELECT DISTINCT teacher_id, subject_id FROM Teacher) AS u
GROUP BY teacher_id;
```

Same result, two passes. Often optimized identically by the engine.

**`COUNT(*)` vs `COUNT(col)` vs `COUNT(DISTINCT col)`:**

| Expression | What it counts |
|------------|----------------|
| `COUNT(*)` | All rows (including NULLs) |
| `COUNT(subject_id)` | Rows where col IS NOT NULL |
| `COUNT(DISTINCT subject_id)` | Distinct non-NULL values |

**Performance for huge data:** `COUNT(DISTINCT)` builds a hash set per group. For billions of rows, use `APPROX_COUNT_DISTINCT` (HyperLogLog, ~99% accurate).

---

## LAYER 4: Interview Variations

• **Unique subjects per (teacher, dept):** `GROUP BY teacher_id, dept_id`.
• **Teachers with > K unique subjects:** Add `HAVING COUNT(DISTINCT subject_id) > K`.
• **Teacher with most unique subjects:** ORDER BY count DESC, LIMIT 1.
• **For each dept, count unique teachers:** Swap roles in GROUP BY.
• **Return the subject list (not count):** `GROUP_CONCAT(DISTINCT subject_id)` (MySQL) or `STRING_AGG(DISTINCT ...)` (Postgres).
• **Teachers in multiple depts:** `HAVING COUNT(DISTINCT dept_id) > 1`.
• **Subjects taught by >1 teacher:** `GROUP BY subject_id HAVING COUNT(DISTINCT teacher_id) > 1`.
• **Teachers teaching same subject in EVERY dept:** Compare per-(teacher, subject) dept count to total depts.
• **NULL teacher_ids:** All NULLs become one group in standard SQL.
• **Approximate for billion rows:** `APPROX_COUNT_DISTINCT`.
• **Cumulative version:** Window function with `COUNT(DISTINCT) OVER`.
• **Pandas streaming:** `defaultdict(set)` mapping teacher → subject set.

---

## LAYER 5: Cheat Sheet

```sql
-- Preferred
SELECT teacher_id, COUNT(DISTINCT subject_id) AS cnt
FROM Teacher
GROUP BY teacher_id;
```

**With subquery:**
```sql
SELECT teacher_id, COUNT(*) AS cnt
FROM (SELECT DISTINCT teacher_id, subject_id FROM Teacher) AS t
GROUP BY teacher_id;
```

**Pandas:**
```python
def count_unique_subjects(teacher):
    return (teacher.groupby('teacher_id')['subject_id']
                   .nunique()
                   .reset_index(name='cnt'))
```

**Pandas — drop_duplicates then groupby:**
```python
def count_unique_subjects(teacher):
    deduped = teacher[['teacher_id', 'subject_id']].drop_duplicates()
    return deduped.groupby('teacher_id').size().reset_index(name='cnt')
```

---

**Time:** O(n) | **Space:** O(distinct pairs)

**Pattern flag:** "For each X, count unique Y" → `GROUP BY x, COUNT(DISTINCT y)`. The trickiest part is recognizing that *some* column in the source table is creating duplicates — here `dept_id`. Always ask: "what's the entity, what attribute am I counting, and what could cause duplication?"
