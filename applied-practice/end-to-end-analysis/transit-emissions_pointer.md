> **Pointer file.** The full write-up lives in r-analytics (its primary home, because the craft is R).

# Transit Emissions (NTD × EIA) — end-to-end analysis

Cross-listed here because it qualifies as **end-to-end applied analysis**: self-acquired federal
data (scrape + download) → multi-source join with a cardinality guardrail → documented CO₂
conversion model → ranked, defended result with a limitations section.

➡ **Full defense write-up:** [`r-analytics/projects/transit-emissions_ntd-eia-co2_r-quarto.md`](../../r-analytics/projects/transit-emissions_ntd-eia-co2_r-quarto.md)
Artifact: `mp02.qmd` (Quarto, 2,572 lines).

**One-line why it belongs in applied-practice:** it is the second independent end-to-end analysis in
the repo (after MP#01 Netflix), and the first that required acquiring and reconciling *two* real
sources rather than one supplied dataset.
