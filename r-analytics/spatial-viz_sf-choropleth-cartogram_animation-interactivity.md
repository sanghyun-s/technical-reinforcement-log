> **Skill:** r-analytics (spatial visualization + animation + interactivity) · **Source:** Baruch STA 9750 Week 7 in-class lab · **Mode:** Coursework (run cold in my own RStudio console) · **Date:** 2026-10-21
> Index: [r-analytics README](./README.md) · companions: [ggplot2 iterative refinement](./ggplot2_iterative-plot-refinement.md)
> *The most dependency-heavy R lab so far — spatial data, a live Census API pull, animation, and interactive graphics, all run end to end.*

# Advanced ggplot2 — sf maps, choropleths, cartograms, gganimate, ggiraph

## Activity
- **Skill:** r-analytics — spatial (`sf`/`geom_sf`), choropleth vs cartogram, Census API (`tidycensus`), animation (`gganimate`), interactivity (`ggiraph`), NYC hex maps (`nychex`)
- **Topic:** time-series smoothing → spatial smoothing → choropleth → counts vs rates → cartograms → Census API → MTA governance analysis → NYC hex maps → gganimate → ggiraph tooltips/linked panels
- **Mode:** Coursework, run cold (every block typed into my own console, not read from solutions)
- **Date:** 2026-10-21

## What I worked on
A full tour of visualization-as-analysis, run live: a 91-day running median on 7,305 daily birth
records; North Carolina SIDS maps (count vs rate); population cartograms; a 12-county NYC-metro
pull straight from the **Census ACS API** across 11 demographic tables; an **MTA voting-power**
analysis; NYC neighborhood-income hex maps; a Gapminder `gganimate` GIF; and interactive
`ggiraph` scatterplots with tooltips and **linked cross-panel highlighting**.

## Independent attempt
Ran the entire chain myself and hit (and fixed) the real dependency failures — this is the part
worth keeping:
- `library(gapminder)` / `census_api_key()` / `map()` all errored first as **"could not find
  function / no package"** → each was a *missing library or package*, fixed by install + load.
  (The Lab-3 lesson confirmed again: *when R says "can't find X," check the package before the code.*)
- The **`ACS_TABLES` / `ACS_DATA` object-dependency chain** broke twice across R restarts — a
  restart wipes the environment, so I had to re-run the whole `tribble → map(get_acs) → bind_rows →
  join → pivot_wider` pipeline before anything downstream worked.
- The **tooltip glue failed** (`object 'effective_mta_vote' not found`) because I'd restarted after
  building `ACS_DATA` but before re-deriving `effective_mta_vote → voter_power → relative_power` —
  so I rebuilt the derived-variable chain, then the tooltip evaluated.

## Assistance used
- **Course material** (the lab page) for the sequence; **AI** only to format this log, not to
  write the R. Verified my own numbers by printing each derived column (the `relative_power` table).

## Development layer — fluency
**Can now do that I couldn't before:**
- Treat an **`sf` object** as a tibble with a `geometry` column (MULTIPOLYGON) and map it with
  `aes(geometry = geometry) + geom_sf()`.
- Reproject coordinates with **`st_transform(26916)`** before a cartogram (projected CRS required),
  and drop geometry with `st_drop_geometry()` when I only need the attributes for a join.
- Pull live **Census ACS** data: `census_api_key()`, then `map()` over a `tribble` of table IDs with
  `get_acs(geometry = TRUE)`, `bind_rows`, join back the concept names, `pivot_wider` to one row per county.
- Build a **cartogram** (`cartogram_cont` / `cartogram_dorling`) so *area* encodes population while
  *fill* encodes a rate — separating two variables onto two visual channels.
- Make a plot **animated** (`transition_time(year)`, `{frame_time}` title, `animate(..., gifski)`)
  and **interactive** (`geom_point_interactive`/`geom_sf_interactive` with `tooltip`+`data_id`,
  `girafe()`), including **linked highlighting** across two `patchwork` panels via a shared `data_id`.

**Still shaky:** `shiny` reactive apps (covered conceptually, not built); CRS/EPSG codes (I used
26916 because the lab did — I can't yet choose a projection from first principles).

**Reproduce cold?** **Partial** — `geom_sf` + choropleth + counts-vs-rates yes; the full `map(get_acs)
→ bind_rows → pivot_wider` ACS pipeline and the cartogram reprojection I'd want one more cold pass on.

## Technical mistake or misconception (the real lesson)
**The environment *is* state, and spatial pipelines make that unforgiving.** Three failures this lab
were all the same root cause — an R restart wiped objects, and a long derived-variable chain
(`ACS_DATA → effective_mta_vote → voter_power → relative_power`) has to be rebuilt *in order* before
anything downstream (a map, a tooltip) can evaluate. This is the Lab-3 session-model lesson at
scale: **the console is the workshop, and the workshop resets.** The fix is discipline, not cleverness —
re-run the pipeline top-to-bottom after any restart, or move it into a script/chunk.

## Artifact / Evidence
This activity log + the full console transcript (`lab7_RSTD_pasted`) + the lab notes. Outputs
produced live: NC SIDS count/rate maps, NYC-metro cartograms, the MTA relative-power map, the NYC
income hex map, the Gapminder GIF, and the linked interactive penguin panels.

## Defense layer — interview
**Can defend now:**
- *"A high raw count isn't high risk — SIDS cases track births, so I map `SID74/BIR74`, a rate with
  a denominator, not the count. Same reason I headline rates over counts in any geographic analysis."*
- *"Choropleths mislead because geographic area ≠ population — big rural counties grab the eye and
  dense small ones vanish. A cartogram fixes it by making *area* encode population and *color* encode
  the rate, so the two variables stop fighting."*
- *"I can pull live Census ACS data programmatically — map over a set of table IDs, join the
  concepts back, pivot to one row per geography — rather than hand-downloading CSVs."*

**Follow-up that could expose weakness:** "Why EPSG 26916?" — honestly, I used the projection the
lab specified; choosing a CRS for a given region from first principles is open work.

## Honest limit
Course-guided, not a self-directed analysis. `shiny` is conceptual only. CRS selection is not yet
mine. I should **not** claim independent GIS fluency — I can execute a guided spatial pipeline and
explain the analytical choices, which is the honest version.

## Open thread
- A self-directed choropleth-vs-cartogram on a dataset of my choosing → toward `applied-practice/end-to-end-analysis/`.
- The denominator/rate lesson is the bridge to **SQL**: a `GROUP BY` with `sum(numerator)/sum(denominator)`, not `avg(rate)`.

## Self-narrative check
**Yes.** "I can build choropleths and cartograms from live Census data and explain *why* a rate and
a population-weighted area change the story" is a concrete geospatial-analytics claim I couldn't make
before — and it deepens the "visualization is part of analysis, not decoration" theme from PA#06 and MP#01.
