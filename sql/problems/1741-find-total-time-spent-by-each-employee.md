> **Skill:** sql / coding-drills (GROUP BY + SUM of a derived column + alias) · **Source:** LeetCode #1741 · **Mode:** New · **Date:** 2026-11-01
> Difficulty: Easy · Family: Aggregation

# 1741. Find Total Time Spent by Each Employee

## Problem
`Employees(emp_id, event_day, in_time, out_time)` — one row per office entry/exit; an employee can enter/leave several times a day. Total minutes per employee per day = `SUM(out_time - in_time)`.

## The insight
Aggregate a **computed expression**, not a stored column: `SUM(out_time - in_time)` per `(event_day, emp_id)`. The per-row duration is `out_time - in_time`; summing within the group handles the "multiple entries per day" case for free.

## My solution
```sql
SELECT
  event_day AS day,
  emp_id,
  SUM(out_time - in_time) AS total_time
FROM Employees
GROUP BY event_day, emp_id;
```

## The lesson
Two small-but-real habits: you can **aggregate an arithmetic expression** directly inside `SUM()`, and the required output column name (`day`) comes from an **alias** (`event_day AS day`) — read the expected schema for the exact names. Composite grouping again (day × employee).

## Reproduce cold
`SUM(expr)` over a two-column group with output aliasing; why summing the per-row difference covers multiple daily events.
