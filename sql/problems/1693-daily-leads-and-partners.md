> **Skill:** sql / coding-drills (GROUP BY + COUNT DISTINCT) · **Source:** LeetCode #1693 · **Mode:** New (first SQL drills) · **Date:** 2026-11-01
> Difficulty: Easy · Family: Aggregation

# 1693. Daily Leads and Partners

## Problem
Table `DailySales(date_id, make_name, lead_id, partner_id)` with possible duplicate rows. For each `(date_id, make_name)`, count **distinct** leads and **distinct** partners.

## The insight
Two-key grouping + **`COUNT(DISTINCT ...)`**. The word "distinct" in the prompt is the tell — the table explicitly "may contain duplicates," so a plain `COUNT(*)` would over-count. Group by the composite key, count each id column distinctly.

## My solution
```sql
SELECT
  date_id,
  make_name,
  COUNT(DISTINCT lead_id)    AS unique_leads,
  COUNT(DISTINCT partner_id) AS unique_partners
FROM DailySales
GROUP BY date_id, make_name;
```

## The lesson (ties to my analytics work)
"Distinct per group" is the SQL version of the R/pandas **grain** discipline — when duplicates are possible, `COUNT(DISTINCT)` not `COUNT(*)`, same reason I use `n_distinct()` in R. Read the schema note ("may contain duplicates") before choosing the aggregate.

## Reproduce cold
Composite `GROUP BY` + `COUNT(DISTINCT col)` for each measure; explain why plain COUNT is wrong here.
