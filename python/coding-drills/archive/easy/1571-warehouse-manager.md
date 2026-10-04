# 1571. Warehouse Manager

**Difficulty:** Easy  
**Pattern:** SQL / JOIN / GROUP BY / Calculation  
**Date Solved:** 2026-05-16  
**Status:** ✅

---

## Understanding the Goal

Calculate total volume occupied in each warehouse by multiplying:
- Product volume (Width × Length × Height)
- Number of units stored
- Sum by warehouse

**Key insight:** JOIN two tables, calculate per-unit volume, multiply by units, then SUM by warehouse.

---

## LAYER 1: SQL Solution Explained

```sql
SELECT 
    w.name AS warehouse_name,
    SUM(w.units * p.Width * p.Length * p.Height) AS volume
FROM Warehouse w
JOIN Products p ON w.product_id = p.product_id
GROUP BY w.name;
```

**Logic:**
1. `JOIN Products` - Get product dimensions for each warehouse entry
2. `ON w.product_id = p.product_id` - Match by product ID
3. `w.units * p.Width * p.Length * p.Height` - Volume per product type
4. `SUM(...)` - Total volume across all products in warehouse
5. `GROUP BY w.name` - Aggregate by warehouse name

---

## LAYER 2: Worked Example

**Input:**

```
Warehouse:
+----------+------------+-------+
| name     | product_id | units |
+----------+------------+-------+
| LCHouse1 | 1          | 1     |
| LCHouse1 | 2          | 10    |
| LCHouse1 | 3          | 5     |
| LCHouse2 | 1          | 2     |
| LCHouse2 | 2          | 2     |
| LCHouse3 | 4          | 1     |
+----------+------------+-------+

Products:
+------------+-------+--------+--------+
| product_id | Width | Length | Height |
+------------+-------+--------+--------+
| 1          | 5     | 50     | 40     |
| 2          | 5     | 5      | 5      |
| 3          | 2     | 10     | 10     |
| 4          | 4     | 10     | 20     |
+------------+-------+--------+--------+
```

**Step 1: After JOIN (with calculations):**
```
warehouse    product_id  units  volume_per_unit  total_volume
LCHouse1     1           1      5*50*40=10000    1*10000=10000
LCHouse1     2           10     5*5*5=125        10*125=1250
LCHouse1     3           5      2*10*10=200      5*200=1000
LCHouse2     1           2      5*50*40=10000    2*10000=20000
LCHouse2     2           2      5*5*5=125        2*125=250
LCHouse3     4           1      4*10*20=800      1*800=800
```

**Step 2: GROUP BY and SUM:**
```
LCHouse1: 10000 + 1250 + 1000 = 12250
LCHouse2: 20000 + 250 = 20250
LCHouse3: 800 = 800
```

**Output:**
```
+----------------+--------+
| warehouse_name | volume |
+----------------+--------+
| LCHouse1       | 12250  |
| LCHouse2       | 20250  |
| LCHouse3       | 800    |
+----------------+--------+
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n log n) - join + group by |
| **Space** | O(n) - result set |
| **JOIN** | Combines warehouse inventory with product dimensions |
| **Calculation** | units × width × length × height per product |
| **Aggregation** | SUM groups by warehouse |

**Key formula:**
```
Total Volume = SUM(units × width × length × height) for each warehouse
```

---

## LAYER 4: Interview Variations

• **If asked "product names too"** → Add product_name to SELECT and GROUP BY  
• **If asked "warehouse with highest volume"** → Add ORDER BY volume DESC LIMIT 1  
• **If asked "average volume per warehouse"** → Use AVG instead of SUM  

---

## LAYER 5: Cheat Sheet

```sql
SELECT 
    w.name AS warehouse_name,
    SUM(w.units * p.Width * p.Length * p.Height) AS volume
FROM Warehouse w
JOIN Products p ON w.product_id = p.product_id
GROUP BY w.name;
```

---

**Time:** O(n log n) | **Space:** O(n)
