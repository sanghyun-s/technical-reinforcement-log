# Coursework Journal — Week of 2026-09-14

Compiled record of two graduate courses this week, tagged by relevance per the
[coursework tag system](./coursework-README.md). Raw proof-of-work and the two full STA
sub-journals are preserved in the course subfolders; this file is the compiled takeaways.

| Course | Tag | This week |
|---|---|---|
| **CIS 9760 — Big Data Technologies** | 🛡️ **Portfolio-Defense** | AWS (budget, S3, EC2) + Linux terminal + Docker on a cloud server |
| **STA 9750 — Software Tools for Reproducible Research** | 📚 **General Coursework** | R (vectors, functions, control flow) + Markdown/Quarto + git |

> **The synthesis worth leading with:** Quarto's *render-in-a-fresh-session* (STA) and Docker's
> *run-in-a-clean-container* (CIS) are the **same principle** — the artifact must run from
> scratch, not from accumulated hidden state. That idea spans both courses *and* directly
> answers my portfolio's prototype-vs-production weakness (§12.2).

---

## 🛡️ CIS 9760 — Big Data Technologies (Portfolio-Defense)

**Why 🛡️:** this is the **deployment-and-scale gap** my §12.2 names ("deployment gap,"
"prototype vs. production," "operational risk"). My apps are portfolio-grade prototypes; this is
the coursework that moves the Tier-3 answer from "not production-hardened" toward "here's how I'd
deploy it."

### Module 2 — AWS foundations

**Cost guardrail first.** Set a monthly **Budget** ($15–20 cap) via Billing & Cost Management
under the root user — a precaution against forgotten running resources. *Operational-cost
awareness is itself a production-maturity signal.*

**S3 (object storage).** Created a bucket, uploaded an object, set object-level permissions.
Key concepts I can now explain:
- A **bucket** is the top-level container; **bucket names are globally unique** across all AWS
  accounts (mine: `cis9760-bucket-s3-assignment-sam-seong`, region `us-east-2` / Ohio).
- An **object** = data + a **key** (unique id within the bucket) + optional **metadata**.
- **Objects are immutable** — you can't edit in place; you upload a new version to change it.
  (Proof: `https://cis9760-bucket-s3-assignment-sam-seong.s3.us-east-2.amazonaws.com/honduras.jpeg`)

**EC2 (compute).** Provisioned and navigated a virtual server:
- EC2 = scalable virtual servers; scale up/down on demand instead of buying hardware.
- **Root user vs IAM user** — root for account setup; **IAM** (users/groups/roles + policies)
  for scoped, least-privilege access. This is the security-vocabulary my §12.2 flags.
- **Public DNS is renewed every time the instance starts** — so connection strings aren't stable
  across restarts. (Practical gotcha with real deployment implications.)

### Module 3 — Linux terminal + Docker on the server

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
This is real containerization: **port mapping** (`-p 5001:80`), **volume mounts** (`-v`, so data
persists outside the container), a **restart policy** (`unless-stopped`), and reading **logs**.
Not "intro to cloud" — this is deployment-grade work.

**Terminal command fluency** (the 26-step file-manipulation exercise, done with relative paths):
`ls` / `ls -ahl` · `pwd` · `cd` (absolute vs relative, `..` and `.`) · `mkdir` · `touch` ·
`mv` (rename *and* move) · `cp` / `cp -r` · `rm` / `rm -r`.

**What I actually learned — from the mistakes, not the clean run:**
- ⚠️ **The dangerous one:** `rm -r .. /SamllApplications` — a stray **space after `..`** meant
  "recursively delete the parent directory." `rm` refused (`refusing to remove '..'`), which
  saved me. **A misplaced space in `rm -r` turns a targeted delete into a catastrophe** — the
  single most dangerous CLI class. (Habit: `rm -i`, or read `rm -r` twice before Enter.)
- Typos I worked around (`SamllApplications`, `cd /home/hubntu`, `cp text.txt`) — CLI competence
  is built by *recovering* from these, not avoiding them.
- `mv` with a relative destination fails from the wrong CWD (`mv tomato.txt home/ubuntu/...` →
  "No such file"); absolute paths or correct CWD fix it. Same "know where you are" discipline as
  the Quarto session model below.

---

## 📚 STA 9750 — Software Tools for Reproducible Research (General Coursework)

**Why 📚:** the course core (R, Quarto, Markdown, git for reproducible research) is a degree
requirement off my Python/SQL/AI-builder axis. Real learning, logged for completeness — **not
pitched as portfolio defense.** *But* several lessons transfer directly to my Python work; those
are flagged 🔁 below.

**Full sub-journals (preserved as-is — my own proof of work):**
[`sta9750/Lab3_journal.md`](./sta9750/Lab3_journal.md) ·
[`sta9750/Week2_review.md`](./sta9750/Week2_review.md)

### Lecture 2 — Markdown · Quarto · git
The one idea under all three: **analysis and write-up should never be two things kept in sync by
hand.** Markdown removes proprietary formatting; Quarto removes copy-paste between code and doc;
git removes "final_v2_REALLY_final." WYSIWYM (write intent) vs WYSIWYG (manipulate appearance).
The **git box model** — `add` (put in box) → `commit` (seal + label) → `push` (ship) — is exactly
the workflow I already use for this repo.

### Lecture 3 / Lab 3 — R fundamentals
Vectors, `<-`, control flow, packages (`install.packages` once ever / `library()` every session),
custom functions with defaults, recycling. All five exercises done by predicting-then-typing.

### 🔁 Lessons that transfer straight to my Python/SQL work
These are R on the surface but universal underneath — worth keeping despite the 📚 tag:
- **Floating-point equality:** `hero_sqrt(3)^2 == 3` is `FALSE` (it's `2.9999999999999996`);
  `isTRUE(all.equal(...))` is the real test. **Identical to Python's `0.1+0.2 != 0.3`** — use a
  tolerance for computed floats, `==` only for ints/strings. (Ties to my Testing course.)
- **Silent recycling / vectorization bugs** — R warns about *ragged* recycling, not *wrong*
  recycling. Same silent-broadcast trap as pandas/numpy. "Silence means the arithmetic was tidy,
  never that I got what I meant."
- **`seq_len(n)` over `1:n`** — `1:0` is `c(1,0)` (loops twice on empty!). Same empty-range guard
  family as the `default=0` / empty-group guard from my recent LeetCode (3541).
- **Mutate vs. return, and scope leakage** — a loop's `i` leaks to global; a function's args
  don't. Same mutate-vs-return distinction running through my OOP and LeetCode notes.
- **I caught the instructor's own bug:** his Exercise 5 `hero_sqrt` uses `1:iter`, so
  `hero_sqrt(3, iter=0)` returns two iterations instead of the starting guess. `seq_len(iter)`
  fixes it. (Documented in the sub-journal.)

---

## Cross-course synthesis — reproducibility via clean environments

The through-line, and the most portfolio-relevant idea of the week:

| | STA 9750 (Quarto) | CIS 9760 (Docker) |
|---|---|---|
| Mechanism | Render runs in a **brand-new R session** every time | Container runs in an **isolated, from-scratch environment** |
| What it guarantees | The `.qmd` is a complete, reproducible record | The image runs the same anywhere, no hidden host state |
| Failure it prevents | "Works in my Console, breaks on Render" | "Works on my machine, breaks in production" |
| My portfolio link | — | Directly the prototype→production / deployment gap (§12.2) |

**Both say: the artifact must run from a clean state, not from accumulated session history.**
That's the same maturity leap my apps need — and now I can speak to it from two angles.

---

## Tag summary (the honesty gate)

- **🛡️ CIS 9760** → Portfolio-Defense. AWS provisioning, S3, IAM, EC2, Linux CLI, and Docker
  containerization patch the deployment/scale/ops gap. Earns interview lines (below).
- **📚 STA 9750** → General Coursework. R/Quarto/Markdown/git — logged for completeness, not
  pitched as core, except the 🔁 transferable lessons above. Upgrades to 🛡️ only if a target JD
  names R or reproducible-reporting.

## Interview lines earned (🛡️ only)

> I've provisioned and managed AWS resources hands-on — set cost budgets, created S3 buckets with
> scoped IAM permissions, launched EC2 instances, and run Docker containers on them over SSH with
> port mapping and volume mounts. So when I talk about my apps being portfolio prototypes, I can
> also speak concretely to how I'd containerize and deploy them.

> Quarto's render-in-a-fresh-session and Docker's clean container are the same reproducibility
> principle — run from scratch, not from hidden state. That's the discipline that separates a
> prototype that "works on my machine" from something production-ready.
