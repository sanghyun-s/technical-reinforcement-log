# 1821. Find Customers With Positive Revenue This Year

**Difficulty:** Easy
**Pattern:** SQL / WHERE Filter
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

Return `customer_id`s where `year = 2021` AND `revenue > 0`. Two simple filters combined with AND.

**Key insight:** PK is `(customer_id, year)` — at most one row per customer per year. So no aggregation needed, no duplicates, no GROUP BY. Just filter the rows.

The problem includes other years and a negative-revenue 2021 row to test that you apply BOTH conditions correctly.

---

## LAYER 1: Line-by-Line Explanation

**Standard SQL:**

```sql
SELECT customer_id
FROM Customers
WHERE year = 2021
  AND revenue > 0;
```

- **`SELECT customer_id`** — only the ID is in expected output
- **`WHERE year = 2021`** — filter to 2021 rows
- **`AND revenue > 0`** — strictly positive

**Why `> 0` not `>= 0`:**
- `> 0` = strictly positive (excludes zero) ← correct
- `>= 0` would include zero-revenue customers
- `!= 0` would include negative-revenue customers (BUG)

"Positive" in business contexts means strictly greater than zero.

**Pandas:**

```python
import pandas as pd

def find_customers(customers: pd.DataFrame) -> pd.DataFrame:
    mask = (customers['year'] == 2021) & (customers['revenue'] > 0)
    return customers.loc[mask, ['customer_id']]
```

Note `&` (not `and`) for element-wise boolean operations, with parentheses around each condition (operator precedence is sneaky).

---

## LAYER 2: Worked Examples

**Example 1: Step-by-step on the input**

| customer_id | year | revenue |
|-------------|------|---------|
| 1 | 2018 | 50 |
| 1 | 2021 | 30 |
| 1 | 2020 | 70 |
| 2 | 2021 | -50 |
| 3 | 2018 | 10 |
| 3 | 2016 | 50 |
| 4 | 2021 | 20 |

**Step 1 — apply `year = 2021`:** 3 rows remain (cust 1, 2, 4).

**Step 2 — apply `revenue > 0`:** Drop cust 2 (-50). Keep cust 1 (30) and cust 4 (20).

**Step 3 — project `customer_id`:**

| customer_id |
|-------------|
| 1 |
| 4 |

**Per-customer analysis:**
- **Customer 1:** 2021 row revenue 30 → ✅
- **Customer 2:** 2021 row revenue -50 → ❌
- **Customer 3:** No 2021 row → ❌
- **Customer 4:** 2021 row revenue 20 → ✅

**Edge cases:**
- Revenue exactly 0: excluded (strict positive)
- Multiple 2021 rows per customer: impossible (PK constraint)
- Customer with only other years: filtered out
- Empty input: empty result
- NULL revenue: `NULL > 0` is NULL (falsy) → excluded automatically

**Why no `DISTINCT` needed:**

PK `(customer_id, year)` means each customer has at most ONE 2021 row. After `WHERE year = 2021`, customer_id is already unique in the result.

---

## LAYER 3: Key Insights

| Approach | Complexity | Notes |
|----------|------------|-------|
| Single WHERE with AND | O(n) | **Optimal** |
| Two CTEs (one per filter) | O(n) | Over-engineered |
| Subquery | O(n) | Unnecessary |
| Self-join | O(n²) | Wrong tool |

**Why "Easy" — what it's testing:**

Not SQL cleverness; it's testing whether you:
1. Read the problem carefully ("positive" vs "non-negative")
2. Apply filters with AND correctly
3. Don't over-engineer (no GROUP BY, JOIN, subquery needed)

Trap is over-thinking. Many reach for DISTINCT, GROUP BY, or aggregation when none is needed.

**"Is DISTINCT necessary?" check:**

Always ask when projecting subset of columns:
- If projection drops a PK column → maybe duplicates → consider DISTINCT
- If remaining columns still form unique key → no duplicates possible → DISTINCT wasteful

Here: `customer_id` is unique within filtered set. No DISTINCT.

**WHERE vs HAVING:**

WHERE filters individual rows BEFORE aggregation. HAVING filters groups AFTER aggregation. No aggregation here → WHERE is correct.

**Reading-comprehension trap:**

If the problem said "revenue" without qualifier, you'd include negative-revenue customers. The word "positive" is doing essential work in the spec.

---

## LAYER 4: Interview Variations

• **Positive revenue in BOTH 2021 AND 2020:** Self-join, INTERSECT, or `HAVING SUM(CASE WHEN ...)`.
• **Positive revenue in ANY year:** `SELECT DISTINCT customer_id WHERE revenue > 0`.
• **Total revenue across all years > 0:** `GROUP BY customer_id HAVING SUM(revenue) > 0`.
• **Year-over-year growth (2021 > 2020):** Self-join, compare per-year revenue.
• **Top K customers by 2021 revenue:** `ORDER BY revenue DESC LIMIT K`.
• **Customers positive every year:** `HAVING MIN(revenue) > 0`.
• **Parametrize the year:** Replace 2021 with `:year` parameter.
• **Non-zero (positive OR negative):** `revenue != 0`.
• **Decade range:** `WHERE year BETWEEN 2020 AND 2029 AND revenue > 0`.
• **Join with customer names table:** Inner join after filtering.
• **What if revenue is NULL?** `NULL > 0` is NULL → row excluded.
• **Why not `revenue > 0 OR year = 2021`?** That's OR — would include all positive across any year + all 2021 rows. Wrong logic.

---

## LAYER 5: Cheat Sheet

```sql
-- Preferred
SELECT customer_id
FROM Customers
WHERE year = 2021 AND revenue > 0;
```

**With alias:**
```sql
SELECT c.customer_id
FROM Customers AS c
WHERE c.year = 2021 AND c.revenue > 0;
```

**With ORDER BY (often expected):**
```sql
SELECT customer_id
FROM Customers
WHERE year = 2021 AND revenue > 0
ORDER BY customer_id;
```

**Pandas:**
```python
def find_customers(customers):
    mask = (customers['year'] == 2021) & (customers['revenue'] > 0)
    return customers.loc[mask, ['customer_id']]
```

**Pandas with query:**
```python
def find_customers(customers):
    return customers.query("year == 2021 and revenue > 0")[['customer_id']]
```

**Subquery (over-engineered, works):**
```sql
SELECT customer_id
FROM (SELECT customer_id, revenue FROM Customers WHERE year = 2021) AS y
WHERE revenue > 0;
```

---

**Time:** O(n) | **Space:** O(matching rows)

**Pattern flag:** "Find rows satisfying multiple conditions" → single WHERE with AND. Always check: (1) Is DISTINCT needed? Only if duplicates can survive the projection. (2) Is "positive" strict or non-negative? Read the problem. (3) Do I need GROUP BY? Only if the problem mentions total, average, count, etc. Plain filtering is the simplest SQL primitive and the most common.
