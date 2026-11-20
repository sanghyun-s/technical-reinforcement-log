> **Skill:** python (pandas data engineering + scikit-learn modeling) · **Source:** Baruch BUS 9430 — solo coding for a project team · **Mode:** Applied project · **Date:** ~2026-10-18 (modeling) → ~2026-11 (consolidated runbook)
> Artifacts: `DataPrep_Combined_Colab.ipynb` (acquisition + cleaning) · `BUS9430_EMS_Colab_runbook.md` (modeling runbook, 1,044 lines) · index: [python README](../README.md)
> *Scope note: this logs the **EMS** work (NYC EMS Incident Dispatch, dataset `76xm-jjuj`). DOT analysis is not started — no placeholder claimed.*

# BUS 9430 — NYC EMS response-delay analysis (solo coding for the team)

**Why 🔁 / strongest Python evidence to date:** this is real data engineering at scale (~4.4M rows,
~30.4M in the full set) plus a modeling pipeline *with a four-bug postmortem, three bugs reproduced
on test data.* The debugging is the differentiator — most of these produce plausible-but-wrong
numbers, which is the expensive kind.

## Data prep (the acquisition + cleaning half)
- **Chunked processing:** 75,000-row chunks over the Socrata API with `Retry`/`HTTPAdapter` paging.
- **Count-before-download guardrail:** ask the server how many rows *should* exist first — "without
  it, a truncated download looks exactly like a successful one."
- **API vs CSV field-name gotcha:** the API field is `incident_id`; the CSV export header is
  `CAD_INCIDENT_ID`. Verified all 22 needed fields against the live endpoint.
- **Safe parsing:** `safe_number` strips thousands separators (`"1,485"`) before `pd.to_numeric`;
  `errors="coerce"` turns junk into `NaN` — *then check how many NaNs it made before trusting it.*
- **Sequential filter funnel with leakage awareness:** severity 1–3 → valid-response flag → response
  > 0 → valid ID → unique ID, each step audited (a filter-funnel table), and invalid records checked
  for *clustering* (dropping them biases those cells faster than they are — a Data Understanding
  finding, not a silent drop).

## Modeling (the runbook half) — and the four bugs
Baseline ranking (Model 0) → logistic regression → ridge on log-seconds → random forest →
dispatch-vs-travel diagnosis → risk-map table → prototype deployment table. The bugs:

1. **`CCACHE` vs `CACHE`** — defined one name, read another → `NameError`. (Cheap.)
2. **`class_weight="balanced"` breaks calibration** — it distorts the intercept so predicted
   probabilities no longer mean "probability," so **summing them for an expected count is
   meaningless.** Fix: a *ranking* model with no class weight (calibrated probabilities) **and** a
   separate *recall* model with `balanced` for the ≥80% recall target — never the same model for both.
   A calibration check (expected total ≈ actual total) is the tripwire if it ever returns.
3. **A merge drops the `calls` column** — both frames carried `calls`, producing `calls_x`/`calls_y`
   then a `KeyError`. Fix: don't aggregate `calls` where the other frame already has it.
4. **One `prep` object shared by three pipelines (the subtle one)** — `Pipeline` **fits its steps in
   place and does not clone them**, so passing the same preprocessor to three pipelines means the last
   `.fit()` silently rewrites the feature space of the earlier two. Fix: `clone(prep)` per model.

## Modeling judgment worth defending
- **Hour/month/severity as categorical** (OneHotEncoder), not numeric — "delay risk at 3 AM isn't
  three times the risk at 1 AM."
- `min_frequency=50` folds rare call types into an "infrequent" bucket (bounded design matrix);
  `handle_unknown="ignore"` so a category only in the test period doesn't crash prediction.
- **Citywide** late-cutoff, not per-cell — a per-area-hour percentile would make every cell exactly
  10% late by construction and the ranking would be pure noise.
- **No test-set leakage:** the 90th-percentile "late" threshold is set from 2024–2025 only, so the
  2026 hold-out can't shape it.

## Development layer — fluency
- **Can now do:** acquire a large dataset safely (chunk, count-check, parse defensively), build a
  leakage-aware filter funnel, and run an sklearn Pipeline/OneHotEncoder modeling suite — *and debug
  the silent failures by reproducing them on test data.*
- **Still shaky:** choosing the model family from first principles (followed the proposal's design);
  calibration diagnostics are new.
- **Reproduce cold?** Partial — the prep spine and the bug fixes yes (they're understood, not copied);
  the full modeling sequence I'd rebuild with the runbook.

## Defense layer — interview
- **Line (the subtle bug):** *"scikit-learn Pipelines fit their steps in place and don't clone them,
  so reusing one preprocessor across three pipelines means the last fit silently overwrites the
  others' feature space — I clone the preprocessor per model. I only found it because I reproduced it
  on test data."*
- **Line (already in INTERVIEW-SENTENCES):** `class_weight="balanced"` inflates predicted
  probabilities, so summing them for an expected count is meaningless.
- **Follow-up risk:** "why logistic over X / defend the feature set" — I executed a given design; I
  can defend the *implementation* better than the *model-selection*.

## Honest limit
Not claiming I designed the modeling approach from scratch — I implemented and *hardened* a given
proposal. The engineering and debugging are mine; the research design was the team's/proposal's.

## Open thread
DOT data + findings when that strand starts. And: port the `clone(prep)` lesson into a tiny cold
reproduction so "found it in a project" becomes "can demonstrate it cold."
