# Interview Sentences

Every file in every track ends with one sentence. They all land here. **This is the
deliverable** — the problem files and course notes are the work; this file is the product,
and the resource I open before a screen.

**Rule:** a sentence earns a place here only if I could survive the follow-up. If I can't, it
goes in *Not yet* at the bottom. Moving a line out of *Not yet* is what progress looks like.

---

## The anchor answer (memorized)

> My biggest development area is independent code fluency. I built my portfolio with heavy
> support from AI coding tools, while I owned the accounting logic, product direction,
> testing scenarios, and business framing. After completing the apps, I realized that to
> contribute more responsibly, I need to understand the codebase more independently. That's
> why I created a reinforcement plan around Python, LeetCode fundamentals, FastAPI, SQL
> security, scikit-learn, and frontend/backend architecture. I'm using the exact
> technologies from my own apps as the study map.

**The follow-up I want them to ask:** *"How's that going?"* — because the answer is a repo
with a tracked unaided rate and a spaced-repetition retention log, not an assertion.

---

## The metric weapon — lead with this on the weakness question

> Rather than claim I'm improving, I measure it. Every problem is logged with whether I solved
> it unaided, with a reference, or guided, and I keep a spaced-repetition retention log across
> my prior 100 problems. I can show you the trend line. Almost no candidate can say that,
> because it's unfakeable — which is exactly why I built it.

**Survives follow-up?** Yes. This is the strongest single sentence in the repo.

---

## Fundamentals / code fluency
*Source: `leetcode/`*

| Sentence | Source | Tier | Survives? |
|---|---|---|---|
| For pair-sum problems I precompute a value→index dict for O(1) complement lookup, and check for the complement before storing so I never reuse an element. | 0001 | 1 | Yes |
| When positions are fixed and looked up many times, I pay O(n) once to build a lookup dict rather than rescanning per query — the same trade as Two Sum. | 1165 | 1 | Yes |
| For array construction I use slicing and `+` because both build new lists; the classic bug is an in-place reverse that corrupts the half you meant to keep. | 3925 | 1 | Yes |
| I can explain why an approach is O(n) vs O(n²) rather than assert it — I built a stack by hand, with O(1) push/pop and an empty-stack guard, to make Big O concrete. | DSA course | 1 | Yes |

---

## Python / API / LLM / ML
*Source: `datacamp/`*

| Sentence | Source | Tier | Survives? |
|---|---|---|---|
| I started with Intermediate Python so I could read my own function signatures unaided — what `*args`/`**kwargs` do to arguments, why a docstring is an interface contract, when to raise a typed error instead of swallowing one. | 01 | 1 | Yes — except the testing follow-up (see Not yet) |

---

## Engineering judgment / AI-era positioning
*Source: `youtube/`*

| Sentence | Source | Tier | Survives? |
|---|---|---|---|
| *(none yet — first video not logged)* | | | |

---

## Not yet — sentences I can say but not defend

The staging area. Moving a line **out** is progress.

| Sentence | What I'd need to defend it | Blocked on |
|---|---|---|
| "I test and verify the AI-assisted code I ship." | A concrete answer to "what would you test in PREPARE?" | Testing course (roadmap Tier A #3) |
| "I can explain how LUCENT's Isolation Forest scores an anomaly." | Depth-based isolation, why fewer splits = more anomalous, why StandardScaler first | scikit-learn course + IF self-study |
| "Horizontal-scan prefix matching, `startswith` early-exit." (0014) | A cold re-solve, not a port | 0014 still ported-only, owed |
