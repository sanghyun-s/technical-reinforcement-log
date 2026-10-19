> **Skill:** cloud-data-systems (review/synthesis) · **Source:** Baruch CIS 9760 midterm review · **Mode:** Review · **Date:** 2026-10-16
> Companion: [aws/Module2-3](./aws/Module2-3_AWS-terminal.md) · [docker/Module4](./docker/Module4_docker-api-app.md) · [opensearch/Module5](./opensearch/Module5_opensearch-docker.md) · [spark-emr/Module6](./spark-emr/pyspark-emr_hdfs-mapreduce-concepts.md)

# CIS 9760 Midterm Review — Modules 4–6 (Docker · Elasticsearch/OpenSearch · HDFS/MapReduce/Spark)

Filtered to what the midterm guidelines name. **Eight of twenty sample questions** come from these
three modules: Dockerfile definition + the `FROM` keyword, credentials in environment variables,
replica shards, Shuffle-and-Sort, the PySpark DAG, and two "which technology would you choose" scenarios.

## Module 4 — Containerization & Docker
- **Why containers:** repeatable results, run-anywhere, transport the environment (kills
  "works on my machine"); **packaging the environment matters more than the implementation.**
- **VM vs container:** a **VM** virtualizes *hardware* via a **hypervisor** (its own full OS);
  a **container** shares the host **OS kernel** (lighter, faster).
- **Full vs partial virtualization:** full = whole hardware simulated (**EC2/HVM**, run any guest
  OS); partial/API-level = OS/execution virtualized (**AWS Lambda**, near-instant start).
- *Holding thought:* my EC2 instance is a VM; everything I ran inside it this semester was a
  container — the course infra shows both at once.
- **Dockerfile → image → running container.** `FROM` sets the base image. **Credentials go in
  environment variables, never in the image** (the Module 4/5 secret-management lesson).

## Module 5 — Elasticsearch / OpenSearch
- Document store queried over REST; **node roles** + **shards** (primary + **replica shards** for
  resilience/throughput). SQL equivalents: index ≈ table, document ≈ row, field ≈ column.

## Module 6 — HDFS / MapReduce / Spark
- **HDFS = storage · MapReduce = processing · Spark = modern processing** (reads HDFS-style storage).
- **MapReduce:** Map → **Shuffle & Sort** → Reduce.
- **Spark architecture:** driver → cluster manager → workers → executors.
- **Transformations are lazy; actions trigger computation** (the DAG idea).

## The "which technology" meta-question
The two scenario questions test matching a tool to a need: object storage (S3) vs search
(OpenSearch) vs distributed processing (Spark/EMR) vs container packaging (Docker) vs raw compute
(EC2). The discriminator is always *what the workload actually needs* — the same "match the tool to
the constraint" judgment that runs through my coding-drills.
