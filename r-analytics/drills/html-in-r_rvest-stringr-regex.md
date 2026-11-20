> **Skill:** r-analytics (web scraping + string manipulation) · **Source:** Baruch STA 9750 Week 11 (Pre-Class #10 + in-class "HTML in R" activity) · **Mode:** Coursework (cold) · **Date:** ~2026-11 (Week 11)
> Applied in: [MP#02 transit emissions](../projects/transit-emissions_ntd-eia-co2_r-quarto.md) (Task 1a scrapes EIA with these tools) · index: [r-analytics README](../README.md)
> *Artifacts: `STA_9750_RStudio_Console_Week__11` (console log) + Week 11 pre-note. The quiz companion (ggplot2 aesthetics + data traps) is `STA9750_PA06_quiz_commands.md`.*

# HTML in R — `rvest` scraping + `stringr`/regex validation

**Why 🔁:** "get data into R from a web page, then validate its shape" is a transferable analyst
task — product codes, customer IDs, ZIPs, transaction references all need format-checking. This is
also the foundation MP#02's data acquisition sits on, so it's the drill *behind* a real project.

## The one distinction that organizes everything
**`rvest` finds *where* the data is in the HTML; `stringr` searches *within* the text once you have
it.** Selecting an element and searching its content are two different steps — the professor's
"Getting Data into R → Web Scraping → Cleaning and Processing Text" flow is exactly this split.

```r
library(rvest); library(stringr)
paragraphs <- starwars |>              # an HTML object already read in
  html_elements("main p") |>           # rvest: SELECT by HTML structure (CSS selector)
  html_text2()                         # rvest: extract clean text
jedi <- str_subset(paragraphs, "Jedi") # stringr: SEARCH the text content
```

## Regex validation — and its trap (Q13–Q17, the real lesson)
- **Format check:** `str_detect(numbers, "^[[:digit:]]{3,5}$")` — anchored (`^…$`) so the *whole*
  string must be 3–5 digits, nothing else.
- **Format ≠ validity:** a string can match the pattern and still not be a real/existing code. Regex
  checks *shape*, not *truth*.
- **The false-positive trap:** `str_detect("-------", "[0-9-]{7,12}")` returns **TRUE** — a row of
  dashes satisfies "7–12 of digits-or-dash." Without anchors and without requiring at least one
  digit, a validator passes garbage. This is the memorable one.

## Development layer — fluency
- **Can now do:** select HTML nodes by CSS selector, extract text with `html_text2()`, and write
  anchored regex that doesn't accept obvious garbage.
- **Still shaky:** complex selector paths on unfamiliar page structures (iterate with docs).
- **Reproduce cold?** Yes for the regex-validation pattern; Partial for arbitrary-page selectors.

## Defense layer — interview
- **Line:** *"Selecting an element and searching its text are two steps — `rvest` locates the node by
  CSS selector, `stringr` searches the extracted text. And I anchor validation regex, because
  `[0-9-]{7,12}` will happily match a row of dashes — format validation only checks shape, not that
  the value is real."*
- **Follow-up risk:** "scrape this live page now" — I'd need to inspect its structure first.

## Honest limit
Not claiming cold fluency writing selectors for an arbitrary site — the *reasoning* is solid, the
site-specific selector work is iterative.
