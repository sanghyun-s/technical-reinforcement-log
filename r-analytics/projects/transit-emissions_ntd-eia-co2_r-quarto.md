> **Skill:** r-analytics (end-to-end analysis in R/Quarto) · **Source:** Baruch STA 9750 Mini-Project #2 · **Mode:** Independent analysis (coursework brief, self-executed) · **Date:** ~2026-11 (Week 11 cycle)
> Companion drill: [HTML in R — rvest/stringr](../drills/html-in-r_rvest-stringr-regex.md) · cross-listed under [applied-practice/end-to-end-analysis](../../applied-practice/end-to-end-analysis/transit-emissions_pointer.md) · index: [r-analytics README](../README.md)
> *Artifact: `mp02.qmd` (2,572 lines, Quarto HTML, code-fold + TOC). This file is the defense write-up, not the code dump — keep the `.qmd` alongside if archiving the source.*

# STA 9750 MP#02 — Greenest US Transit Agencies (NTD × EIA emissions)

**Why 📚 → leaning 🔁:** this is the first time in R I ran a *complete* analysis end-to-end on
**two real federal datasets I had to acquire myself** — scrape, clean, join, model, and defend a
ranked result — rather than a guided single-table lab. The join-discipline and multi-source
reasoning transfer directly to any analyst role; the award-scoring judgment transfers to the
accounting-adjacent "which number is actually fair?" question.

## What the project does
Links the **National Transit Database** (NTD — energy use + service/ridership records) to the
**EIA** state electricity profiles, converts every agency's fuel + electricity into **pounds of
CO₂**, normalizes by passenger activity, and names award winners across three ridership tiers
(Small / Medium / Large). Two awards, deliberately measuring different things:
- **Greenest Transit Agency** — lowest CO₂ per *passenger-mile* (direct operational intensity).
- **Cleanest Grid, Best Used** — electrified share of fleet energy × how much cleaner the state
  grid is than the US average (a 0–1 score; rewards electrification *only where the grid is clean*).

## The five things that make this more than a course exercise

**1. Self-acquired data via scraping (Task 1a).** The EIA state profiles were pulled with `httr2` +
`rvest` (CSS-selector scraping), not handed over as a CSV. This is the applied version of the
HTML-in-R drill — and it means the pipeline reruns against the live source.

**2. A real join-key defect, caught and fixed (Data Cleaning).** NTD **Energy** imports IDs
*numerically* (`1`), while NTD **Service** uses *five-character strings* (`"00001"`). Joining them
as-is silently drops matches. I aligned both keys to character before any join. This is the
PA#05 join-cardinality lesson showing up in the wild.

**3. Aggregate-before-join with a conservation check.** NTD Service splits directly-operated (DO)
and purchased (PT) service into separate rows; NTD Energy is one row per agency×mode. Joining the
un-aggregated tables would **duplicate energy rows and inflate every downstream emissions total**.
I aggregate service to the agency×mode grain *first*, and **assert the national UPT (ridership)
total is unchanged by the aggregation** — a cardinality guardrail, not a hope.

**4. Explicit accounting assumptions for the conversion.** Fuel→CO₂ used documented coefficients
(e.g. CNG converted on a gasoline-gallon-equivalent heat basis before applying the EIA natural-gas
coefficient). Baseline inventories exclude biogenic tailpipe CO₂ (biodiesel/ethanol), which would
hand biodiesel-heavy operators an artificial 0.000 score — so **Award 1 rankings re-apply physical
combustion factors** across all biofuel agencies. A sensitivity check on this basis even broke a
zero-emission tie (resolved to the City of Seattle).

**5. Honest handling of the gaps.** EIA covers the 50 states only, so DC and PR agencies get `NA`
grid intensity on the join. Rather than discard them wholesale, **fuel-only operations were kept**
and only the electricity-consuming rows with no assignable grid rate were excluded — and that
exclusion is disclosed (WMATA, DDOT, Alternativa de Transporte Integrado dropped from the ranking).
The write-up ends with four stated methodological limitations, not a victory lap.

## Headline findings (defensible)
- **Normalizing by passenger-mile flips the naive story.** Raw tonnage punishes big systems for
  carrying more people; per-passenger-mile, **MTA New York City Transit ranks in the Large-tier
  top five** — high-volume urban transit is among the *cleanest per rider*, not the dirtiest.
- **The two awards reward different operating models**, especially in the Small tier (only partial
  overlap): per-mile favors long-distance regional coaches; per-trip favors short-hop urban systems.
- **Seattle wins both** in the Medium tier (100% electric on a clean WA grid), while Small/Large
  Cleanest-Grid winners differ from the Greenest winners — confirming grid-aware electrification is
  a *distinct* achievement, not a restatement of low intensity.

## Reusable craft
- One `show_table()` DT-datatable helper and one bar-chart helper, written once and reused for every
  "top N" question — consistent formatting, house green theme (`GTA_GREEN <- "#2C6E49"`).
- Chunks written to be **safe to re-run interactively** (raw NTD mode codes preserved once, guarded
  recodes) — the R-restart-wipes-the-object-chain lesson from Week 7, applied defensively.

## Development layer — fluency
- **Can now do:** acquire two messy federal sources, reconcile incompatible join keys, aggregate to
  the correct grain with a conservation assertion, build a documented unit-conversion model, and
  write a limitations section that survives scrutiny.
- **Still shaky:** writing the scraping selectors fully cold (leaned on docs/iteration for the
  `rvest` element paths); the heat-content/coefficient tables were looked up, not memorized.
- **Reproduce cold?** Partial. The *structure and reasoning* — yes. The exact scraping selectors
  and conversion constants — I'd reconstruct with references.

## Defense layer — interview
- **Primary line:** *"Before joining transit energy to service records I aggregate to the agency-by-mode
  grain first and assert the national ridership total is unchanged — joining the un-aggregated tables
  would duplicate energy rows and inflate every emissions figure. Verifying by a conservation check,
  not by eyeballing the output, is the habit."*
- **Framing line:** *"I normalize emissions by passenger-mile because raw tonnage penalizes
  high-ridership systems like MTA New York City Transit for serving more people — once you normalize,
  high-volume urban transit is among the cleanest per rider."*
- **Follow-up that could expose me:** "Walk me through your CO₂ coefficients" — I'd reach for the
  documented table, not recite it.

## Honest limit
I will NOT claim I can write production `rvest` scrapers unaided yet, and I will NOT present the
emissions coefficients as memorized domain knowledge — they're documented, sourced assumptions.

## Open thread
Port one self-contained piece (the aggregate-before-join + conservation check) into a cold R drill
in `../drills/` to convert "did it in a project" into "can reproduce the pattern cold."
