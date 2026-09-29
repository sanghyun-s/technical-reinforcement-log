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
or is named in a target JD. Everything else is 📚 — accurate labeling, not a demotion.

## Structure — organized by course

Coursework arrives by course, per week, so the folders are course-based. The weekly hub carries the
cross-course synthesis; the course folders carry the depth.

```
coursework/
├── coursework-README.md            ← this file: tag system + index
├── 2026-09-14_coursework-journal.md ← weekly hub (synthesis + tags + interview lines)
├── cis9760/                         ← 🛡️ Big Data Technologies
│   ├── Module2-3_AWS-terminal.md
│   └── Module4_docker-api-app.md
└── sta9750/                         ← 📚 Software Tools for Reproducible Research
    ├── Lab3_journal.md
    ├── Week2_review.md
    ├── Lecture2-3_R-Quarto.md
    └── MP00_peer-feedback.md
```

## Logged journals — index

| Course | Doc | Tag | Topic |
|---|---|---|---|
| CIS 9760 | [Module2-3_AWS-terminal](./cis9760/Module2-3_AWS-terminal.md) | 🛡️ | AWS (S3/EC2/IAM), Linux CLI, Docker on EC2 |
| CIS 9760 | [Module4_docker-api-app](./cis9760/Module4_docker-api-app.md) | 🛡️ | Dockerfile authoring, REST-API app, env-var secrets, 9-bug postmortem |
| STA 9750 | [Lecture2-3_R-Quarto](./sta9750/Lecture2-3_R-Quarto.md) | 📚 | Markdown/Quarto/git + R fundamentals (+ 🔁 transferable) |
| STA 9750 | [Lab3_journal](./sta9750/Lab3_journal.md) | 📚 | R deep-dive: recycling, floats, session model, caught instructor bug |
| STA 9750 | [Week2_review](./sta9750/Week2_review.md) | 📚 | Reproducible-research toolchain overview |
| STA 9750 | [MP00_peer-feedback](./sta9750/MP00_peer-feedback.md) | 📚 (+🔁) | GitHub Pages pipeline, peer-review meta-skill, render-trap = Docker snapshot |
| both | [Week of 09-14 hub](./2026-09-14_coursework-journal.md) | 🛡️+📚 | cross-course reproducibility synthesis |

## How each entry gets logged

Send the lab / assignment / journal; it's drafted or preserved as a course doc with: what it
covered, the tag + one line of why, direct portfolio hits (🛡️ only), and an interview sentence
where one is earned. 🛡️ entries that produce a defensible line graduate to INTERVIEW-SENTENCES;
📚 entries stay here — logged, visible, honest, not pitched. Everything surfaces on WEEKLY-LOG with
its tag inline.
