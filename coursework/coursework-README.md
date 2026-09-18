# Coursework Log

Graduate coursework practice — labs, pre-assignments, and exercises — logged alongside the
LeetCode and DataCamp tracks. This is **breadth from my degree**, kept honest by a relevance tag
so it never gets confused with the focused interview-defense material.

---

## The relevance tag (the honesty gate)

Every entry carries one tag. The tag is the whole point: it stops general coursework from
masquerading as portfolio defense.

| Tag | Meaning | Shows up in interview prep? |
|---|---|---|
| 🛡️ **Portfolio-Defense** | Directly defends a portfolio gap or a target-JD keyword | **Yes** — feeds INTERVIEW-SENTENCES + the deployment-gap answer |
| 📚 **General Coursework** | Real learning, breadth, degree requirement — but off the 2027 axis | No — logged for completeness, not pitched |

**The test (same as the repo's governing rule):** a topic is 🛡️ only if it closes a code-fluency
gap, defends a portfolio technology, or is named in a target JD. Everything else is 📚 — which is
not a demotion, just accurate labeling.

---

## Logged journals

| Date | Journal | Courses | Tags |
|---|---|---|---|
| 2026-09-14 | [Week of 09-14](./2026-09-14_coursework-journal.md) | CIS 9760 · STA 9750 | 🛡️ + 📚 |

## Current coursework — tagged

| Topic | Tag | Why | Defends |
|---|---|---|---|
| **Docker** | 🛡️ Portfolio-Defense | Containerization = the "how would this run in production?" answer — now practiced on EC2 | §12.2 Layer A "deployment gap"; prototype→production |
| **AWS (S3 / EC2 / IAM)** | 🛡️ Portfolio-Defense | Cloud provisioning, storage, scoped access — where the apps would deploy | Deployment gap; §6.1 Tier 1/2 JD keyword |
| **Linux terminal / CLI** | 🛡️ Portfolio-Defense | Server management without a GUI; the ops layer under deployment | §12.2 operational layer |
| **AWS / cloud management** | 🛡️ Portfolio-Defense | Where the apps would actually deploy; also a common JD keyword | Deployment gap; §6.1 Tier 1/2 job families |
| **Cloud computing (concepts)** | 🛡️ Portfolio-Defense | Operational/scaling vocabulary for a production conversation | §12.2 "operational risk", scaling, monitoring |
| **Quarto Markdown** | 📚 General Coursework | Reproducible reporting; useful, but not on the Python/SQL/AI-builder axis | — (adjacent to data-analyst reporting if a JD names it) |
| **R** | 📚 General Coursework | New language for coursework; Python/SQL remain my primary tools | — (upgrade to 🛡️ only if a target JD names R) |

> **Note on the deployment gap:** Docker + AWS + cloud are the *most valuable* coursework here,
> because they patch a real weakness the portfolio has. My apps are portfolio-grade prototypes
> (Tier 3 base answer: "not production-hardened"). Learning containerization and cloud deployment
> is exactly what moves that answer from "I couldn't" toward "here's how I would."

---

## Structure

```
coursework/
├── coursework-README.md   ← this file: the tag system + index
├── docker/                ← 🛡️ labs, notes
├── aws-cloud/             ← 🛡️ labs, notes
├── r/                     ← 📚 labs, pre-assignments
├── quarto/                ← 📚 notes
└── <course>/              ← one folder per course/topic
```

## How each entry gets logged

Same workflow as the DataCamp track — you send the lab / pre-assignment / practice file, I draft
a summary MD with:
1. **What it covered** (concepts, not a transcript)
2. **The tag** (🛡️ or 📚) + one line of why
3. **Direct hits on the portfolio** — *only for 🛡️ entries* (for 📚, this is "n/a — breadth")
4. **Interview sentence** — *only for 🛡️ entries that earn one*

🛡️ entries that produce a defensible line graduate to INTERVIEW-SENTENCES like any course.
📚 entries stay here — logged, visible, honest, not pitched.

## Surfacing on the front page

WEEKLY-LOG gets a **Coursework** track alongside LeetCode/DataCamp, with the tag inline — so the
activity shows (momentum) while the tag keeps the honesty (a 📚 R lab reads as breadth, a 🛡️
Docker lab reads as portfolio defense).
