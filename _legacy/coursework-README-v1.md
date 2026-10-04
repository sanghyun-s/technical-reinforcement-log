# Coursework Log

Graduate coursework practice — labs, pre-assignments, exercises, and journals — logged alongside
the LeetCode and DataCamp tracks. Kept honest by a relevance tag so degree breadth never gets
confused with focused interview-defense material.

## The relevance tag (the honesty gate)

| Tag | Meaning | In interview prep? |
|---|---|---|
| 🛡️ **Portfolio-Defense** | Directly defends a portfolio gap or a target-JD keyword | **Yes** — feeds INTERVIEW-SENTENCES + the deployment-gap answer |
| 📚 **General Coursework** | Real learning / degree requirement — off the 2027 axis | No — logged for completeness, not pitched |

**The test:** a topic is 🛡️ only if it closes a code-fluency gap, defends a portfolio technology,
or is named in a target JD. Everything else is 📚 — accurate labeling, not a demotion. A 📚 entry
can still carry 🔁 *transferable* lessons that apply to my Python/SQL work.

## Structure — organized by course

```
coursework/
├── coursework-README.md             ← this file: tag system + index
├── 2026-09-14_coursework-journal.md ← weekly hub (cross-course synthesis + interview lines)
├── cis9760/                         ← 🛡️ Big Data Technologies
│   ├── Module2-3_AWS-terminal.md
│   ├── Module4_docker-api-app.md
│   └── Module5_opensearch-docker.md
└── sta9750/                         ← 📚 Software Tools for Reproducible Research
    ├── Week2_review.md
    ├── Lab3_journal.md
    ├── Lecture2-3_R-Quarto.md
    ├── MP00_peer-feedback.md
    ├── Lab4_journal.md
    └── Week5_base-R-grammar.md
```

## Logged journals — index

| Course | Doc | Tag | Topic |
|---|---|---|---|
| CIS 9760 | [Module2-3_AWS-terminal](./cis9760/Module2-3_AWS-terminal.md) | 🛡️ | AWS (S3/EC2/IAM), Linux CLI, Docker on EC2 |
| CIS 9760 | [Module4_docker-api-app](./cis9760/Module4_docker-api-app.md) | 🛡️ | Dockerfile authoring, REST-API app, env-var secrets, 9-bug postmortem |
| CIS 9760 | [Module5_opensearch-docker](./cis9760/Module5_opensearch-docker.md) | 🛡️ | OpenSearch cluster, Dockerized Python REST client on EC2, debug-by-layer |
| STA 9750 | [Week2_review](./sta9750/Week2_review.md) | 📚 | Reproducible-research toolchain overview |
| STA 9750 | [Lab3_journal](./sta9750/Lab3_journal.md) | 📚 | R deep-dive: recycling, floats, session model, caught instructor bug |
| STA 9750 | [Lecture2-3_R-Quarto](./sta9750/Lecture2-3_R-Quarto.md) | 📚 | Markdown/Quarto/git + R fundamentals (+ 🔁 transferable) |
| STA 9750 | [MP00_peer-feedback](./sta9750/MP00_peer-feedback.md) | 📚 (+🔁) | GitHub Pages pipeline, peer-review meta-skill, render-trap = Docker snapshot |
| STA 9750 | [Lab4_journal](./sta9750/Lab4_journal.md) | 📚 (+🔁) | dplyr verbs, R's NA model, group-aware filtering; mean-of-a-logical=rate |
| STA 9750 | [Week5_base-R-grammar](./sta9750/Week5_base-R-grammar.md) | 📚 (+🔁) | base R reference + 12 probes; the "quiet failures" catalogue |
| both | [Week of 09-14 hub](./2026-09-14_coursework-journal.md) | 🛡️+📚 | cross-course reproducibility synthesis |

## How each entry gets logged

Send the lab / assignment / journal; it's drafted or preserved as a course doc with: what it
covered, the tag + one line of why, direct portfolio hits (🛡️ only), and an interview sentence
where one is earned. 🛡️ entries that produce a defensible line graduate to INTERVIEW-SENTENCES;
📚 entries stay here — logged, visible, honest, not pitched. Everything surfaces on WEEKLY-LOG with
its tag inline.
