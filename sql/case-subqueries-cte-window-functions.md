> **Skill:** sql (CASE, subqueries, CTEs, window functions) · **Source:** DataCamp "Data Manipulation in SQL" · **Mode:** Course-guided · **Date:** 2026-10-25
> Index: [sql README](./README.md) · **first artifact in `sql/`**
> *Techniques in my own words + the key patterns. Proof-of-work screenshots live in the source course capture (not committed — 14.6 MB). What this doc does NOT yet prove: writing these cold on unfamiliar data.*

# Data Manipulation in SQL — CASE · Subqueries · CTEs · Window Functions

## What the course taught (European Soccer DB)
Four tools for shaping query output beyond a flat `SELECT ... WHERE`:
**CASE** (conditional columns) → **Subqueries** (a query inside a query) → **CTEs** (named
subqueries up front) → **Window functions** (per-row calculations over a group without collapsing rows).

---

## 1. CASE — conditional logic in a column
Maps conditions to values, like `if/elif/else`. Each `WHEN` can test **multiple conditions** with
`AND`/`OR`; order matters (first match wins); `ELSE` catches the rest; `END AS name`.
```sql
SELECT date, hometeam_id, awayteam_id,
  CASE WHEN hometeam_id = 8455 AND home_goal > away_goal THEN 'Chelsea home win!'
       WHEN awayteam_id = 8455 AND home_goal < away_goal THEN 'Chelsea away win!'
       WHEN (hometeam_id = 8455 OR awayteam_id = 8455)
            AND home_goal = away_goal                    THEN 'Chelsea tie'
       ELSE 'loss' END AS outcome
FROM match
WHERE hometeam_id = 8455 OR awayteam_id = 8455;
```
**Reproduce cold:** a win/loss/tie classifier comparing two columns, multi-condition `WHEN`, `ELSE`.

## 2. Subqueries — a query inside a query
Three placements, three jobs:
- **In `WHERE`** — filter against a computed set/value: `WHERE id IN (SELECT id FROM ... WHERE ...)`.
- **In `FROM`** — treat a query's result as a table (must alias it): `FROM (SELECT ...) AS s`.
- **In `SELECT`** — attach one scalar value to every row:
  `(SELECT AVG(home_goal+away_goal) FROM match WHERE season='2013/2014') AS overall_avg`.

**Nested** = subquery inside a subquery. **Correlated** = the inner query references the outer
query's row (re-evaluated per outer row). The course's warning, which is the real lesson:
**filter *each* subquery, not just the outer one** — an unfiltered inner query silently changes the
denominator/population.
```sql
SELECT s.stage, ROUND(s.avg_goals,2) AS avg_goal,
  (SELECT AVG(home_goal+away_goal) FROM match WHERE season='2013/2014') AS overall_avg
FROM (SELECT stage, AVG(home_goal+away_goal) AS avg_goals
      FROM match WHERE season='2013/2014' GROUP BY stage) AS s   -- inner filter
WHERE s.avg_goals > (SELECT AVG(home_goal+away_goal)
                     FROM match WHERE season='2013/2014');       -- filter here too
```
**Reproduce cold:** a `FROM`-subquery aggregated then filtered against a `SELECT`-subquery scalar.

## 3. CTEs — named subqueries, declared up front (`WITH`)
Same power as a `FROM`-subquery, but **named and placed before the main query**, so it reads
top-to-bottom instead of inside-out. **Multiple CTEs: list them comma-separated, no comma after the
last one.** A later CTE can build on an earlier one.
```sql
WITH s1 AS (SELECT country_id, id FROM match WHERE (home_goal+away_goal) >= 10),
     s2 AS (SELECT country_id, id FROM match WHERE (home_goal+away_goal) <= 1)
SELECT c.name AS country,
       COUNT(s1.id) AS high_scores,
       COUNT(s2.id) AS low_scores
FROM country AS c
INNER JOIN s1 ON c.id = s1.country_id
INNER JOIN s2 ON c.id = s2.country_id
GROUP BY c.name;
```
**Why it matters (readability = maintainability):** a deeply nested/correlated subquery and a CTE
can compute the same answer, but the CTE is far easier to read, reuse, and debug. Same instinct as
my "arithmetic in one place" portfolio principle.
**Reproduce cold:** two CTEs joined back to a dimension table, no trailing comma.

## 4. Window functions — per-row calc over a group, rows kept
`OVER()` runs an aggregate/rank **without collapsing rows** (unlike `GROUP BY`). `PARTITION BY`
restarts the window per group (and can partition by **multiple columns**); `ORDER BY` inside `OVER`
makes it **running/sliding**.
```sql
-- running total + running average, oldest→newest
SELECT date, home_goal,
  SUM(home_goal) OVER(ORDER BY date
      ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total,
  AVG(home_goal) OVER(ORDER BY date) AS running_avg
FROM match WHERE hometeam_id = 8456 AND season = '2011/2012';

-- partition by two columns: each team's season average, on every row
AVG(home_goal+away_goal) OVER(PARTITION BY m.season, c.name) AS season_ctry_avg
```
**Frames** bound the window: `ROWS BETWEEN n PRECEDING AND CURRENT ROW` (trailing),
`CURRENT ROW AND UNBOUNDED FOLLOWING` (backward total), `UNBOUNDED PRECEDING AND CURRENT ROW`
(full running). Reverse the direction with `ORDER BY date DESC`.
**Reproduce cold:** a running total with an explicit frame; `PARTITION BY` a group average alongside raw rows.

---

## The cross-cutting lesson (ties to my analytics work)
The same **grain / denominator** discipline from my R work (MP#01 "rate not count", PA#05 join
cardinality) shows up here as three SQL habits: **filter every subquery** (don't let an inner query
change the population), prefer a **CTE** when a nested subquery gets unreadable, and use a **window
function** when I need a per-group statistic *without* losing the detail rows.

## Interview line candidates (sql — in the bank's SQL section)
> I reach for a CTE over a nested subquery when readability starts to suffer — same answer, but a
> `WITH` block reads top-to-bottom and is far easier to debug and reuse.
> A window function gives me a group statistic without collapsing the rows — `AVG(...) OVER(PARTITION
> BY team)` puts each team's average next to every individual match, which `GROUP BY` can't do.

## Honest limit
Course-guided, on a clean teaching database. I have **not** yet written these cold against
unfamiliar data, and correlated subqueries + window *frames* are the two I'd want to re-derive
unaided. Next: a cold query on a new dataset → promotes `sql/` from Building toward Active.
