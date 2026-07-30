# Technical Reinforcement Log

**Sang-Hyun Seong** · M.S. Business Analytics, Baruch College (Zicklin) · Expected May 2027
Companion to the *Accounting Meets AI* portfolio — PREPARE · CASSIA · LUCENT

> Repetition-based reinforcement of the code fluency behind an AI-assisted portfolio.
> Not an unaided-rate scoreboard — a record of returning to patterns and rebuilding them.

<p align="center">
  <a href="./WEEKLY-LOG.md"><img src="https://img.shields.io/badge/Weekly%20Log-1f6feb?style=for-the-badge" alt="Weekly Log"></a>
  <a href="./RE-DRILL-LOG.md"><img src="https://img.shields.io/badge/Re--Drill%20Log-fb8500?style=for-the-badge" alt="Re-Drill Log"></a>
  <a href="./LEARNING-DASHBOARD.md"><img src="https://img.shields.io/badge/Dashboard-2a9d8f?style=for-the-badge" alt="Learning Dashboard"></a>
  <a href="./INTERVIEW-SENTENCES.md"><img src="https://img.shields.io/badge/Interview%20Bank-6f42c1?style=for-the-badge" alt="Interview Bank"></a>
  <a href="./leetcode/archive/ARCHIVE-LOG.md"><img src="https://img.shields.io/badge/Prior%20100%20Archive-6c757d?style=for-the-badge" alt="Prior 100 Archive"></a>
  <a href="https://github.com/sanghyun-s/leetcode-study/tree/main"><img src="https://img.shields.io/badge/Original%20LeetCode%20Log-181717?style=for-the-badge&logo=github&logoColor=white" alt="Original LeetCode Log"></a>
</p>

---

## 📊 Snapshot

| | |
|---|---|
| **Prior archive** | ~100 problems (98 files) · 💯 milestone Jun 2026 · now the re-drill library |
| **New in this repo** | 5 problems + re-drills, since Jul 2026 |
| **DataCamp** | 2 complete · 13 planned |
| **Interview bank** | 6 defensible lines · 3 in progress |
| **Tracks** | LeetCode (new + re-drill) · DataCamp · YouTube · app-code reading |

*Full detail: [WEEKLY-LOG](./WEEKLY-LOG.md) · [RE-DRILL-LOG](./RE-DRILL-LOG.md) · [LEARNING-DASHBOARD](./LEARNING-DASHBOARD.md) · [INTERVIEW-SENTENCES](./INTERVIEW-SENTENCES.md)*

---

## 🗓️ Weekly progress

*Rollup — full rows in [WEEKLY-LOG.md](./WEEKLY-LOG.md).*

| Week | Sessions | New problems | Re-drills / recall | Course reps | Sentences promoted |
|---|---:|---:|---:|---:|---:|
| 2026-07-22 | 11 | 3 | 6 | 2 | 4 |
| 2026-07-15 | 2 | 2 | 0 | 2 | 3 |

---

## ⚡ Latest push (LeetCode + DataCamp)

*Most recent activity — full log in [WEEKLY-LOG.md](./WEEKLY-LOG.md).*

| Date | Track | Activity | Mode |
|---|---|---|---|
| 07-26 | LeetCode | LC 1874 Minimize Product Sum (re-drill) | Reference Recall |
| 07-26 | LeetCode | LC 538 Convert BST to Greater Tree (new) | First-Pass Assisted |
| 07-26 | LeetCode | LC 1038 / 938 BST re-drills | Reference Recall |
| 07-26 | LeetCode | LC 2824 Count Pairs Sum < Target (re-drill) | Pattern Re-Drill |
| 07-26 | DataCamp | DSA — Ch 4: sorts (**course complete**) | Course-Guided |
| 07-19 | DataCamp | DSA — Ch 1: linked lists, stacks, queues, Big O | Course-Guided |

---

## 🎯 Interview takeaways

*Top defensible lines — full bank (6 defensible, 3 "not yet") in [INTERVIEW-SENTENCES.md](./INTERVIEW-SENTENCES.md).*

| Line | Defends |
|---|---|
| I don't claim I'm improving — I measure it: solve mode per problem + a spaced-repetition retention log I can show. | The §4 weakness question (turns it into a strength) |
| Precompute a value→index dict for O(1) complement lookup, checking before storing so I never reuse an element. | Hash map · Python fluency |
| A parabola's extremes sit at a sorted array's ends, so I sort in O(n) with two pointers converging inward. | Two pointers · complexity reasoning |
| Counting sort is faster on paper, but only with bounded values — for this input a plain sort is clearer and robust. | Engineering judgment |
| I built a stack and both binary searches by hand, so I can justify Big O from the data structure up, not memorize it. | DSA fundamentals |

---

## 🧩 Archive coverage — prior ~100

*Pattern families from the [baseline archive](./leetcode/archive/). Full day-by-day milestone
record: [ARCHIVE-LOG.md](./leetcode/archive/ARCHIVE-LOG.md) · cheat sheet:
[PATTERNS-CHEATSHEET.md](./leetcode/archive/PATTERNS-CHEATSHEET.md).*

| Family | ~Count | | Family | ~Count |
|---|---:|---|---|---:|
| Hash Map / Set / Table | 12 | | Math (collapse, Frobenius, Catalan…) | 6 |
| Sorting (+ counting sort) | 11 | | Strings | 5 |
| Trees (DFS ×5 · BFS ×2 · mirror · N-ary) | 11 | | Two Pointers | 5 |
| SQL (7 sub-patterns) | 10 | | Bit Manipulation | 5 |
| Greedy | 9 | | Matrix | 4 |
| Dynamic Programming (7 sub-patterns) | 8 | | Simulation | 4 |
| Prefix Sum · Linked List | 3 · 3 | | Backtracking · Binary Search · Graph | 2 · 1 · 1 |

**Re-drill priority (largest / rustiest first):** Trees · Two Pointers · SQL · DP.

---

## What this repo is

This repo is **not an unaided-rate scoreboard.** It records how I strengthen the code fluency
behind my AI-assisted portfolio — through repeated implementation, next-day recall,
course-guided coding, pattern re-drills, and interview-ready explanations. The key evidence is
whether I can **return to a pattern, rebuild it with less help, explain the trade-off, and
connect it to my portfolio work.**

## Repetition-first rule

My mentor's instruction: **understand the solution first — even with AI, hints, or a worked
answer — then return the next day and rebuild it with less help.** Difficulty doesn't matter;
consistency and momentum do. A first-pass assisted solve becomes evidence when it leads to
next-day recall → pattern re-drill → a cleaner re-implementation → a defensible explanation.

## How work is labeled

Solve Mode is an **honesty marker, not a grade.** No mode is second-class.

| Mode | Meaning |
|---|---|
| **First-Pass Assisted** | Understood a new problem using AI / answer / hints / course |
| **Course-Guided** | Wrote the code myself inside a structured environment (DataCamp) |
| **Reference Recall** | Rebuilt it myself while referring to my own notes/repo |
| **Next-Day Recall** | Re-implemented a recent problem without looking |
| **Cold Solve** | New problem, from a blank page |
| **Pattern Re-Drill** | Repeated a prior-archive problem to keep a pattern sharp |
| **Transcribed** | Copied to learn. Logged honestly, never inflated as evidence |

Cold solves still matter (1–2/week) — one kind of evidence among several, not the throne.

## Baseline archive: prior ~100 LeetCode problems

Completed with AI assistance and detailed notes ([`leetcode-study`](https://github.com/sanghyun-s/leetcode-study),
mirrored under [`leetcode/archive/`](./leetcode/archive/)). Not pure independent evidence —
my **re-drill library.** Spaced repetition and next-day recall run on it. A more honest framing
than hiding it or overclaiming it.

## Structure

Root markdown = what I'm doing (public-facing). Folders = detailed backing files.

```
technical-reinforcement-log/
├── README.md                 ← snapshot + dashboards (this file)
├── WEEKLY-LOG.md             ← main activity stream, all tracks
├── RE-DRILL-LOG.md           ← repetition & next-day recall
├── LEARNING-DASHBOARD.md     ← cumulative pattern coverage + courses
├── INTERVIEW-SENTENCES.md    ← polished, defensible lines
├── leetcode/
│   ├── archive/              ← prior ~100 + ARCHIVE-LOG.md + cheat sheet (re-drill library)
│   └── problems/             ← new problems worked here
├── datacamp/courses/         · youtube/videos/         · sessions/ (optional long-form)
```
