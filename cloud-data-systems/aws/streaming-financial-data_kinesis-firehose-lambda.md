> **Skill:** cloud-data-systems (AWS streaming) · **Source:** Baruch CIS 9760 Module 11 (in-class/homework) · **Mode:** Coursework (hands-on) · **Date:** ~2026-11
> Companion AWS work: [S3/EC2/IAM & terminal (Modules 2–3)](Module2-3_AWS-terminal.md) · [Spark on EMR (Module 6)](../spark-emr/pyspark-emr_hdfs-mapreduce-concepts.md) · index: [cloud-data-systems README](../README.md)
> *Artifacts: `ExecResults` + `S3` screenshots (Lambda run + bucket). Deliverables submitted to Brightspace.*

# CIS 9760 Module 11 — Streaming data: Kinesis → Firehose → S3, producer on Lambda

**Why 🛡️:** adds the **streaming** leg to my AWS evidence (I already had batch/storage via S3/EC2
and processing via EMR/Spark). "How does data arrive continuously, not as a file?" now has a
hands-on answer.

## The pipeline, in one line
A **Kinesis Data Stream** (on-demand capacity) feeds a **Firehose** delivery stream that **batches to
S3**; the data *producer* runs as an **event-driven Lambda** instead of a long-lived EC2 script.

## What's worth defending
- **Why re-do the EC2 producer as a Lambda?** The previous exercise ran the generator on an EC2
  instance you have to start, pay for, and stop. Lambda is **event-driven** — it runs when invoked
  and stops, so there's no server to manage. That trade (managed/ephemeral vs. always-on) is the
  point of the module.
- **It needed different plumbing than a script:** an **IAM execution role** with Kinesis/Firehose
  policies attached (Lambda assumes a role, it doesn't use your keys), and **raised Memory (3000 MB)
  / Timeout (15 min)** because the default "run fast and finish" Lambda profile is too tight for a
  2,000-record generation loop.
- **`lambda_handler(event, context)` is the entry point** AWS calls on invocation. Here the **test
  event is a formality** — the function generates its own random financial data internally, so the
  event payload doesn't drive it. Knowing *why* the event is ignored is the understanding, not just
  that it ran.
- **Firehose buffer hints** (size 1, interval 60s) control how often batches land in S3 — the
  latency/file-count trade for a delivery stream.

## Development layer — fluency
- **Can now do:** stand up a Kinesis stream + Firehose-to-S3, author/deploy a Lambda producer,
  attach the IAM policies it needs, tune memory/timeout, run via a test event, and verify output in
  the bucket.
- **Still shaky:** wiring a *consumer* side and real event-source triggers (this producer was
  self-contained); writing the IAM policy JSON from scratch rather than attaching managed policies.
- **Reproduce cold?** Partial — the console workflow yes; the exact policy names and trust-role setup
  I'd reconstruct with the console/docs.

## Defense layer — interview
- **Line:** *"A Kinesis stream feeds a Firehose that batches to S3, and I ran the producer as an
  event-driven Lambda rather than a long-lived EC2 process — which is exactly why it needed an IAM
  execution role and a raised timeout and memory, instead of just a server I keep running."*
- **Follow-up risk:** "set up the consumer / trigger from S3 events" — I've done the producer side,
  not the full event-driven consumer loop.

## Honest limit
Not claiming end-to-end streaming architecture design — I ran a guided producer→stream→S3 path and
understand each hop, but haven't designed consumers, retries, or throughput sizing myself.
