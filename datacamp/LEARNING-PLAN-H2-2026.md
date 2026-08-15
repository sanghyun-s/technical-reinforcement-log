# Learning Sequencing Plan — H2 2026 → 2027

Companion to [ROADMAP.md](./ROADMAP.md) and [ROADMAP-SUPPLEMENT.md](./ROADMAP-SUPPLEMENT.md).
**Tier A is nearly complete** (Intermediate Python · DSA · OOP done). **Testing in Python is still outstanding — it is Priority 0, before any Tier B course** (see Phase 0). This file sequences the
remaining Tier B and C DataCamp courses, plus the free supplements, into a dated schedule.

**Anchor dates:** Fall recruiting ~Sep 2026 · Spring 2027 graduation · Summer 2027 OPT.

---

## Ordering principle

1. Defend the two flagship apps' external dependencies first — highest-probability questions.
2. Keep the generative-AI courses grouped before the classical-ML one.
3. Positioning and rehearsal courses go last, time-gated to when they pay off.
4. A course earns its slot only if it closes a fluency gap, defends portfolio tech, or is named
   in a target JD. Curiosity is not a reason.

---

## Phase 0 — Finish Tier A (do this first)

The last Tier A course, still outstanding. **Priority 0 — before any Tier B course**, because it
closes the fluency block and removes a live liability in a claim I already make.

| Order | Course | Hrs | Why it's first |
|---|---|---|---|
| 0 | **Introduction to Testing in Python** | 4 | §12.1's answer claims I *"test, debug, and verify AI-assisted code."* The follow-up — *"how do you verify it?"* — I currently answer weakly ("I read it carefully"). Testing converts that to "pytest cases against behaviour I specified before accepting generated code." Every other course *adds* a capability; this *removes a liability in something I already say.* Promotes the "I verify AI-generated code" line out of the interview bank's "Not yet." |

---

## Phase 1 — App-dependency defense (Aug–Sep 2026)

The core of Tier B. After this phase I can defend both flagship apps' engines: CASSIA's RAG and
PREPARE's agents.

| Order | Course | Hrs | Window | Why now |
|---|---|---|---|---|
| 1 | **Working with the OpenAI API** | 3 | Aug | CASSIA + LUCENT both run on it — highest-probability technical question. Start here. |
| 2 | **Introduction to LLMs in Python** | 3 | Aug | Pair immediately with #1: #1 is *how I called it*, #2 is *what it's doing*. Feeds the RAG-evaluation Tier 2 question. |
| 3 | **Building Scalable Agentic Systems** | ~4 | Sep | Defends PREPARE's Claude Agent SDK; doubles as §12.2 Layer-B positioning. Keeps the generative-AI block together. |
| + | **Anthropic Academy — Building with the Claude API** *(free)* | ~self-paced | alongside 1–3 | DataCamp #1 covers the OpenAI dependency; this covers PREPARE's **Claude** side. Together = defend *both* model dependencies, not half. |

---

## Phase 2 — Classical ML for LUCENT (Sep–Oct 2026)

| Order | Course | Hrs | Window | Why |
|---|---|---|---|---|
| 4 | **Supervised Learning with scikit-learn** | 4 | Sep–Oct | LUCENT's ML vocabulary: train/test split, `StandardScaler`, evaluation, the `.fit()`/`.predict()` estimator pattern. |
| 4b | **Isolation Forest self-study** *(not a course)* | ~1 afternoon | right after #4 | **⚠ Required companion.** The course is *Supervised* learning; IF is **unsupervised**, so it won't be covered. Read docs + the paper: why random splits isolate outliers, why anomalies need fewer splits, why the score is depth-based, why `StandardScaler` matters across scales. Skipping this = walking into "how does LUCENT score an anomaly?" overconfident. |

---

## Phase 3 — Conditional & positioning (Oct–Nov 2026)

Take these only if the trigger fires. Skipping-with-a-reason is itself a defensible answer.

| Order | Course | Hrs | Condition | Note |
|---|---|---|---|---|
| 5 | **NLP in Python** | 4 | *only if* RAG/text questions still feel thin after #2 | Partly redundant with #2. Otherwise skip and save the 4 hrs. |
| 6 | **Claude Code in Action** | — | low-urgency | "How much did you use AI tools?" is a top-3 question, but I can already answer it from experience — this *sharpens*, doesn't create. |
| 7 | **AI for Finance** | — | anytime in window | Positioning language for the accounting/finance AI identity, not new capability. |
| 8 | **Introduction to Python for Finance** | 4 | skim syllabus first | Likely overlaps CIS 9650 + PREPARE/LUCENT pandas. If >50% familiar, **skip and say why**. |

---

## Phase 4 — Recruiting-timed & on-demand (Sep 2026 onward / as needed)

| Order | Course | Hrs | Timing | Note |
|---|---|---|---|---|
| 9 | **Practicing Coding Interview Questions in Python** | 4 | **September**, with fall recruiting | Rehearsal — worthless before the material exists, valuable right as interviews start. Time-gated on purpose. |
| 10 | **Financial Analysis in Power BI** | — | **on-demand only** | Not Python, doesn't touch code fluency. Take *only* if a specific JD names Power BI. |

---

## Free supplements (fold in opportunistically — build-first = fluency)

From [ROADMAP-SUPPLEMENT.md](./ROADMAP-SUPPLEMENT.md). Slot these around the DataCamp schedule;
they reinforce the same concepts by *building*, so they count as fluency reps, not "watched a course."

| Resource | Pairs with | When |
|---|---|---|
| Anthropic Academy — Claude API (agents, RAG, MCP) | Phase 1 (#1–3) | alongside the OpenAI/LLM courses |
| Scrimba — AI Engineer Path (build apps: agents, RAG, MCP) | Phase 1 | when a build rep is wanted over a lecture |
| Hugging Face — AI Agents Course | Phase 1 (#3) | after Building Scalable Agentic Systems |
| DeepLearning.AI short courses (RAG, Text-to-SQL) | Phase 1 (#2) | cherry-pick the CASSIA-relevant ones |
| AWS Certified AI Practitioner *(positioning, ~$75–100)* | after Tier B | later — a résumé signal, not a fluency builder |

---

## Deliberately NOT taking (skipping is an answer, not a gap)

If asked "have you looked at X?", the reply is the reasoning, not "no."

| Course | Why not |
|---|---|
| Graph RAG (LangChain + Neo4j) | CASSIA runs **ChromaDB**; don't study a vector stack I didn't build. |
| Multi-Agent Systems (LangGraph) | PREPARE runs the **Claude Agent SDK**; #3 covers agentic concepts against the framework I used. |
| Keras / TensorFlow / XGBoost / RLHF / Image Modeling | Contradicts §6.1 — pure ML engineering is a stretch role, not a core target. Invites the interview I'm avoiding. |
| Quantitative Risk Mgmt · Portfolio Analysis | Wrong finance — market risk, not the bookkeeping/audit/tax stack in §8. |
| Winning a Kaggle Competition | Competition ML; no link to an app, a JD keyword, or the weakness. |

---

## The one-line schedule

**Aug:** OpenAI API → LLMs in Python (+ Anthropic Academy) ·
**Sep:** Agentic Systems → scikit-learn + Isolation Forest self-study · Coding-interview practice starts ·
**Oct–Nov:** conditional NLP / Claude Code / AI for Finance / Python for Finance (skim) ·
**On-demand:** Power BI only if a JD names it.

> If only three of the remaining courses ever get done, they are #1, #2, #3 — the ones that let
> me defend what I actually built.
