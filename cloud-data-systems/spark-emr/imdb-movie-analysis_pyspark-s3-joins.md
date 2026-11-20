> **Skill:** cloud-data-systems (PySpark on AWS / JupyterLab) · **Source:** Baruch CIS 9760 Project 1 · **Mode:** Applied project (coursework brief, self-executed) · **Date:** ~2026-11
> Companion: [HDFS/MapReduce/Spark concepts (Module 6)](pyspark-emr_hdfs-mapreduce-concepts.md) · index: [cloud-data-systems README](../README.md)
> *Artifact: `Project_Analysis.ipynb` (99 cells, JupyterLab). Exported to a 28-page PDF on Windows via the HTML→Edge print fallback — see [portfolio-defense Windows journal](../../portfolio-defense/windows-wsl2-reconstruction_journal.md).*

# CIS 9760 Project 1 — IMDB analysis in PySpark (S3 → Spark → joins)

**Why 🛡️:** this is the *scale* half of my portfolio-defense gap made concrete — a real multi-table
analysis that runs on Spark reading from S3, not pandas on a laptop. "How would this run on data too
big for one machine?" now has a worked answer, not just vocabulary.

## The shape of the work
Four IMDB tables loaded from **S3 into Spark DataFrames** — a small star schema:
`name.basics` (people) · `title.basics` (titles) · `title.principals` (who worked on what) ·
`title.ratings` (averageRating, numVotes). Then schema inspection, cleaning, aggregation, charts,
and eight multi-table analytical questions.

## The three techniques worth defending

**1. Explode a delimited column into an association table.** Genres arrive as one packed column.
`split` + `explode` turns one title with `Action|Adventure` into two rows — a tidy
title↔genre association table you can `countDistinct` and `groupBy`.
```python
from pyspark.sql.functions import col, split, explode, countDistinct, avg
genre_assoc = titles.select("tconst", explode(split(col("genres"), ",")).alias("Genre"))
```

**2. Clean the null marker *before* counting.** IMDb encodes "no genre" as the literal `\N`. Counting
distinct genres without filtering it **silently inflates the count by one** ("Oops! Something is
off!"). Filtering `\N` first is the fix — a data-quality reflex, the same family as MP#02's `NA`
grid handling.

**3. Aggregate in the cluster, then `.collect()` only the small result.** For the matplotlib charts
I aggregate first (`groupBy().avg()`, `.count()`) and collect *only the aggregated table, never the
full IMDb dataset* — because `collect()` pulls data to the driver, and collecting millions of rows
is how you OOM the driver. Aggregate → collect-small → plot.

## The eight join questions (what they exercise)
2003 films with >50k votes and rating ≥8 (filter + join ratings) · Cillian Murphy's films since 2007
and his highest-rated (people→principals→titles→ratings, `max`) · Zendaya's films per year
(`groupBy` year) · Audrey Hepburn's age at death (from `name.basics` birth/death) · Chris Evans'
highest-rated with ties shown · Eastwood vs Ford average rating (two actor subsets, compare) ·
Depp & Bonham Carter films *together* (self-join on shared titles) · Harry Potter franchise high/low
for Radcliffe (`max`/`min`). Each needs 2–4 tables merged "from scratch."

## Development layer — fluency
- **Can now do:** load multiple tables from S3 into Spark, inspect/drop columns, `explode` a
  delimited field, filter null markers, join 2–4 tables, aggregate, and chart the collected result.
- **Still shaky:** writing the trickier self-joins (co-acting pairs) without iterating.
- **Reproduce cold?** Partial — the load/inspect/explode/aggregate spine yes; the multi-table join
  questions I'd rebuild with some trial.

## Defense layer — interview
- **Line (Spark hygiene):** *"To chart from Spark I aggregate in the cluster and only `.collect()`
  the small result — collect pulls everything to the driver, so collecting the full dataset is how
  you crash it. Aggregate first, collect a handful of rows, then plot."*
- **Line (data quality):** *"IMDb marks missing genres with `\N`; counting distinct genres before
  filtering it inflates the count by one — I filter the null marker before any `countDistinct`."*
- **Follow-up risk:** "optimize this join / explain the shuffle" — I can reason about it (Module 6
  concepts) but haven't tuned partitions in practice.

## Honest limit
Not claiming Spark *performance tuning* (partitioning, broadcast joins, caching strategy) — I can
run and reason about correct Spark, not yet optimize it.

## Open thread
This moves PySpark specifically from "concept + one EMR run" toward "used end-to-end" — see the
dashboard note. Next: tune one join (broadcast the small table) to start the optimization story.
