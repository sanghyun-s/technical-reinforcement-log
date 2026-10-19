> **Skill:** cloud-data-systems (Apache Spark + AWS EMR; HDFS & MapReduce concepts) · **Source:** Baruch CIS 9760 Module 6 · **Mode:** Coursework · **Date:** 2026-10-07 (deck) / EMR homework ~2026-10-14
> Companion: [OpenSearch REST client](../opensearch/Module5_opensearch-docker.md) · [Midterm review](../docker-opensearch-spark_concept-review.md) · index: [cloud-data-systems README](../README.md)
> *Converted from the module review doc; concepts + EMR workflow preserved. The Spark notebook artifact is `EMRNotebook_Sam_Seong.ipynb` (add alongside if keeping the raw code).*

# CIS 9760 Module 6 — HDFS · MapReduce · Apache Spark (on AWS EMR)

**Why 🛡️:** distributed storage + processing is the *scale* half of the deployment/scale gap my
§12.2 names. It also connects to the portfolio: "how would this run on data too big for one
machine?" is a real interview question I can now answer with vocabulary and a hands-on EMR run.

**Format note:** Module 6 was the course's first fully-asynchronous session (5 recorded decks,
~45 min, no live hands-on). HDFS and MapReduce exist only as slides; the one practice was the EMR
Spark homework — so those two topics are written down here rather than skimmed.

## The organizing split
**HDFS = storage. MapReduce = processing. Spark replaces the processing half while still reading
from the storage half.** Any question that pairs them wrongly is testing this one sentence.

## HDFS (distributed storage)
- Apache, open-source; a framework for large-scale processing in two halves (HDFS + MapReduce),
  inspired by Google's architecture, written in Java (Python/other APIs available).
- Runs on **commodity hardware**, decentralized, **automatic data replication**, handles node
  failure (a server holding replicas failing), elastic scale by adding/removing nodes.
- **"Write once, read many times"** is a *design constraint*, not a feature — HDFS isn't built for
  in-place edits. **Same model as S3 objects** (immutable; see Module 2) — good for analytics, bad
  for transactional workloads.
- **NameNode** (master: metadata, where blocks live) + **DataNodes** (workers: store the blocks).

## MapReduce (the original processing model)
- **Map** → transform/emit key-value pairs; **Shuffle & Sort** → group by key across nodes;
  **Reduce** → aggregate per key. (The Shuffle-and-Sort phase is a named midterm topic.)

## Apache Spark (the modern processing engine)
- Replaces MapReduce's processing with in-memory, DAG-based execution — much faster for iterative
  work.
- **Architecture:** **Driver** (runs the program, builds the plan) → **Cluster Manager** (allocates
  resources) → **Workers**, each running **Executors** (do the actual tasks).
- **Transformations vs Actions — the key Spark idea:** transformations (`filter`, `select`, `map`)
  are **lazy** (they just extend the plan/DAG); **actions** (`count`, `collect`, `show`, write)
  **trigger** the computation. Nothing runs until an action is called.

## The EMR homework (the hands-on half)
Provisioned an AWS **EMR** cluster and ran a **PySpark** job against data on the cluster — the Spark
half of the module, exercised end-to-end (the HDFS/MapReduce half stayed conceptual). Artifact:
`EMRNotebook_Sam_Seong.ipynb`. Same cloud-ops discipline as Modules 2–5: provision → run → **tear
down** (EMR is billable), and debug-by-layer when something fails.

## Interview line candidate (🛡️ — not yet promoted)
> I ran a PySpark job on an AWS EMR cluster, and the concept that makes Spark click is lazy
> evaluation: transformations just build a DAG and nothing computes until an action like `count()`
> or a write triggers it — which is why you can chain filters cheaply and why the *action* is where
> the cost and the bugs show up. Spark replaced MapReduce's processing while still reading from
> HDFS-style storage, which is the same immutable write-once-read-many model as S3.

## Reproduce cold
- State the HDFS/MapReduce/Spark split; NameNode vs DataNode; driver/cluster-manager/worker/executor.
- Explain lazy transformations vs triggering actions with an example.
- Provision an EMR cluster and run a basic PySpark job, then tear it down.
