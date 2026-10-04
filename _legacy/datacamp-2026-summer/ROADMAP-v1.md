# DataCamp Roadmap

**13 courses, ranked.** Nine carried from the 2027 Application Plan §5.1, four added
2026-07-17. Ordered by how directly each one closes the stated weakness — *independent code
fluency* — not by interest, and not by how good it looks on a profile.

**The ordering principle:** courses that make me able to *read and write* code come before
courses that make me able to *talk about* systems. A course that only produces vocabulary is
ranked below one that produces capability, because vocabulary without capability is exactly
the failure mode §3 warns about — sounding fluent until someone opens an editor.

**Status:** 1 complete (Intermediate Python for Developers), 13 remaining.
**Target:** Tier A + B done before fall recruiting opens (September 2026). Not two weeks.

---

## Tier A — Code fluency. The actual weakness.

These three are the plan. Everything below them is defense of work already done; these are
the ones that change what I can do.

| # | Course | Hrs | Why it's here |
|---|---|---|---|
| **1** | [Data Structures and Algorithms in Python](https://app.datacamp.com/learn/courses/data-structures-and-algorithms-in-python) | 4 | Covers stacks, queues, hash tables, search, sort — **five of the eight categories in §5.2**. Right now every LeetCode problem costs double: learning the pattern *and* the structure under it. This pays that debt once and makes every subsequent problem cheaper. Take before adding LeetCode volume. |
| **2** | [Object-Oriented Programming in Python](https://app.datacamp.com/learn/courses/object-oriented-programming-in-python) | 4 | A Pydantic `BaseModel` **is a class**; inheritance is the mechanism. FastAPI dependency injection sits on the same foundation. I currently cannot explain the thing my own backend is built on. Directly closes the Tier 2 question *"where and why are Pydantic schemas used?"* |
| **3** | [Introduction to Testing in Python](https://app.datacamp.com/learn/courses/introduction-to-testing-in-python) | 4 | §12.1's answer claims I'm *"learning how to structure, test, debug, and verify AI-assisted code."* The follow-up is **"how do you verify it?"** Strong answer: pytest cases against behavior I specified before accepting generated code. Weak answer: "I read it carefully." Today I have the weak answer. My self-narrative also claims I owned the *testing scenarios* on all three apps — this closes the distance between that claim and my ability to defend it. |

---

## Tier B — App defense. Tier 2 questions → Tier 1.

Each one buys back specific questions from the matrix. Ranked by how often the question
actually comes up.

| # | Course | Hrs | Defends |
|---|---|---|---|
| **4** | [Working with the OpenAI API](https://app.datacamp.com/learn/courses/working-with-the-openai-api) | 3 | CASSIA + LUCENT both run on it — the most-used external dependency in the portfolio and the highest-probability technical question. Embeddings, GPT narrative generation, cost/token structure. |
| **5** | [Introduction to LLMs in Python](https://app.datacamp.com/learn/courses/introduction-to-llms-in-python) | 3 | RAG, LLM pipeline, prompt, model response. Pairs with #4 — #4 is *how I called it*, #5 is *what it's doing*. Feeds the Tier 2 question on evaluating RAG retrieval quality. |
| **6** | [Supervised Learning with scikit-learn](https://app.datacamp.com/learn/courses/supervised-learning-with-scikit-learn) | 4 | LUCENT's ML vocabulary: train/test split, `StandardScaler`, evaluation metrics. **⚠ Read the caveat below before taking this.** |
| **7** | [Building Scalable Agentic Systems](https://app.datacamp.com/learn/courses/building-scalable-agentic-systems) | — | PREPARE's Claude Agent SDK, PDF Skill, agentic parsing. Also feeds §12.2 Layer B positioning. |
| **8** | [Natural Language Processing (NLP) in Python](https://app.datacamp.com/learn/courses/natural-language-processing-nlp-in-python) | 4 | CASSIA's document Q&A, retrieval, chunking, text processing. Partly redundant with #5 — take it only if the RAG questions still feel thin after #5. |
| **9** | [Claude Code in Action](https://app.datacamp.com/learn/courses/55059) | — | *"How much did you use AI coding tools?"* is a **top-three question** for me. Ranked here, not higher, only because I can already answer it from lived experience — this sharpens the answer rather than creating it. |

### ⚠ The scikit-learn caveat — important

**Isolation Forest is an unsupervised algorithm.** A course called *Supervised Learning with
scikit-learn* will not cover it directly. That does not make the course wrong — it teaches
the sklearn API surface, the estimator pattern (`.fit()` / `.predict()`), scaling, and
evaluation, all of which LUCENT uses. But it will **not** hand me the answer to *"can you
explain how Isolation Forest scores an anomaly?"*

This matters because §12.2 already lists *"supervised vs. unsupervised for LUCENT"* as a
Tier 2 question. If I take the course and assume it covered LUCENT's model, I'll walk into
that question overconfident — the worst possible state. **After #6, budget separate time for
Isolation Forest specifically:** why it isolates outliers with random splits, why anomalous
points need fewer splits to isolate, why the score is a depth-based measure, and why
`StandardScaler` matters when features are on different scales. That's a documentation-and-
paper afternoon, not a DataCamp course.

---

## Tier C — Positioning and breadth. Real, but not urgent.

| # | Course | Hrs | Note |
|---|---|---|---|
| **10** | [AI for Finance](https://app.datacamp.com/learn/courses/ai-for-finance) | — | Connects directly to the Accounting/Finance AI positioning. Positioning language, not capability — which is why it's below the app-defense tier and not above it. |
| **11** | [Practicing Coding Interview Questions in Python](https://app.datacamp.com/learn/courses/practicing-coding-interview-questions-in-python) | 4 | Literally §5.2's goal as a course title. **Deliberately last-ish: this is rehearsal, and rehearsal before the material exists is theater.** Take in September, alongside fall recruiting — not now. |
| **12** | [Introduction to Python for Finance](https://app.datacamp.com/learn/courses/introduction-to-python-for-finance) | 4 | Demoted from §5.1's #7. Likely overlaps CIS 9650 and the pandas work already done in PREPARE/LUCENT. Skim the syllabus first — if >50% is familiar, skip it and say why. |
| **13** | [Financial Analysis in Power BI](https://app.datacamp.com/learn/courses/financial-analysis-in-power-bi) | — | Demoted from §5.1's #9. Not Python, doesn't touch code fluency, hedges toward a Tier 2 job family. Take only if a specific JD demands Power BI. |

---

## Deliberately not taking — and why that's an answer, not a gap

Being able to explain *why I skipped something* is itself defense. If asked "have you looked
at LangGraph?", the answer isn't "no" — it's the reasoning below.

| Course | Why not |
|---|---|
| Graph RAG with LangChain and Neo4j | CASSIA runs **ChromaDB**. Studying a vector stack I didn't build invites questions about a stack I can't defend. |
| Multi-Agent Systems with LangGraph | PREPARE runs the **Claude Agent SDK**. Same reasoning — #7 covers the agentic concepts against the framework I actually used. |
| Keras / TensorFlow / XGBoost / RLHF / Image Modeling | Contradicts §6.1, which classifies pure Gen AI / ML engineering as a **stretch role, not a core target**. Deep learning coursework invites exactly the interview I'm trying not to have. |
| Quantitative Risk Management · Portfolio Analysis | Finance, but the **wrong finance** — market/portfolio risk, not the bookkeeping / audit / tax stack in §8. |
| Winning a Kaggle Competition | Competition ML. No connection to any app, any JD keyword, or the weakness. |

> **The rule:** a course earns a slot only if it closes a gap in code fluency, defends a
> technology already in my portfolio, or is named in a target JD. Curiosity is not a reason.

---

## Sequencing note

The temptation is to take Tier B first — it's more interesting and more directly about the
apps. Resist it. Tier B teaches me to *describe* systems I already built. Tier A changes
what I can *build and verify*. If only three of these thirteen get finished, they should be
1, 2, and 3, because those are the only ones that make the §4 weakness answer true rather
than aspirational.
