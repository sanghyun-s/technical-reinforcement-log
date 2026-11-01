> **Skill:** sql / coding-drills (CTEs + CASE + HAVING + window ranking) · **Source:** LeetCode #3278 · **Mode:** New · **Date:** 2026-11-01
> Difficulty: Medium · Family: Multi-CTE / Window Functions
> *🔁 Closed loop: this uses exactly the CTE + CASE + `ROW_NUMBER() OVER(PARTITION BY ...)` stack from the DataCamp "Data Manipulation in SQL" course — course → cold application.*

# 3278. Find Candidates for Data Scientist Position II

## Problem
`Candidates(candidate_id, skill, proficiency)` and `Projects(project_id, skill, importance)`. For each project, the best candidate who has **all** required skills, scored: start 100, +10 per skill where `proficiency > importance`, −5 where `proficiency < importance`, 0 if equal. Top score per project; ties → lower `candidate_id`; skip projects with no qualifying candidate. Order by `project_id`.

## The insight — decompose into three CTEs
This is the whole DataCamp course in one problem. Break the compound requirement into named stages:
1. **ProjectSkillCount** — how many skills each project needs (`COUNT(skill) GROUP BY project_id`). Needed for the "must have ALL skills" test.
2. **CandidateScores** — join candidates to projects **on `skill`**, compute the score with a `CASE` inside `SUM()`, and enforce "has all skills" with **`HAVING COUNT(p.skill) = total_skills`** (a candidate only reaches the full count if they match every required skill).
3. **RankedCandidates** — `ROW_NUMBER() OVER (PARTITION BY project_id ORDER BY score DESC, candidate_id ASC)` — the tie-break is encoded directly in the `ORDER BY`. Keep `rn = 1`.

## My solution
```sql
WITH ProjectSkillCount AS (
  SELECT project_id, COUNT(skill) AS total_skills
  FROM Projects GROUP BY project_id
),
CandidateScores AS (
  SELECT p.project_id, c.candidate_id,
         100 + SUM(CASE WHEN c.proficiency > p.importance THEN 10
                        WHEN c.proficiency < p.importance THEN -5
                        ELSE 0 END) AS score
  FROM Projects p
  INNER JOIN Candidates c       ON p.skill = c.skill
  INNER JOIN ProjectSkillCount psc ON p.project_id = psc.project_id
  GROUP BY p.project_id, c.candidate_id, psc.total_skills
  HAVING COUNT(p.skill) = psc.total_skills        -- "must have ALL skills"
),
RankedCandidates AS (
  SELECT project_id, candidate_id, score,
         ROW_NUMBER() OVER (PARTITION BY project_id
                            ORDER BY score DESC, candidate_id ASC) AS rn
  FROM CandidateScores
)
SELECT project_id, candidate_id, score
FROM RankedCandidates
WHERE rn = 1
ORDER BY project_id ASC;
```

## The key techniques (each from the course)
- **CASE inside SUM** = conditional accumulation (the scoring rule) — straight from the course's CASE chapter.
- **`HAVING COUNT(...) = total`** = the "has every required skill" filter — a post-aggregation filter, not `WHERE`.
- **`ROW_NUMBER() OVER(PARTITION BY ... ORDER BY score DESC, id ASC)`** = "top-N per group with a tie-break" — the canonical window-function pattern; `WHERE rn = 1` picks the winner. Skipping projects with no candidate is automatic (they never appear in `CandidateScores`).

## Reproduce cold
The 3-CTE skeleton; `CASE`-in-`SUM` scoring; `HAVING` for the all-skills gate; `ROW_NUMBER` partitioned with a 2-key order + `rn = 1`.

## Interview line candidate
> For "best row per group with a tie-break," I rank inside each group with `ROW_NUMBER() OVER(PARTITION BY group ORDER BY score DESC, id ASC)` and keep `rn = 1` — the tie-break lives in the ORDER BY. I built the score with a `CASE` inside `SUM`, and enforced "must have all required skills" with a `HAVING COUNT = total` after the join, which also drops projects with no qualifying candidate for free.
