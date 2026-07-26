# 2084. Drop Type 1 Orders for Customers With Type 0 Orders

**Difficulty:** Medium
**Pattern:** SQL / Conditional Filter / Anti-Join
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

Return all orders, with one rule: **if a customer has at least one type-0 order, drop all their type-1 orders.**

Per-customer cases:
- **Mixed (has both 0 and 1):** Keep only type-0
- **Type-0 only:** Keep all (all are 0)
- **Type-1 only:** Keep all (no type-0 to "override")

**Key insight:** A row survives iff `order_type = 0` OR the customer has no type-0 orders at all. The hard part is expressing "customer has no type-0 orders" — use `NOT EXISTS` (or `NOT IN`).

This is the classic **anti-join pattern**: "include row X unless some condition exists in the same table."

---

## LAYER 1: Line-by-Line Explanation

**Preferred — single WHERE with OR + NOT EXISTS:**

```sql
SELECT order_id, customer_id, order_type
FROM Orders o
WHERE order_type = 0
   OR NOT EXISTS (
       SELECT 1 FROM Orders o2
       WHERE o2.customer_id = o.customer_id
         AND o2.order_type = 0
   );
```

For each row `o`:
- `order_type = 0`? → keep
- Otherwise → check if customer has ANY type-0 row; if no, keep; if yes, drop

**Why `SELECT 1`?** With `EXISTS`/`NOT EXISTS`, the projected column doesn't matter — engine only checks "does subquery return ≥1 row?". `SELECT 1` is convention.

**Alternative — NOT IN:**

```sql
SELECT order_id, customer_id, order_type
FROM Orders
WHERE order_type = 0
   OR customer_id NOT IN (
       SELECT customer_id FROM Orders WHERE order_type = 0
   );
```

⚠️ NOT IN breaks if subquery returns NULLs (whole `NOT IN` becomes NULL = falsy). Safe here since customer_id is non-nullable, but prefer `NOT EXISTS` for robustness.

**Alternative — LEFT JOIN anti-join:**

```sql
SELECT o.order_id, o.customer_id, o.order_type
FROM Orders o
LEFT JOIN (SELECT DISTINCT customer_id FROM Orders WHERE order_type = 0) c0
       ON o.customer_id = c0.customer_id
WHERE o.order_type = 0 OR c0.customer_id IS NULL;
```

**Alternative — window function (most elegant):**

```sql
SELECT order_id, customer_id, order_type
FROM (
    SELECT order_id, customer_id, order_type,
           MIN(order_type) OVER (PARTITION BY customer_id) AS min_type
    FROM Orders
) t
WHERE order_type = min_type;
```

For each customer, `MIN(order_type)`:
- Has type 0 → min = 0
- Only type 1 → min = 1

Keep only rows matching the customer's min. One pass.

**Pandas:**

```python
import pandas as pd

def filter_orders(orders: pd.DataFrame) -> pd.DataFrame:
    orders['min_type'] = orders.groupby('customer_id')['order_type'].transform('min')
    return orders.loc[orders['order_type'] == orders['min_type'],
                       ['order_id', 'customer_id', 'order_type']]
```

`.transform('min')` broadcasts per-group minimum back to every row.

---

## LAYER 2: Worked Examples

**Example 1: Walking through input**

| order_id | customer_id | order_type |
|----------|-------------|------------|
| 1 | 1 | 0 |
| 2 | 1 | 0 |
| 11 | 2 | 0 |
| 12 | 2 | 1 |
| 21 | 3 | 1 |
| 22 | 3 | 0 |
| 31 | 4 | 1 |
| 32 | 4 | 1 |

**Per-customer:**

| customer_id | has 0? | has 1? | strategy |
|-------------|--------|--------|----------|
| 1 | ✅ | ❌ | Keep all (all 0) |
| 2 | ✅ | ✅ | Drop 1's |
| 3 | ✅ | ✅ | Drop 1's |
| 4 | ❌ | ✅ | Keep all |

**Window function logic:**

| order_id | customer_id | order_type | min_type | keep? |
|----------|-------------|------------|----------|-------|
| 1 | 1 | 0 | 0 | ✅ |
| 2 | 1 | 0 | 0 | ✅ |
| 11 | 2 | 0 | 0 | ✅ |
| 12 | 2 | 1 | 0 | ❌ |
| 21 | 3 | 1 | 0 | ❌ |
| 22 | 3 | 0 | 0 | ✅ |
| 31 | 4 | 1 | 1 | ✅ |
| 32 | 4 | 1 | 1 | ✅ |

6 rows survive ✓

**Tracing NOT EXISTS on `(12, 2, 1)`:**
- `order_type = 0`? No.
- `NOT EXISTS (... cust=2, type=0)`? Subquery finds `(11, 2, 0)`. EXISTS=TRUE. NOT EXISTS=FALSE.
- Both branches false → drop. ✓

**Tracing NOT EXISTS on `(31, 4, 1)`:**
- `order_type = 0`? No.
- `NOT EXISTS (... cust=4, type=0)`? Subquery finds nothing. NOT EXISTS=TRUE.
- OR true → keep. ✓

**Edge cases:**
- Customer with 1 row type 0: kept
- Customer with 1 row type 1: kept (no type-0 to drop it)
- 1000 type-1 orders + 1 type-0: all 1000 type-1 dropped, only the type-0 survives

---

## LAYER 3: Key Insights

| Approach | Complexity | Notes |
|----------|------------|-------|
| `NOT EXISTS` (correlated) | O(n²) worst, often O(n) with index | **Most robust** |
| `NOT IN` | O(n) | NULL-fragile |
| LEFT JOIN + IS NULL | O(n) | Verbose but explicit |
| Window `MIN OVER PARTITION` | O(n log n) | **Most elegant** |

**Why this is filed as Medium:**

Easy SQL semantics but Medium reasoning. Requires recognizing the anti-join pattern. Beginners trip on:
1. `WHERE order_type = 0 OR order_type = 1` (gets everything — useless)
2. GROUP BY (loses per-row granularity)
3. Forgetting the OR — filters drop too much

The correct insight: **"keep this row if it's type 0, OR the customer has no type-0 row anywhere."** The OR is essential.

**Anti-join pattern — internalize this:**

"Include rows from A unless related rows exist in B." Three idioms:
1. **NOT EXISTS** — most flexible, NULL-safe
2. **NOT IN** — concise but NULL-fragile
3. **LEFT JOIN ... WHERE ... IS NULL** — explicit anti-join

**Why the window approach is so elegant:**

`MIN(order_type) OVER (PARTITION BY customer_id)` annotates every row with the customer's best type. `WHERE order_type = min_type` keeps only matching rows. Logic compresses to one pass.

**Why MIN works here specifically:** order_type ∈ {0, 1}, and we want "0 if available else 1." `MIN(0, 1) = 0`, `MIN(1, 1) = 1`. MIN happens to encode our preference exactly. For more complex rules, you'd use CASE.

**Performance:** NOT EXISTS correlated subqueries are usually rewritten by engines into hash anti-joins → effectively O(n) with hashing.

---

## LAYER 4: Interview Variations

• **Three types (0, 1, 2) — keep lowest:** Same window MIN approach. Generalizes for free.
• **Drop type 1 only if customer has BOTH 0 AND 2:** Multi-condition NOT EXISTS or CASE in HAVING.
• **Preference list (gold > silver > bronze):** CASE map types to priorities, then MIN OVER partition.
• **Most recent type per customer:** `ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) = 1`.
• **Customers with type 0 but no type 1:** `WHERE order_type = 0 AND NOT EXISTS (... order_type = 1 ...)`.
• **Customers with only type 1:** Anti-join (customers without type-0).
• **Sole order of their type:** `COUNT() OVER (PARTITION BY customer_id, order_type) = 1`.
• **R dplyr equivalent:** `group_by(customer_id) %>% filter(order_type == min(order_type))`.
• **NULL order_type?** Use `COALESCE(order_type, 999)`.
• **Streaming version:** Maintain per-customer state; complex due to retroactive revocation.
• **Bulk delete:** `DELETE FROM Orders WHERE order_type = 1 AND customer_id IN (SELECT customer_id FROM Orders WHERE order_type = 0)`.
• **Count of dropped orders per customer:** Aggregate before/after, subtract.
• **Why not HAVING?** HAVING is post-aggregation. No GROUP BY here, so WHERE is correct.

---

## LAYER 5: Cheat Sheet

```sql
-- Preferred — Window function, one pass
SELECT order_id, customer_id, order_type
FROM (
    SELECT order_id, customer_id, order_type,
           MIN(order_type) OVER (PARTITION BY customer_id) AS min_type
    FROM Orders
) t
WHERE order_type = min_type;
```

**NOT EXISTS (classic anti-join):**
```sql
SELECT order_id, customer_id, order_type
FROM Orders o
WHERE order_type = 0
   OR NOT EXISTS (
       SELECT 1 FROM Orders o2
       WHERE o2.customer_id = o.customer_id AND o2.order_type = 0
   );
```

**NOT IN:**
```sql
SELECT order_id, customer_id, order_type
FROM Orders
WHERE order_type = 0
   OR customer_id NOT IN (SELECT customer_id FROM Orders WHERE order_type = 0);
```

**LEFT JOIN anti-join:**
```sql
SELECT o.order_id, o.customer_id, o.order_type
FROM Orders o
LEFT JOIN (SELECT DISTINCT customer_id FROM Orders WHERE order_type = 0) c0
       ON o.customer_id = c0.customer_id
WHERE o.order_type = 0 OR c0.customer_id IS NULL;
```

**Pandas:**
```python
def filter_orders(orders):
    orders['min_type'] = orders.groupby('customer_id')['order_type'].transform('min')
    return orders.loc[orders['order_type'] == orders['min_type'],
                       ['order_id', 'customer_id', 'order_type']]
```

---

**Time:** O(n) to O(n log n) | **Space:** O(n)

**Pattern flag:** "Keep rows BUT exclude some based on a condition involving OTHER rows in the same table" → anti-join pattern. Three idioms: `NOT EXISTS`, `NOT IN`, `LEFT JOIN ... IS NULL`. When the rule is "prefer X over Y per group," reach for `MIN/MAX OVER PARTITION BY` for a one-pass solution.
