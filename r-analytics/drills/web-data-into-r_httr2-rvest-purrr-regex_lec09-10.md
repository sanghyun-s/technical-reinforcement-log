> **Skill:** r-analytics (web/API data acquisition → tidy → verify) · **Source:** Baruch STA 9750 Lectures 09–10, two weeks of in-class hands-on practice · **Mode:** Coursework (guided, run independently on Windows RStudio) · **Date:** ~2026-11 (Lec 09–10 cycle)
> Companion: [HTML in R — rvest/stringr quiz note](html-in-r_rvest-stringr-regex.md) (the Week-11 validation note) · applied in: [MP#02 transit emissions](../projects/transit-emissions_ntd-eia-co2_r-quarto.md) · index: [r-analytics README](../README.md)
> *Artifacts: the two-week RStudio console log (one file), my Lecture 09–10 study notes (bilingual, instructor code + my own verified Windows outputs), and the package-install console. This note summarizes and defends that body of work; the console is the raw evidence.*

# STA 9750 Lec 09–10 — getting web & API data into R, end to end

**Why 🔁:** "pull data from an API or a web page, turn it into a tidy table, then check it's actually
right" is a transferable analyst task. This is the two-week hands-on body *behind* the thinner Week-11
quiz note — and the toolchain it drills (httr2 → rvest → purrr → regex) is exactly what MP#02's data
acquisition reused. It's **guided in-class practice**, not independent analysis — logged as a drill,
with that scope stated plainly.

## Lecture 09 — acquisition & structuring
- **httr2 requests, two equivalent ways.** A whole-URL `request(...)` vs. a decomposed
  `request() |> req_url_path() |> req_url_query(...)`; confirmed identical with `all.equal()` (`TRUE`).
  Knowing they're equal is the point — the decomposed form is what you parameterize later.
- **API → JSON → dataframe.** Open Trivia DB: `req_perform()` → `HTTP 200` → `resp_body_json()`,
  checked question count and the structure of the first record before trusting it, then reshaped the
  nested incorrect-answers field with `pivot_wider`.
- **Single-table scraping.** `read_html()` → `html_table()` for a one-table page (the shortcut when a
  page has exactly one table), with a dimension check.
- **A `leaflet` map** of CUNY locations from scraped coordinates.
- **The Cocktails exercise — iteration over many pages.** Collect recipe-page URLs → write a function
  that extracts title + ingredients from one page → `map` it across pages → `bind_rows`. This is the
  "write it for one, apply it to all" pattern, which is what scales scraping past a single page.

## Lecture 10 — pattern extraction & cleaning
- **Apple-rankings scrape** with `html_elements`/`html_element`/`html_attr`: pulled types,
  descriptions, scores, categories into a dataframe, formatted with `gt`.
- **Per-item detail pages:** extracted a URL per apple, `map`ped the detail scrape, combined back.
- **Regression visualization:** `geom_smooth` with correlation and R² — a first link between scraped
  data and a modeled relationship (which fed the MP#02 instinct to *model*, not just tabulate).
- **Regex practice:** `str_extract_all` / `str_count` / `str_remove_all` — extract plural nouns, count
  fruits, pull numbers only. The same string-validation family as the Week-11 note.

## The part that's actually mine — verification discipline
My study notes don't just copy the instructor's code; they record *how to run it honestly*:
- **Don't run competing solutions against the same object in sequence** — a second "alternative"
  mutates the first's result; keep `eval=FALSE` blocks optional. (Same lesson as BUS 9430's `clone(prep)`.)
- **Three different checks for three different things:** `HTTP 200` = the connection worked; `dim()` =
  the structure is right; grep + eyeballing cases = the content is *meaningfully* right. Passing one
  is not passing the others.
- **Preserve the original object** (`ALL_RECIPES`) and compare a derived one (`RECIPES_LONG`) against
  it, so history stays recoverable — the R-restart-wipes-state lesson, applied.
- **Dated, not eternal:** "these are my Nov 2026 run outputs; a live site can change, so they're not
  guaranteed future answers." I left the Final Combined Solution marked *run-but-not-yet-dimension-
  verified* rather than claiming a number I hadn't checked.

## Development layer — fluency
- **Can now do:** acquire from an API (httr2 + JSON), scrape single and multi-page sites (rvest +
  a function + `purrr::map`), reshape nested fields, map coordinates, and run the acquire→tidy→verify
  loop with the right check at each stage.
- **Still shaky:** writing selectors for an unfamiliar page cold; the nested-list/`unnest` handling I
  followed more than led.
- **Reproduce cold?** Partial — the acquire→tidy→verify spine yes; the exact selectors and the
  multi-page function I'd rebuild with references.

## Defense layer — interview
- **Line:** *"When I pull web or API data into R I check three different things, not one — HTTP 200
  says the connection worked, `dim()` says the structure is right, and spot-checking cases says the
  content is meaningfully right. Passing one isn't passing the others."*
- **Line (iteration):** *"To scrape many pages I write the extractor for one page, test it on a single
  record, then `map` it across the rest and `bind_rows` — never hand-repeat it."*
- **Follow-up risk:** "scrape this live site now" — I'd inspect its structure first; selectors aren't
  cold yet.

## Honest limit
Guided coursework, not independent analysis — I ran the instructor's exercises and verified my own
outputs. The *verification habits* are mine to defend; the selector-writing is still iterative.
