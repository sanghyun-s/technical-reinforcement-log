# Technical Reinforcement Log

**Sang-Hyun Seong** · M.S. Business Analytics, Baruch College (Zicklin) · Expected May 2027
Companion to the *Accounting Meets AI* portfolio — PREPARE · CASSIA · LUCENT

---

## Why this repo exists

I built my portfolio applications with heavy support from AI coding tools. I owned the
accounting logic, product direction, testing scenarios, and business framing. After
completing the apps, I identified my largest development gap honestly:

> **Independent code fluency** — the ability to read, write, debug, and extend code
> without AI assistance.

This repo is the execution record of the plan I built to close that gap. It is not a
certificate collection. Every entry in it ends with a sentence I can say in an interview.

**Weakness → diagnosis → plan → execution → job connection.** This repo is the *execution*
column, made auditable.

---

## The honesty mechanism

Every LeetCode entry records a **Solve Mode**:

| Mode | Meaning |
|---|---|
| `unaided` | Wrote it cold. No reference, no AI, no prior solution open. |
| `reference` | Consulted my own earlier notes or docs, but assembled the logic myself. |
| `guided` | Had the pattern and construct list explained before writing. Solution still written by me. |
| `transcribed` | Copied a worked solution. **Counts as study, not as evidence.** |

A `guided` solve is always followed by a **cold variant rep** — a near-identical problem
solved `unaided`, same day or next. The variant is the actual evidence. The first solve is
just the lesson.

The number that matters in this repo is not the problem count. It is the **unaided rate over
time**, tracked in [`leetcode/PROGRESS.md`](leetcode/PROGRESS.md).

---

## Structure

```
technical-reinforcement-log/
├── README.md                  ← you are here
├── INTERVIEW-SENTENCES.md     ← the payoff: every track drains into this file
├── SETUP.md                   ← git commands for this repo
├── leetcode/
│   ├── TEMPLATE.md
│   ├── PROGRESS.md            ← unaided-rate tracking
│   └── problems/
├── datacamp/
│   ├── TEMPLATE.md
│   └── courses/
└── youtube/
    ├── TEMPLATE.md
    └── videos/
```

## The three tracks

| Track | Purpose | Output per unit |
|---|---|---|
| **LeetCode** | Basic-coding defense. Don't freeze on a fundamentals question. | 1 problem file + solve mode + variant rep |
| **DataCamp** | Weakness-reinforcement evidence, tied to my own stack. | 1 course file: which interview sentence it defends |
| **YouTube** | Technical / operational / AI-era developer language. | 1 video file: 3 claims · 2 app links · 1 interview sentence · 3 keywords |

### Scope discipline (LeetCode)

Deliberately narrow. Easy-focused, some Medium. This is not big-tech SWE prep.

`arrays / strings` · `hash map` · `two pointers` · `stack / queue` · `basic recursion` ·
`sorting` · `binary search` · `simple SQL-style logic`

### Daily ceiling, not a daily quota

- **2–3 problems** on days with a genuinely new pattern (dissection + cold variant takes real time)
- **4–5 problems** on days that are mostly variant reps and re-drills of the existing 100

If the LeetCode block starts eating the DataCamp block, the YouTube block, or the
interview-answer block, the number is too high. Study that doesn't convert into a
defensible answer doesn't count.

---

## The governing principle

> Study must not end as study. Videos must not end as videos. App journals must not end as
> retrospectives. Everything must convert into a defensible answer to an interview question.

---

## Status

| Metric | Value | Updated |
|---|---|---|
| Problems logged (this repo) | 0 | 2026-07-15 |
| Unaided rate (last 20) | — | 2026-07-15 |
| DataCamp courses complete | 0 / 10 | 2026-07-15 |
| Videos logged | 0 | 2026-07-15 |
| Interview sentences banked | 0 | 2026-07-15 |

*Prior work: 100 problems completed with AI assistance — archived in
[`leetcode-study`](https://github.com/sanghyun-s/leetcode-study). Those are the re-drill set,
not the evidence set.*
