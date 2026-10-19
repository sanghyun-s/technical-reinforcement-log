> **Skill:** python (pandas data wrangling + scikit-learn logistic regression) · **Source:** Baruch BUS 9430 · **Mode:** Applied project · **Date:** 2026-10-18
> Index: [python README](./README.md)
> *NYC EMS dispatch modeling runbook (Google Colab). 🔁 The `class_weight="balanced"` → inflated-probability bug is a real ML-fluency catch. Original runbook below, verbatim.*

---

# BUS 9430 — EMS Preliminary Modeling Runbook (Google Colab, Python)

**Code Red(uction) · NYC EMS Incident Dispatch Data (`76xm-jjuj`) · compiled 18 October 2026**

Every Korean note from the draft is translated inline. The code is reorganised into copy-paste Colab
cells with the explanation carried in comments, so a cell and its reasoning travel together.

**Four bugs were found and fixed. Three of them were reproduced on test data — the evidence is in
§2.** Read that section before you run anything; two of the bugs produce numbers that look
plausible and are wrong, which is the expensive kind.

---

## 1. What was verified against the live API

| Check | Result |
|---|---|
| All 22 field names in `FIELDS` exist on the endpoint | **Pass — all 22 valid** |
| `incident_id` is the real API field name | **Yes.** The CSV export header is `CAD_INCIDENT_ID`, but the Socrata API field is `incident_id`. The draft is correct |
| Full field list retrieved | 31 fields; `communityschooldistrict` and `congressionaldistrict` also exist and are unused |

I could not run the aggregate queries from here — the sandbox cannot reach `data.cityofnewyork.us`.
**The September-2026 completeness question is therefore still open, and Step 1 is where you settle
it.** That is the right place for it anyway.

---

## 2. Fix these four things before running

### Bug 1 — `CCACHE` vs `CACHE` (Step 5)

```python
CCACHE = "data/ems_2024_2026sep.csv.gz"   # defined as CCACHE
if os.path.exists(CACHE):                  # read as CACHE  -> NameError
```

One character. Fixed below as `CACHE`.

### Bug 2 — `class_weight="balanced"` makes `expected_late_calls` meaningless

Step 13 fits logistic regression with `class_weight="balanced"`; Step 14 then sums the predicted
probabilities and calls the result `expected_late_calls`. Balancing deliberately distorts the
intercept so the model behaves as if the classes were 50/50, which inflates every predicted
probability. Measured on test data with a true 10% late rate:

| | With `class_weight="balanced"` | Without |
|---|---:|---:|
| Mean predicted risk | **0.490** | 0.100 |
| Actual late rate | 0.100 | 0.100 |
| Σ(risk) reported as "expected late calls" | **8,379** | 1,711 |
| Actual late calls | 1,710 | 1,710 |
| **Inflation** | **4.9×** | 1.0× |

**The ranking is unaffected** — balancing is a monotone change, so the hotspot order is the same
either way. Only the *numbers* are wrong. Three options:

1. **Drop `class_weight` for the ranking model.** Simplest, and makes `expected_late_calls` a real
   expected count. Recall will fall; the proposal's ≥80% recall target is then met by choosing a
   threshold, not by reweighting.
2. **Keep `class_weight` and rename the column** to `avg_risk_score` / `risk_rank`, presenting no
   count.
3. Fit both and report both.

**The runbook below takes option 1 and keeps a balanced model alongside it for recall**, because
the proposal demands both a ranked list (needs calibration) and a recall target (needs balance).

### Bug 3 — Step 18's merge loses the `calls` column

`hotspots` and `diagnosis` both contain `calls`, so `merge` renames them `calls_x` and `calls_y`,
and the final column selection raises:

```
KeyError: "['calls'] not in index"
```

Reproduced. Fixed below by dropping `calls` from `diagnosis` before the merge.

### Bug 4 — one `prep` object shared by three pipelines (the Colab-specific one)

`prep` is passed to the logistic, ridge and random-forest pipelines. **`Pipeline` does not clone its
steps — it fits them in place.** So `rf.fit(rf_train, ...)` refits the *same* `ColumnTransformer` on
the 300,000-row sample, and every earlier model silently inherits that refit.

Reproduced: after fitting a second pipeline on a subsample, calling `predict_proba` on the first
raised

```
ValueError: X has 2 features, but LogisticRegression is expecting 54 features as input.
```

because `min_frequency=50` collapsed categories differently on the smaller sample. If the column
counts happen to match instead of differing, you get no error and wrong predictions.

This matters in Colab more than anywhere else, because re-running cells out of order is the normal
way to work there. **Fix: `clone(prep)` in each pipeline.**

---

## 3. One sizing decision to make before Step 12

Step 12 groups by `incident_dispatch_area × hour_of_week` and then keeps cells with ≥ 50 calls.

Rough arithmetic: FDNY handles on the order of 1.1 million ambulance responses a year. If severity
1–3 is roughly a third of those, 2024 contributes about 350,000–400,000 analysis rows. Spread over
about 70 dispatch areas × 168 hours of the week = **11,760 cells, around 30 calls per cell on
average.** A ≥ 50 threshold would discard most of the grid.

By contrast `area × time_block` is 70 × 6 = **420 cells, around 900 calls each** — comfortable.

This is the same area-hour-versus-time-block divergence between the deck and the finished proposal.
The practical resolution, which the proposal's own risk table already gestures at ("pool thin cells
into weekday and weekend blocks"):

> **Model at the call level, report at `area × time_block`.** Use `hour_of_week` as a feature if you
> want it, not as the reporting grid.

Step 12 below builds the baseline both ways and prints how many cells survive, so the team decides
from the actual counts rather than from a guess.

---

# The runbook

Each numbered heading is one Colab cell. Run them in order the first time.

---

## Step 0 — Environment and API connection

```python
# BUS 9430 — EMS preliminary modeling
# Data: NYC EMS Incident Dispatch Data, Socrata dataset 76xm-jjuj
#
# Colab has pandas, numpy, requests and scikit-learn preinstalled; nothing to pip install.

import io
import os
import time
import requests
import numpy as np
import pandas as pd

# Colab's filesystem is wiped when the runtime disconnects. "data/" is fine for a
# single session; see Step 5 for mounting Drive if you want the cache to survive.
os.makedirs("data", exist_ok=True)

BASE_JSON = "https://data.cityofnewyork.us/resource/76xm-jjuj.json"
BASE_CSV  = "https://data.cityofnewyork.us/resource/76xm-jjuj.csv"

# Smallest possible request: one row, just to confirm the endpoint answers and to
# see the field names the API actually uses (they differ from the CSV export headers --
# the API calls it `incident_id`, the CSV export calls it CAD_INCIDENT_ID).
r = requests.get(BASE_JSON, params={"$limit": 1}, timeout=60)
r.raise_for_status()

print("Status:", r.status_code)
print("Columns returned:")
print(list(r.json()[0].keys()))
```

**Expected output:** `Status: 200`, followed by 31 field names.

---

## Step 1 — Confirm the date coverage first

> *To confirm whether the data can support the finished proposal, start by looking at monthly row
> counts from 2024 onward.*

```python
# This is the gate on the whole project design. The finished proposal holds out
# Jan-Sep 2026 as the test set, so September 2026 has to be a complete month.
# NYC Open Data publishes with a lag and the most recent month is often partial.

monthly_resp = requests.get(
    BASE_JSON,
    params={
        "$select": "date_trunc_ym(incident_datetime) as ym, count(*) as n",
        "$where":  "incident_datetime >= '2024-01-01T00:00:00.000'",
        "$group":  "ym",
        "$order":  "ym"
    },
    timeout=60
)
monthly_resp.raise_for_status()

monthly_counts = pd.DataFrame(monthly_resp.json())
monthly_counts["n"] = monthly_counts["n"].astype(int)

print(monthly_counts.to_string(index=False))

# A reference point: the dataset holds ~30.4M records over Jan 2005 - Sep 2026,
# which is about 116,000 per month on average. Compare the last few rows against
# the median of the earlier ones rather than against that figure.
print("\nMedian monthly volume in this window:",
      f"{int(monthly_counts['n'].median()):,}")
print("Last three months:")
print(monthly_counts.tail(3).to_string(index=False))
```

> *Pay particular attention to whether September 2026 is a normal full month. If September is
> abnormally small — roughly half the size of other months — then the final holdout should be
> shortened to Jan–Aug 2026.*

**Record the answer and tell the team.** It decides whether the holdout is Jan–Sep or Jan–Aug, and
that goes on the slide.

---

## Step 2 — Select only the fields we need

> *This part carries over unchanged from the earlier acquisition code.*

```python
# All 22 of these were checked against the live endpoint and exist.
FIELDS = [
    # identity and time
    "incident_id",
    "incident_datetime",

    # what the call was -- KNOWN AT ARRIVAL, so these can be predictors
    "initial_call_type",
    "initial_severity_level_code",

    # what it turned out to be -- known only afterwards, so outcomes/diagnostics only
    "final_call_type",
    "final_severity_level_code",

    # the response-time decomposition (outcomes)
    "valid_dispatch_rspns_time_indc",
    "dispatch_response_seconds_qy",
    "valid_incident_rspns_time_indc",
    "incident_response_seconds_qy",
    "incident_travel_tm_seconds_qy",

    # geography -- known at arrival
    "borough",
    "incident_dispatch_area",
    "zipcode",
    "policeprecinct",
    "communitydistrict",
    "citycouncildistrict",

    # status flags
    "special_event_indicator",
    "standby_indicator",
    "transfer_indicator",
    "held_indicator",
    "reopen_indicator",
]
```

> *An important distinction here:*

**Predictor candidates** — `incident_dispatch_area`, time features, `initial_severity_level_code`,
`initial_call_type`.

**For diagnostics and outcomes only** — `dispatch_response_seconds_qy`,
`incident_travel_tm_seconds_qy`, `held_indicator`, `reopen_indicator`, `transfer_indicator`,
and both `final_*` fields.

> *In particular, `held_indicator` may be information that only becomes available later, so it is not
> used directly as a predictive input.*

This is the leakage control, and it deserves to be stated more strongly than "may be": **`held`
means no unit was free**, which is produced by the same dispatch failure that makes the call late.
Using it as a predictor would give an excellent AUC from a near-synonym for the outcome. The same
applies to `reopen` and `transfer`, and to both `final_*` fields — a final severity code is assigned
after the crew arrives.

> *This is the one risk on the deck that the finished proposal names and the slides do not.*

---

## Step 3 — Define the acquisition window

> *For now, download once so that both designs are supported.*

```python
# One download covers both candidate designs:
#   deck version      -> train 2024, test 2025
#   proposal version  -> develop 2024-2025, hold out Jan-Sep 2026
DATA_START = "2024-01-01T00:00:00.000"
DATA_END   = "2026-10-01T00:00:00.000"   # exclusive, so this stops at 30 Sep 2026

WHERE = (
    f"incident_datetime >= '{DATA_START}' "
    f"AND incident_datetime <  '{DATA_END}'"
)
print(WHERE)
```

> *That is: all of 2024, all of 2025, and January–September 2026.*

If Step 1 shows September 2026 to be partial, change `DATA_END` to `"2026-09-01T00:00:00.000"` and
re-run from here.

---

## Step 4 — Ask the server how many rows should exist

> *This part is still needed exactly as in the earlier code.*

```python
# Asking the server for the count BEFORE downloading gives an independent number
# to validate the download against. Without it, a truncated download looks
# exactly like a successful one.
count_resp = requests.get(
    BASE_JSON,
    params={"$select": "count(*) as n", "$where": WHERE},
    timeout=60
)
count_resp.raise_for_status()

expected_n = int(count_resp.json()[0]["n"])
print(f"Expected rows: {expected_n:,}")
```

> *This number is the benchmark for acquisition validation.*

Order-of-magnitude sanity check: 33 months at roughly 116,000 records a month is about **3.8
million**. If the figure comes back near 30 million, the `$where` clause did not apply.

---

## Step 5 — Stable paginated download

> *This is also needed. The only changes are the filename and the date range.*

```python
# FIX (bug 1): the draft defined CCACHE and read CACHE -> NameError.
CACHE = "data/ems_2024_2026sep.csv.gz"
PAGE_SIZE = 50_000

if os.path.exists(CACHE):
    # FIX: read back as str, matching how the pages were downloaded, so the
    # cached path and the fresh path produce identically typed columns.
    ems = pd.read_csv(CACHE, dtype=str, low_memory=False)
    print(f"Loaded cached file: {len(ems):,} rows")

else:
    pages  = []
    offset = 0

    while offset < expected_n:
        params = {
            "$select": ",".join(FIELDS),
            "$where":  WHERE,
            # $order is what makes pagination safe. Without a deterministic sort the
            # server does not guarantee a stable page boundary, so rows can be
            # duplicated or skipped across pages -- silently.
            "$order":  "incident_datetime, incident_id",
            "$limit":  PAGE_SIZE,
            "$offset": offset,
        }

        resp = requests.get(BASE_CSV, params=params, timeout=120)
        resp.raise_for_status()

        chunk = pd.read_csv(io.StringIO(resp.text), dtype=str)
        pages.append(chunk)
        offset += len(chunk)

        print(f"Downloaded: {offset:,} / {expected_n:,} ({offset / expected_n:.1%})")

        if len(chunk) < PAGE_SIZE:
            break
        time.sleep(0.1)      # courtesy pause; keeps the API from rate-limiting

    ems = pd.concat(pages, ignore_index=True)
    ems.to_csv(CACHE, index=False, compression="gzip")
    print(f"Cached to {CACHE}")
```

> *Including `$order` matters: it keeps the page boundary stable during pagination.*

**Colab note.** This takes roughly 75 pages and several minutes. The runtime disconnects after
idling, and `data/` goes with it. To keep the cache across sessions, mount Drive first and point
`CACHE` there:

```python
from google.colab import drive
drive.mount("/content/drive")
CACHE = "/content/drive/MyDrive/bus9430/ems_2024_2026sep.csv.gz"
os.makedirs(os.path.dirname(CACHE), exist_ok=True)
```

That one change saves the whole team from re-downloading 3.8 million rows each.

---

## Step 6 — Acquisition validation

> *Required, unchanged from the earlier code. Both asserts must pass before moving on to data
> preparation.*

```python
print("Expected:  ", f"{expected_n:,}")
print("Downloaded:", f"{len(ems):,}")

missing_cols = sorted(set(FIELDS) - set(ems.columns))
print("Missing expected columns:", missing_cols)

dupes = ems["incident_id"].duplicated().sum()
print("Duplicate incident IDs:", dupes)

assert len(missing_cols) == 0, f"missing columns: {missing_cols}"
assert dupes == 0, "duplicate incident_id -- pagination overlapped"

# NOTE: an exact row-count match can fail for a legitimate reason. NYC Open Data
# refreshes the dataset, so a count taken in Step 4 and a download finished ten
# minutes later can differ by a handful of rows. A small drift is fine; a large
# one means the download was truncated.
drift = abs(len(ems) - expected_n)
print(f"Row-count drift: {drift:,} ({drift / expected_n:.4%})")
assert drift <= max(50, 0.001 * expected_n), (
    f"row count off by {drift:,} -- download likely truncated, not a refresh"
)
```

The duplicate-ID check is the one that catches a pagination failure, which is why `$order` is in
Step 5. Note it is `assert`ed here rather than just printed.

---

## Step 7 — Parse and engineer basic features

```python
ems["incident_datetime"] = pd.to_datetime(ems["incident_datetime"], errors="coerce")

numeric_cols = [
    "incident_response_seconds_qy",
    "dispatch_response_seconds_qy",
    "incident_travel_tm_seconds_qy",
    "initial_severity_level_code",
]
for col in numeric_cols:
    ems[col] = pd.to_numeric(ems[col], errors="coerce")

# errors="coerce" turns anything unparseable into NaN rather than raising, so
# check how many it created before trusting the column.
print("NaT datetimes:", int(ems["incident_datetime"].isna().sum()))
for col in numeric_cols:
    print(f"  NaN in {col}: {int(ems[col].isna().sum()):,}")

ems["year"]        = ems["incident_datetime"].dt.year
ems["month"]       = ems["incident_datetime"].dt.month
ems["hour"]        = ems["incident_datetime"].dt.hour
ems["weekday_num"] = ems["incident_datetime"].dt.weekday     # Monday = 0
ems["weekday"]     = ems["incident_datetime"].dt.day_name()

# 0-167: the proposal's "area-hour" unit is dispatch area x hour of the week.
ems["hour_of_week"] = ems["weekday_num"] * 24 + ems["hour"]
```

> *Also create a time block for reporting.*

```python
# Six four-hour blocks. These are the reporting grid on Slide 9 -- compact enough
# that a ranked list fits on a slide, and each cell holds enough calls to be stable.
ems["time_block"] = pd.cut(
    ems["hour"],
    bins=[-1, 3, 7, 11, 15, 19, 23],
    labels=["12-4 AM", "4-8 AM", "8 AM-12 PM", "12-4 PM", "4-8 PM", "8 PM-12 AM"]
)
print(ems["time_block"].value_counts().sort_index())
```

---

## Step 8 — Validation before filtering

> *Do not filter first — check what you would be removing before you remove it.*

```python
print("Date range:", ems["incident_datetime"].min(), "to", ems["incident_datetime"].max())

print("\nYear counts:")
print(ems["year"].value_counts().sort_index())

print("\nMonth counts for 2026 (is September complete?):")
print(ems.loc[ems["year"] == 2026, "month"].value_counts().sort_index())

print("\nInitial severity codes:")
print(ems["initial_severity_level_code"].value_counts(dropna=False).sort_index())

print("\nIncident response validity:")
print(ems["valid_incident_rspns_time_indc"].value_counts(dropna=False))
```

> *Confirm that severity 1–3 actually exist and mean what the team's definition assumes, then apply
> the filter.*

The proposal defines a life-threatening call as **severity segment 1–3**. This cell is where that is
confirmed rather than assumed. Two things to look at:

- **What values actually appear.** If the codes run 1–8, then 1–3 is a defensible top band. If they
  run 1–9 or include text, the definition needs revisiting before the slide claims it.
- **How many rows each code holds.** If severity 1 is a few hundred records a year and severity 3 is
  hundreds of thousands, "life-threatening" is being carried almost entirely by one code.

---

## Step 9 — Life-threatening and valid-response filtering

> *Preliminary definition, per the proposal:*

```python
before = len(ems)

analysis = ems[ems["initial_severity_level_code"].isin([1, 2, 3])].copy()
print(f"After severity 1-3:        {len(analysis):,}  ({len(analysis)/before:.1%} of all)")

# Valid response time: FDNY's own flag for whether the recorded time can be trusted.
analysis = analysis[analysis["valid_incident_rspns_time_indc"] == "Y"].copy()
print(f"After validity flag = Y:   {len(analysis):,}")

analysis = analysis[analysis["incident_response_seconds_qy"] > 0].copy()
print(f"Final analysis rows:       {len(analysis):,}")
```

```python
# The proposal flags that ~1.13 million incidents have no valid response time.
# Dropping them is right, but check whether they cluster: if invalid records are
# concentrated in particular areas or hours, dropping them biases those cells
# toward looking faster than they are. This is a Data Understanding finding.
invalid_rate = (
    ems[ems["initial_severity_level_code"].isin([1, 2, 3])]
      .assign(invalid=lambda d: d["valid_incident_rspns_time_indc"].ne("Y"))
      .groupby("incident_dispatch_area")["invalid"]
      .agg(["mean", "size"])
      .rename(columns={"mean": "pct_invalid", "size": "calls"})
      .sort_values("pct_invalid", ascending=False)
)
print(invalid_rate.head(10))
print("\nSpread across areas:", f"{invalid_rate['pct_invalid'].min():.1%}",
      "to", f"{invalid_rate['pct_invalid'].max():.1%}")
```

> *Whether to drop standby / special-event records does not need to be hard-coded straight away.
> Check the distributions first:*

```python
for col in ["standby_indicator", "special_event_indicator",
            "transfer_indicator", "held_indicator", "reopen_indicator"]:
    print("\n", col)
    print(analysis[col].value_counts(dropna=False))
```

> *Then remove them according to the team's rule.*

A standby at a parade is not a failed emergency response, so leaving those rows in distorts every
number. `held` and `reopen` are different — those are real responses that went badly, and removing
them would remove the evidence. The defensible split:

```python
# Remove: not genuine emergency responses.
# Keep:   genuine responses, however they went (held / reopened / transferred).
analysis = analysis[
    (analysis["standby_indicator"] != "Y") &
    (analysis["special_event_indicator"] != "Y")
].copy()
print(f"After dropping standby and special-event: {len(analysis):,}")
```

---

## Step 10 — Create reusable year splits

```python
train_2024   = analysis[analysis["year"] == 2024].copy()
valid_2025   = analysis[analysis["year"] == 2025].copy()
holdout_2026 = analysis[analysis["year"] == 2026].copy()

print("2024:        ", f"{len(train_2024):,}")
print("2025:        ", f"{len(valid_2025):,}")
print("2026 holdout:", f"{len(holdout_2026):,}")

# The finished proposal pools 2024-2025 for development. Build that frame now too,
# so switching designs is one variable rather than a rewrite.
dev_2024_2025 = analysis[analysis["year"].isin([2024, 2025])].copy()
print("2024-2025 development:", f"{len(dev_2024_2025):,}")
```

> *Building this structure means the team can go either way on the timeline.*

**`holdout_2026` is not touched again until the design is settled and the models are final.** That
is the entire argument for the proposal's three-period structure: the moment you look at a period to
make a modeling decision, it stops being out-of-sample.

---

## Step 11 — The "late" cutoff, both versions

> *For experimenting with the current slide design:*

```python
def add_late_label(df, cutoff):
    """Flag calls slower than a FIXED citywide cutoff. The cutoff is computed once,
    from the development data only, and then applied unchanged to every period."""
    out = df.copy()
    out["late"] = (out["incident_response_seconds_qy"] > cutoff).astype(int)
    return out


# ---- Design 1: the deck -- cutoff from 2024, test on 2025 -------------------
cutoff_deck = train_2024["incident_response_seconds_qy"].quantile(0.90)
print("2024 citywide P90:", round(cutoff_deck, 1), "seconds",
      f"({cutoff_deck/60:.1f} min)")

# ---- Design 2: the finished proposal -- cutoff from 2024-2025 ---------------
# "the 90th-percentile threshold is set from 2024-2025 only, so the 2026 test
#  data does not shape it"
cutoff_prop = dev_2024_2025["incident_response_seconds_qy"].quantile(0.90)
print("2024-2025 citywide P90:", round(cutoff_prop, 1), "seconds",
      f"({cutoff_prop/60:.1f} min)")

# CITYWIDE is load-bearing. A per-area-hour percentile would make every cell
# exactly 10% late by construction and the ranking would be pure noise.
LATE_CUTOFF = cutoff_deck        # <- change to cutoff_prop once the team decides

train_2024    = add_late_label(train_2024,    LATE_CUTOFF)
valid_2025    = add_late_label(valid_2025,    LATE_CUTOFF)
holdout_2026  = add_late_label(holdout_2026,  LATE_CUTOFF)
dev_2024_2025 = add_late_label(dev_2024_2025, LATE_CUTOFF)
```

```python
print("2024 late rate:   ", f"{train_2024['late'].mean():.1%}")
print("2025 late rate:   ", f"{valid_2025['late'].mean():.1%}")
print("2026 late rate:   ", f"{holdout_2026['late'].mean():.1%}")
```

> *2024 should be about 10%. 2025 does not have to be 10%.*

**And it probably will not be.** The proposal's own evidence says response times are rising — 11%
demand growth FY2015–FY2026, worsening times at nearly every stage. A cutoff fixed on older data
will flag **more** than 10% of later calls.

That is not a bug, it is a finding, and it has one consequence to handle: the proposal sets the
precision target at "at least 20% (twice the 10% base rate)". If the test-period base rate is 13%,
that parenthesis is wrong and the target quietly becomes easier. **Restate the target against the
observed base rate in the test period, and report that rate next to it.** The drift itself is free
evidence for the business case.

---

## Step 12 — Baseline ranking (Model 0)

> *This is Model 0 / the benchmark.*

```python
def build_baseline(df, keys, min_calls=50):
    """Rank cells by their own P90 response time. No machine learning.
    Every model has to beat this to earn its place."""
    out = (
        df.groupby(keys, observed=True)
          .agg(calls=("incident_id", "size"),
               p90_response=("incident_response_seconds_qy", lambda x: x.quantile(0.90)),
               late_rate=("late", "mean"))
          .reset_index()
    )
    kept = out[out["calls"] >= min_calls]
    print(f"  {keys}: {len(out):,} cells, {len(kept):,} survive calls >= {min_calls} "
          f"({len(kept)/len(out):.0%}); median calls per cell = {out['calls'].median():.0f}")
    return kept.sort_values("p90_response", ascending=False)


print("Baseline grids:")
baseline_hour_of_week = build_baseline(train_2024, ["incident_dispatch_area", "hour_of_week"])
baseline_time_block   = build_baseline(train_2024, ["incident_dispatch_area", "time_block"])

baseline_time_block.head(20)
```

**Read the two survival percentages before choosing.** See §3 — `area × hour_of_week` is roughly
11,760 cells and will likely lose most of them to the ≥ 50 filter, while `area × time_block` is
about 420 cells with roughly 900 calls each. Whichever you pick, the deck and the proposal must then
use the same noun.

---

## Step 13 — Logistic regression

> *Now import the modeling libraries.*

```python
from sklearn.base import clone                      # <- fixes bug 4
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import OneHotEncoder
from sklearn.impute import SimpleImputer
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import roc_auc_score, recall_score, precision_score
```

> *Predictors are limited to what is known at the time the call arrives:*

```python
FEATURES = [
    "incident_dispatch_area",
    "hour",
    "weekday",
    "month",
    "initial_severity_level_code",
    "initial_call_type",
]
TARGET = "late"
```

> *`held_indicator` is not included here.*

Nor is anything else produced during or after the response. **Two gaps worth noting to the team:**

- The proposal lists **prior-hour call volume** as a predictor ("calls in the same area in the
  previous hour, which is known when a call arrives"). It is not in `FEATURES`. It is the only
  feature in the proposal that carries load information, which is exactly what drives dispatch
  delay — worth adding.
- `hour` and `weekday` enter separately, so logistic regression gets no area × hour interaction.
  Random forest will find it; logistic regression will not. Adding `hour_of_week` as a categorical
  gives the linear model the same chance.

```python
# Everything goes through OneHotEncoder, including hour / month / severity. Treating
# hour as categorical rather than numeric is deliberate: delay risk at 3 AM is not
# three times the risk at 1 AM.
#
# min_frequency=50 folds rare call types into an "infrequent" bucket, which keeps
# the design matrix bounded. handle_unknown="ignore" means a category that appears
# only in the test period does not crash prediction.
prep = ColumnTransformer([
    ("cat",
     Pipeline([
         ("imputer", SimpleImputer(strategy="most_frequent")),
         ("onehot",  OneHotEncoder(handle_unknown="ignore", min_frequency=50)),
     ]),
     FEATURES)
])

# FIX (bug 4): clone(prep) everywhere. Pipeline fits its steps IN PLACE and does not
# clone them, so passing the same `prep` object to three pipelines means the last
# .fit() wins and silently rewrites the feature space of the earlier two.

# --- Model for RANKING: no class_weight, so predicted probabilities are calibrated
#     and summing them gives a real expected count. (FIX, bug 2.)
logit_rank = Pipeline([
    ("prep",  clone(prep)),
    ("model", LogisticRegression(max_iter=1000, solver="lbfgs")),
])

# --- Model for RECALL: class_weight="balanced" to meet the proposal's >=80% recall
#     target. Use it for recall/precision, NEVER for expected counts.
logit_bal = Pipeline([
    ("prep",  clone(prep)),
    ("model", LogisticRegression(max_iter=1000, class_weight="balanced", solver="lbfgs")),
])
```

```python
logit_rank.fit(train_2024[FEATURES], train_2024[TARGET])
logit_bal.fit(train_2024[FEATURES],  train_2024[TARGET])

valid_2025["logit_risk"]     = logit_rank.predict_proba(valid_2025[FEATURES])[:, 1]
valid_2025["logit_risk_bal"] = logit_bal.predict_proba(valid_2025[FEATURES])[:, 1]

auc = roc_auc_score(valid_2025["late"], valid_2025["logit_risk"])
print("2025 Logistic AUC:", round(auc, 3), "  (proposal target: >= 0.75)")

# THE CALIBRATION CHECK -- this is what catches bug 2 if it ever comes back.
# For a calibrated model these two numbers should be close.
print("\nCalibration (ranking model):")
print("  mean predicted risk:", round(valid_2025['logit_risk'].mean(), 4))
print("  actual late rate:   ", round(valid_2025['late'].mean(), 4))
print("  sum of risk:        ", round(valid_2025['logit_risk'].sum(), 1))
print("  actual late calls:  ", int(valid_2025['late'].sum()))

print("\nBalanced model, for comparison -- note the inflation:")
print("  mean predicted risk:", round(valid_2025['logit_risk_bal'].mean(), 4))

# Recall and precision come from the balanced model at a 0.5 threshold.
pred_bal = (valid_2025["logit_risk_bal"] >= 0.5).astype(int)
print("\nBalanced model @0.5 -- recall:",
      round(recall_score(valid_2025['late'], pred_bal), 3),
      " precision:", round(precision_score(valid_2025['late'], pred_bal), 3))
print("base rate in this period:", round(valid_2025['late'].mean(), 3),
      "<- the precision target should be stated against THIS, not against 10%")
```

**Colab performance note.** `solver="lbfgs"` replaces `liblinear` here; on a sparse one-hot matrix of
several hundred thousand rows it converges considerably faster. If it warns about convergence, raise
`max_iter` rather than switching back.

---

## Step 14 — Build the actual risk-map table

```python
hotspots = (
    valid_2025
    .groupby(["incident_dispatch_area", "time_block"], observed=True)
    .agg(
        calls               = ("incident_id",                  "size"),
        avg_late_risk       = ("logit_risk",                   "mean"),
        # Only meaningful because logit_rank is calibrated -- see bug 2.
        expected_late_calls = ("logit_risk",                   "sum"),
        actual_late_calls   = ("late",                         "sum"),
        actual_late_rate    = ("late",                         "mean"),
        p90_response        = ("incident_response_seconds_qy", lambda x: x.quantile(0.90)),
    )
    .reset_index()
)

hotspots = (hotspots[hotspots["calls"] >= 50]
            .sort_values("expected_late_calls", ascending=False))

# Reconciliation: expected and actual totals should be close across the whole table.
print("Total expected late calls:", round(hotspots['expected_late_calls'].sum(), 1))
print("Total actual late calls:  ", int(hotspots['actual_late_calls'].sum()))

hotspots.head(20)
```

> *This actually implements Slide 7's Input → Logistic → Risk map → Rank.*

Note that `expected_late_calls` ranks by **volume × risk**, not risk alone. That is the right
objective for a capacity decision — a small area with terrible odds matters less than a busy area
with merely bad odds, because tours are placed where they serve the most calls. Worth saying out
loud in the presentation, because it looks like a detail and is actually the business logic.

---

## Step 15 — Linear / Ridge model

> *Use the same preprocessing:* — but a **cloned** copy, per bug 4.

```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_absolute_error

ridge = Pipeline([
    ("prep",  clone(prep)),          # FIX (bug 4)
    ("model", Ridge(alpha=10.0, solver="lsqr")),
])
```

> *The target is log response seconds:*

```python
# Response times are right-skewed, so model log seconds. log1p/expm1 handle a
# zero safely, though Step 9 already removed non-positive response times.
y_train_log = np.log1p(train_2024["incident_response_seconds_qy"])

ridge.fit(train_2024[FEATURES], y_train_log)

pred_log     = ridge.predict(valid_2025[FEATURES])
pred_seconds = np.expm1(pred_log)

ridge_mae = mean_absolute_error(valid_2025["incident_response_seconds_qy"], pred_seconds)
print("Ridge MAE:", round(ridge_mae, 1), "seconds")

# The benchmark it has to beat: predicting the training mean for every call.
naive = np.full(len(valid_2025), train_2024["incident_response_seconds_qy"].mean())
print("Naive-mean MAE:", round(
    mean_absolute_error(valid_2025["incident_response_seconds_qy"], naive), 1), "seconds")
```

Back-transforming with `expm1` gives something closer to a conditional median than a mean, which is
the right target for MAE anyway. The naive comparison is added because "MAE = 190 seconds" means
nothing on its own — the proposal says the model must beat a baseline, so compute the baseline.

---

## Step 16 — Random forest experiment

> *Start with a sample:*

```python
from sklearn.ensemble import RandomForestClassifier

MAX_RF_TRAIN = 300_000
rf_train = (train_2024.sample(MAX_RF_TRAIN, random_state=42)
            if len(train_2024) > MAX_RF_TRAIN else train_2024)
print(f"RF training rows: {len(rf_train):,}")

rf = Pipeline([
    ("prep",  clone(prep)),          # FIX (bug 4) -- this is the one that was breaking the others
    ("model", RandomForestClassifier(
        n_estimators=100,            # start at 100 in Colab; raise once it runs
        max_depth=14,
        min_samples_leaf=50,
        n_jobs=-1,
        random_state=42,
        class_weight="balanced_subsample",
    )),
])

rf.fit(rf_train[FEATURES], rf_train["late"])

valid_2025["rf_risk"] = rf.predict_proba(valid_2025[FEATURES])[:, 1]
rf_auc = roc_auc_score(valid_2025["late"], valid_2025["rf_risk"])

print("Logistic AUC:    ", round(auc, 3))
print("Random Forest AUC:", round(rf_auc, 3))
```

**Colab warning.** A forest on a wide sparse one-hot matrix is slow and memory-hungry. `n_estimators`
is 100 here rather than 200 — run it once, see the wall time, then raise it. If the free tier runs
out of RAM, cut `MAX_RF_TRAIN` to 150,000 first; the AUC comparison is what matters, not the last
decimal.

`class_weight="balanced_subsample"` has the same calibration consequence as bug 2, so **`rf_risk`
is for AUC comparison, not for expected counts.**

**The question you will be asked:** if the forest wins on AUC, do you deploy it? For a ranked list
FDNY must defend in a budget hearing, interpretability can reasonably outrank a small AUC gain.
Report both, say which you would ship and why. Have that answer ready — it is the most likely hard
question on Slide 7.

---

## Step 17 — Dispatch versus travel diagnosis

> *This is a diagnostic for interpreting results, not a model predictor.*

```python
city_dispatch_med = valid_2025["dispatch_response_seconds_qy"].median()
city_travel_med   = valid_2025["incident_travel_tm_seconds_qy"].median()
print("Citywide median dispatch:", round(city_dispatch_med, 1), "s")
print("Citywide median travel:  ", round(city_travel_med, 1), "s")

diagnosis = (
    valid_2025
    .groupby(["incident_dispatch_area", "time_block"], observed=True)
    .agg(
        median_dispatch=("dispatch_response_seconds_qy",  "median"),
        median_travel  =("incident_travel_tm_seconds_qy", "median"),
    )
    .reset_index()
)
# FIX (bug 3): `calls` is deliberately NOT aggregated here. hotspots already has it,
# and having it in both frames is what produced calls_x / calls_y and the KeyError.

diagnosis["dispatch_excess"] = diagnosis["median_dispatch"] - city_dispatch_med
diagnosis["travel_excess"]   = diagnosis["median_travel"]   - city_travel_med

# A cell is only "limited" by something if it is actually slower than the city on
# that component. The draft labelled every cell, including ones faster than
# citywide on both, where neither label means anything.
both_fast = (diagnosis["dispatch_excess"] <= 0) & (diagnosis["travel_excess"] <= 0)

diagnosis["main_constraint"] = np.where(
    both_fast, "Not slow",
    np.where(diagnosis["dispatch_excess"] > diagnosis["travel_excess"],
             "Dispatch-limited", "Travel-limited")
)
print(diagnosis["main_constraint"].value_counts())
```

> *This is an operational heuristic, not causal proof.*

Keep that sentence in the presentation. The decomposition says *where the seconds accumulate*, not
*why*; a dispatch-limited cell is consistent with too few units, and also with demand spikes that
more units would not fix.

---

## Step 18 — Prototype deployment table

```python
deployment_preview = hotspots.merge(
    diagnosis,
    on=["incident_dispatch_area", "time_block"],
    how="left",
)

deployment_preview["recommended_action"] = np.select(
    [
        deployment_preview["main_constraint"] == "Dispatch-limited",
        deployment_preview["main_constraint"] == "Travel-limited",
    ],
    [
        "Review additional tour / staffing capacity",
        "Review unit positioning / travel conditions",
    ],
    default="No action indicated",
)

# `calls` resolves cleanly now that diagnosis no longer carries a column of that name.
deployment_preview[[
    "incident_dispatch_area", "time_block", "calls",
    "avg_late_risk", "expected_late_calls", "actual_late_calls",
    "p90_response", "main_constraint", "recommended_action",
]].head(20)
```

> *This is the step that replaces Slide 9's District A/B/C placeholders with actual preliminary
> output.*

```python
# Save it -- Colab's filesystem does not survive a disconnect.
deployment_preview.to_csv("data/deployment_preview.csv", index=False)
from google.colab import files
files.download("data/deployment_preview.csv")
```

---

## Run order

> *When you actually run this, do not do it all in one go.*

| Session | Steps | What it confirms |
|---|---|---|
| **1 — today** | 0 → 1 → 2 → 3 → 4 → 5 → 6 | *That far confirms the data came in correctly.* Step 1 also answers the September-2026 question the team is waiting on |
| **2** | 7 → 8 → 9 → 10 | *Check the cleaning and the splits.* Step 8 settles the severity-code definition |
| **3** | 11 → 12 → 13 → 14 | *Once this far, the core analysis on the current slides is actually implemented.* |
| **4** | 15 → 16 → 17 → 18 | *Ridge and Random Forest can be added after that.* Plus the deployment table |

> *So the earlier 2025 server-side filtering, row-count, stable pagination and validation steps were
> never meant to be discarded. More precisely, the current final version takes the 2025-only part,
> extends it to 2024–September 2026, and folds it into the front of the pipeline above.*

That is right, and it is worth saying to the team in exactly those terms — those four habits
(server-side filtering, an independent row count, `$order` for stable pagination, and an assert
before proceeding) are what make the download trustworthy, and they transferred wholesale.

---

## What Session 1 should report back to the team

Three numbers, and they unblock other people's work:

1. **Is September 2026 complete?** From Step 1. Decides Jan–Sep versus Jan–Aug, and the slide wording.
2. **What `expected_n` came back?** From Step 4. Roughly 3.8 million is expected; the deck should say
   the working figure, not the 30.4 million catalog figure.
3. **What severity codes actually exist, and how many rows each holds?** From Step 8. Confirms or
   corrects the "severity 1–3" definition before it goes on a slide.

Two decisions still open, both from the deck-versus-proposal reconciliation:

- **Which timeline** — 2024 train / 2025 test, or 2024–2025 develop / Jan–Sep 2026 held out. Step 11
  implements both; one variable switches between them.
- **Which reporting grid** — `area × hour_of_week` or `area × time_block`. Step 12 prints how many
  cells survive under each, so the team can decide from counts rather than preference.
