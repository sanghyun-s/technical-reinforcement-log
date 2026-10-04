> **Coursework · CIS 9760 Big Data Technologies · Modules 2–3** · Tag: 🛡️ **Portfolio-Defense**
> Split out of the [Week of 09-14 hub](../../2026-09-14_coursework-synthesis.md). Companion:
> [Module 4 — Docker + REST API app](../docker/Module4_docker-api-app.md) · index: [coursework-README](../../_legacy/coursework-README-v1.md)

# CIS 9760 Modules 2–3 — AWS foundations + Linux terminal + Docker on a cloud server

**Why 🛡️:** this is the **deployment-and-scale gap** my §12.2 names ("deployment gap," "prototype
vs. production," "operational risk"). My apps are portfolio-grade prototypes; this coursework moves
the Tier-3 answer from "not production-hardened" toward "here's how I'd deploy it."

## Module 2 — AWS foundations

**Cost guardrail first.** Set a monthly **Budget** ($15–20 cap) via Billing & Cost Management under
the root user — a precaution against forgotten running resources. *Operational-cost awareness is
itself a production-maturity signal.*

**S3 (object storage).** Created a bucket, uploaded an object, set object-level permissions.
- A **bucket** is the top-level container; **bucket names are globally unique** across all AWS
  accounts (mine: `cis9760-bucket-s3-assignment-sam-seong`, region `us-east-2` / Ohio).
- An **object** = data + a **key** (unique id within the bucket) + optional **metadata**.
- **Objects are immutable** — you can't edit in place; you upload a new version to change it.

**EC2 (compute).**
- EC2 = scalable virtual servers; scale up/down on demand instead of buying hardware.
- **Root user vs IAM user** — root for account setup; **IAM** (users/groups/roles + policies) for
  scoped, least-privilege access. The security vocabulary my §12.2 flags.
- **Public DNS is renewed every time the instance starts** — connection strings aren't stable
  across restarts. (Practical gotcha with real deployment implications.)

## Module 3 — Linux terminal + Docker on the server

**SSH into EC2, start/stop to conserve hours** — the instance is billed while running, so
stop-when-done is both cost-hygiene and the normal ops rhythm.

**Docker (containerized deployment).** Ran a `filebrowser` container on the EC2 box:
```bash
docker run -d --name filebrowser --user 0:0 \
  -p 5001:80 \
  -v "/home/ubuntu/files":/srv \
  -v "/home/ubuntu/filebrowser-db/filebrowser.db":/database.db \
  --restart unless-stopped filebrowser/filebrowser
docker logs filebrowser        # read container output
docker stop / rm filebrowser   # lifecycle management
```
Real containerization: **port mapping** (`-p 5001:80`), **volume mounts** (`-v`, data persists
outside the container), a **restart policy** (`unless-stopped`), reading **logs**. Deployment-grade
work, not "intro to cloud."

**Terminal command fluency** (the 26-step file-manipulation exercise, relative paths):
`ls` / `ls -ahl` · `pwd` · `cd` (absolute vs relative, `..` and `.`) · `mkdir` · `touch` ·
`mv` (rename *and* move) · `cp` / `cp -r` · `rm` / `rm -r`.

**What I actually learned — from the mistakes:**
- ⚠️ **The dangerous one:** `rm -r .. /SamllApplications` — a stray **space after `..`** meant
  "recursively delete the parent directory." `rm` refused (`refusing to remove '..'`), which saved
  me. **A misplaced space in `rm -r` turns a targeted delete into a catastrophe.** (Habit: `rm -i`,
  or read `rm -r` twice.)
- Typos I worked around (`SamllApplications`, `cd /home/hubntu`, `cp text.txt`) — CLI competence is
  built by *recovering* from these.
- `mv` with a relative destination fails from the wrong CWD; absolute paths or correct CWD fix it.
  Same "know where you are" discipline as the Quarto session model.

## Interview line earned (🛡️)

> I've provisioned and managed AWS resources hands-on — set cost budgets, created S3 buckets with
> scoped IAM permissions, launched EC2 instances, and run Docker containers on them over SSH with
> port mapping and volume mounts. So when I talk about my apps being portfolio prototypes, I can
> also speak concretely to how I'd containerize and deploy them.
