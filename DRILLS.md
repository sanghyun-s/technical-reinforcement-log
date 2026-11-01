# Coding Drills — policy

How I run cold problem-solving practice (LeetCode and similar). This is the *approach*; the actual
problems live per-competency, and the cadence is tracked in [WEEKLY-LOG](./WEEKLY-LOG.md).

> **The point is not problem count or GitHub green squares.** It's: *can I solve or explain this
> independently now?* A week of fewer, genuinely-cold problems beats a week of copied solutions.

## Languages & weighting

| Language | Weight | Home folder |
|---|---|---|
| **Python** | Primary | [`python/coding-drills/`](./python/coding-drills/) (`problems/`, `archive/`) |
| **SQL** | Core (balancing up) | [`sql/problems/`](./sql/problems/) |
| **R** | Core (balancing up) | [`r-analytics/drills/`](./r-analytics/drills/) |
| **Java** | Occasional | `java/drills/` (created on first problem) |

Python stays primary; SQL and R are deliberately added to balance toward analyst/BI/data roles.
Java is a possibility, lightly weighted — folder gets created when the first Java drill lands, not before.

**Why per-competency, not one `coding-drills/` folder:** a drill is both a *pattern* and a
*language workout*, and V2's rule is folder = competency. Keeping SQL drills in `sql/` means someone
browsing that skill sees the cold evidence right there. The unifying layer is this doc + WEEKLY-LOG,
not a folder.

## Cadence (target, not a cage)

- **≥ 4 problems per week**, committed to Git **twice a week**.
- Managed independently — I pick problems by current gap, not a fixed list.
- Subject to change; a lighter but deeper week still counts.

## Mode tags (the honesty gate — same as the rest of the log)

Every drill records how it was solved, in its file header and on WEEKLY-LOG:
`New` (first encounter) · `Cold solve` (unaided) · `Reference recall` (archive open) ·
`Re-drill` (returned to a prior problem) · `Course-guided`. **Cold-first is the goal:** attempt
unaided → find the exact point of failure → *then* look at a hint/reference → reproduce later.

## Per-problem file (lightweight)

Competency-first filename (`NNNN-slug.md`), with a header carrying `Skill · Source · Mode · Date ·
Difficulty · Family`, then: the insight, my solution(s), complexity, mistakes/debugging if any, a
"reproduce cold" line, and an interview-line candidate if one is earned. Re-drills of prior problems
also get a row in [RE-DRILL-LOG](./python/coding-drills/RE-DRILL-LOG.md).

## Structured courses — PARKED

**LeetCode Explore** (<https://leetcode.com/explore/>) offers DataCamp-style structured curricula
(chapters/activities). **Parked for now** (see [ROADMAP](./ROADMAP.md)) — a candidate way to expand
the log with structured drill content if I want curriculum over ad-hoc problem selection. If started,
course notes file under the relevant competency (an SQL Explore card → `sql/`, etc.), source tagged
`LeetCode Explore`.
