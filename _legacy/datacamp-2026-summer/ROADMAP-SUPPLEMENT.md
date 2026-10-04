# Roadmap Supplement — Flexible AI-Engineering Learning

Companion to [ROADMAP.md](./ROADMAP.md). These are **self-paced, free or lightly-paid** AI
resources — a deliberate menu, not a commitment to all of them.

---

## The governing rule (reconciles with the mentor's caution)

> **Anything that makes me build is fluency work. Anything that only makes me watch or memorize
> is positioning.** Build first; collect credentials later, and never at the cost of the Tier A
> fluency courses (OOP, Testing) or LeetCode reps.

The mentor's worry is legitimate: my §4 weakness is *independent code fluency*, and the failure
mode for someone in my position is collecting AI certificates instead of building hands-on
ability. So every item below is tagged **Build** (feeds fluency) or **Positioning** (résumé /
vocabulary signal), and Build items take priority. No $2000 bootcamps — that's the exact trap.

---

## Tier S — Build, and maps directly to MY stack (do these first)

| Resource | Cost | Why it's top | Maps to |
|---|---|---|---|
| **Anthropic Academy — Building with the Claude API** | Free (+cert) | Tool calling, RAG pipelines, **MCP servers**, agent architectures — taught by Anthropic. PREPARE runs the Claude Agent SDK; this is a course on the exact framework I shipped. | PREPARE (agentic), CASSIA (RAG) |
| **Scrimba — AI Engineer Path** | Free | Project-based (build apps, not watch), covers agents, RAG, MCP, multimodal. Build-first = fluency, not spec-padding. | CASSIA (RAG), PREPARE (agents) |
| **Hugging Face — AI Agents Course** | Free (+cert) | ~8 hrs, 4 modules, runnable code on free CPU tier; tool calling → multi-agent orchestration. Certified free. | PREPARE (agentic parsing) |

*These three cover the same ground my apps already use, by building — so they're §5.1
weakness-reinforcement, not résumé decoration. Start here.*

---

## Tier A — Build, foundational understanding

| Resource | Cost | Note |
|---|---|---|
| **Hugging Face — LLM Course** | Free | Transformers, tokenizers, fine-tuning end-to-end. Expects solid Python (I'm building that). The "understand LLMs below the API" course. |
| **DeepLearning.AI — short courses** (RAG; Building Agentic RAG w/ LlamaIndex; Functions/Tools/Agents; LLMs with SQL) | Free to audit* | 1–2 hrs each, hands-on. The RAG + Text-to-SQL ones map straight to CASSIA. Cherry-pick, don't binge. |
| **Karpathy — Neural Networks: Zero to Hero** | Free (YouTube) | 8 videos, builds a GPT from scratch. The best free deep-dive into how LLMs actually work — doubles as YouTube-track material. |

*\*DeepLearning.AI lessons are free to watch; certificates/graded assignments may be paid.*

---

## Tier B — Positioning (park until Tier A fluency courses are done)

| Resource | Cost | Honest read |
|---|---|---|
| **AWS Certified AI Practitioner (AIF-C01)** | $75–100 exam, ~30–40 hrs prep | Conceptual, not hands-on — built for non-engineers needing AI credibility fast. 50% of the exam is AWS-service-specific (Bedrock/SageMaker) my apps don't use. A **résumé signal**, not a fluency-builder. Fits §6.1 Tier 1/2 targeting. Take *after* OOP + Testing, as positioning. |
| **Kaggle Learn micro-courses** | Free (+cert) | 3–5 hr on-ramps (Intro to ML, etc.). Low-cost credential filler if a JD wants it. |
| **Google — Intro to Generative AI / ML Crash Course** | Free | Vendor-neutral concept coverage. Fine as background, not a priority. |

---

## Skip

| Resource | Why |
|---|---|
| **Simplilearn "AI Engineer" bootcamp** | $2000+, broad, heavily marketed. The exact "pay for the feeling of a path" trap. Everything in it is covered free above. Not while paying tuition. |
| Any paid bootcamp > a few hundred dollars | Same reasoning. Free build-first resources dominate on ROI. |

---

## How this plugs into the existing plan

- **Tier S items** slot into the DataCamp roadmap as **Tier B companions** to *Working with the
  OpenAI API* and *Introduction to LLMs* — same concepts, reinforced by building.
- **Log them like any track:** a short file per resource (what I built, 1 interview sentence),
  surfaced in WEEKLY-LOG. Building an agent in the HF course is a *fluency* rep, not "watched a
  course."
- **Sequencing:** finish Tier A DataCamp (OOP → Testing) first. Then one Tier S build course.
  AWS cert is a later positioning move, not a now move.

---

## Sources & official links (verify before committing time/money)

**Go to the primary source first** — these are the authoritative pages. The recommendations
above draw partly on third-party write-ups (course aggregators, review blogs), so confirm
price, free tier, and "includes certificate" at the official page before relying on them.
These are affiliate-heavy topics, so fine print (exact cost, cert availability) is what to
double-check.

| Resource | Official link | Verification note |
|---|---|---|
| Anthropic Academy | https://anthropic.skilljar.com | Free-tier + Claude API / MCP course claims via Class Central + third-party guides; confirm on the site |
| Scrimba — AI Engineer Path | https://scrimba.com/ai-engineer-path-c02v | JS-rendered; only page metadata was verifiable (agents/RAG/MCP/multimodal) — confirm contents + price directly |
| Hugging Face — courses | https://huggingface.co/learn | Agents course "free + certified" per HF course pages + aggregators |
| DeepLearning.AI — short courses | https://www.deeplearning.ai/courses | Lessons free to audit; certificates/graded work may be paid — confirm per course |
| AWS Certified AI Practitioner (AIF-C01) | https://aws.amazon.com/certification/certified-ai-practitioner/ | Price cited as $75–100 across sources — official page is the authority |

**Secondary sources consulted (third-party, not authoritative):**
- AWS cert: supercareer.co, passitexams.com, certpath.dev, classesplace.com, certcrush.app
- Anthropic Academy: classcentral.com, devtoolhub.com, pasqualepillitteri.it
- Hugging Face: aiweekly.co, spectrumailab.com
- DeepLearning.AI / RAG: deeplearning.ai/courses, turingpost.com

*Honest caveat: the official Anthropic Academy and AWS pages were not fetched directly during
research — those specifics rest on third-party sources. The overall shape (Anthropic/HF/Scrimba
free & build-first; AWS a cheap conceptual cert; Simplilearn overpriced) is well-supported; the
exact numbers are what to verify at the source.*
