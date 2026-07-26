# 2339. All the Matches of the League

**Difficulty:** Easy
**Pattern:** SQL / CROSS JOIN / Self-Join
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

Generate every ordered pair `(home_team, away_team)` where the two teams are different. With `n` teams, exactly `n × (n - 1)` such pairs (every team plays every other team twice — once home, once away).

**Key insight:** This is a **Cartesian product with a filter**. Cross-join the table with itself, exclude rows where home == away.

The filter `WHERE t1.team_name != t2.team_name` removes the n "self-match" rows.

---

## LAYER 1: Line-by-Line Explanation

**Preferred — explicit CROSS JOIN:**

```sql
SELECT
    t1.team_name AS home_team,
    t2.team_name AS away_team
FROM Teams t1
CROSS JOIN Teams t2
WHERE t1.team_name != t2.team_name;
```

- **`FROM Teams t1`** — first copy (home)
- **`CROSS JOIN Teams t2`** — pair every t1 row with every t2 row → n × n rows
- **`WHERE t1.team_name != t2.team_name`** — drop n self-pairs → n × (n - 1) rows
- **`SELECT ... AS home_team, ... AS away_team`** — rename columns

**Comma-style cross join (equivalent, older syntax):**

```sql
SELECT t1.team_name AS home_team, t2.team_name AS away_team
FROM Teams t1, Teams t2
WHERE t1.team_name != t2.team_name;
```

**Pandas:**

```python
import pandas as pd

def find_all_matches(teams: pd.DataFrame) -> pd.DataFrame:
    matches = teams.merge(teams, how='cross', suffixes=('_home', '_away'))
    matches = matches[matches['team_name_home'] != matches['team_name_away']]
    return matches.rename(columns={
        'team_name_home': 'home_team',
        'team_name_away': 'away_team'
    })
```

---

## LAYER 2: Worked Examples

**Example 1: 3 teams → 6 matches**

Input: Leetcode FC, Ahly SC, Real Madrid

Step 1 — cross join produces 3 × 3 = 9 rows (including 3 self-pairs).

Step 2 — apply `t1.team_name != t2.team_name`:

| home_team | away_team |
|-----------|-----------|
| Leetcode FC | Ahly SC |
| Leetcode FC | Real Madrid |
| Ahly SC | Leetcode FC |
| Ahly SC | Real Madrid |
| Real Madrid | Leetcode FC |
| Real Madrid | Ahly SC |

6 rows = 3 × (3 - 1) ✓

**Why exactly n × (n − 1):**

- Unordered pairs: C(n, 2) = n(n − 1)/2
- Each contributes 2 ordered rows → total n(n − 1)

**Edge cases:**
- Single team: 0 matches (no opponent to play)
- Empty table: 0 matches

**WARNING — DON'T use `t1.team_name < t2.team_name`:**

That returns only UNORDERED pairs (each pair once, not twice). Would give 3 rows instead of 6 — missing swap-home-and-away matches.

---

## LAYER 3: Key Insights

| Approach | Result | Complexity |
|----------|--------|------------|
| `CROSS JOIN` + `WHERE !=` | All ordered pairs | O(n²) |
| Comma cross join | Same result | O(n²) |
| `INNER JOIN ... ON t1.name != t2.name` | Same result | O(n²) |

**Why CROSS JOIN is the right primitive:**

`CROSS JOIN` exists specifically for "generate all combinations" — the SQL counterpart of nested loops. When you see "list all pairs" or "every combination," CROSS JOIN is the first tool.

**Three classic flavors of self-join:**

| Filter | Use case |
|--------|----------|
| `t1.id != t2.id` | All ordered distinct pairs (this problem) |
| `t1.id < t2.id` | Unordered distinct pairs (friend pairs, edges) |
| `t1.id <= t2.id` | Pairs including self (reflexive relations, rare) |

**Why not INNER JOIN with `!=` in ON clause?**

It works but reads oddly — INNER JOIN usually joins on equality. CROSS JOIN + WHERE is cleaner for combinatorial generation.

**Performance:** O(n²) intermediate rows. For 10,000 teams that's 100M rows — slow. In league scheduling, n is small (10-30 teams), so fine.

---

## LAYER 4: Interview Variations

• **Each pair plays once (unordered):** Change filter to `t1.team_name < t2.team_name`. Returns C(n, 2) rows.
• **Including self-scrimmages:** Remove WHERE entirely. Returns n² rows.
• **Round-robin with weeks assigned:** Add `ROW_NUMBER() OVER()` for match IDs.
• **Same-conference only:** Add `WHERE t1.conference_id = t2.conference_id`.
• **K matches per pair:** Cross-join with a numbers table of size K.
• **Multiple leagues:** Add `WHERE t1.league_id = t2.league_id`.
• **Triplets (3 teams per match):** Triple cross join with `t1.id < t2.id < t3.id`. Returns C(n, 3).
• **Exclude rivalry matches:** LEFT JOIN Rivalries; filter for no match.
• **What if Teams has duplicates?** Wrap source in `(SELECT DISTINCT team_name FROM Teams)`.
• **Materialize for large tables:** Build a Matches table once rather than re-cross-joining.
• **Recover team list from matches:** `SELECT DISTINCT home_team FROM Matches`.

---

## LAYER 5: Cheat Sheet

```sql
-- Preferred
SELECT t1.team_name AS home_team, t2.team_name AS away_team
FROM Teams t1
CROSS JOIN Teams t2
WHERE t1.team_name != t2.team_name;
```

**Comma cross join:**
```sql
SELECT t1.team_name AS home_team, t2.team_name AS away_team
FROM Teams t1, Teams t2
WHERE t1.team_name <> t2.team_name;
```

**Pandas:**
```python
def find_all_matches(teams):
    m = teams.merge(teams, how='cross', suffixes=('_home', '_away'))
    m = m[m['team_name_home'] != m['team_name_away']]
    return m.rename(columns={'team_name_home': 'home_team', 'team_name_away': 'away_team'})
```

**Pandas (older versions without 'cross' merge):**
```python
def find_all_matches(teams):
    teams['key'] = 1
    m = teams.merge(teams, on='key', suffixes=('_home', '_away'))
    m = m[m['team_name_home'] != m['team_name_away']]
    return m[['team_name_home', 'team_name_away']].rename(
        columns={'team_name_home': 'home_team', 'team_name_away': 'away_team'})
```

---

**Time:** O(n²) | **Space:** O(n²) for result

**Pattern flag:** "List all pairs / combinations of entities" in SQL → **CROSS JOIN**. Three filter templates: `!=` for ordered pairs, `<` for unordered pairs, no filter for all combinations including self. Pick based on whether order matters and whether self-pairs are valid.
