> **Skill:** r-analytics (ggplot2 data visualization) · **Source:** Baruch STA 9750 PA #06 · **Mode:** Coursework · **Date:** 2026-10-16
> Companion: [ggplot2 aesthetics + join-validation drills](./ggplot2-aesthetics_join-validation-drills.md) · index: [r-analytics README](./README.md)
> *ggplot2 is R-native visualization → lives in r-analytics (not `bi/`, which is Power BI/Tableau/Excel). Original walkthrough below, verbatim.*

---

# STA 9750 PA #06 — "Lots of Plots" · RStudio Walkthrough

**Brightspace due: Tuesday 20 October, 6:00 PM.** Quiz opened 6 October. Your Thursday section's
recommended completion date (15 Oct) has passed, but the recommended date is not the deadline — the
Brightspace due date is what counts, and you have until Tuesday evening.

---

## 1. What this assignment actually is

**It is a read-through, not a problem set.** There are no numbered exercises and nothing to submit
from R. The document builds *one* scatter plot across thirteen revisions, and the grade comes from
the Brightspace quiz.

So the useful thing to do in RStudio is run the thirteen stages yourself and watch what each line
changes. The professor says this explicitly:

> *"As you review this document, also watch how I iterate and refine each figure until I have
> something I'm finally happy with… You should adapt a similar pattern of 'take a sad plot and make
> it better' as you create plots for your mini-projects and, ultimately, your final project."*

That is a direct signal about MP#02 and the final project. The plots in your crime track will be
graded on this standard.

**Time estimate: 30–40 minutes**, most of it waiting for one package to install.

---

## 2. Two things to settle before you start

### 2.1 Which `penguins` is this?

The document uses `flipper_len` and `body_mass`. **Those are the column names in base R's built-in
`penguins` dataset, added in R 4.5.0** — not the `palmerpenguins` package, where the same columns
are called `flipper_length_mm` and `body_mass_g`.

You are on R 4.6.1, so the base version is already there and **you do not need `library(palmerpenguins)`**.
If you load palmerpenguins out of habit it will mask the base dataset and every line in this
assignment will fail with "object 'flipper_len' not found."

Confirm in one line before anything else:

```r
names(penguins)
```

You should see: `species`, `island`, `bill_len`, `bill_dep`, `flipper_len`, `body_mass`, `sex`, `year`.

If you instead see `flipper_length_mm`, something has masked it — run `conflicts()` or restart R
with a clean session (Session → Restart R).

### 2.2 Install `ggpmisc` now, not at stage 10

Stage 10 needs it, and it pulls in several dependencies (`ggpp`, `polynom`, `confintr`, `quantreg`
and others). On macOS some of those may compile from source, which can take **several minutes**.
Start it first so it finishes while you work through stages 1–9.

```r
install.packages("ggpmisc")
```

The document gives it as `if(!require("ggpmisc")) install.packages("ggpmisc")`. That works, but note
what it does: `require()` returns `FALSE` and prints a warning rather than erroring when the package
is missing — so the `if` installs it but **does not load it**. That is why `library(ggpmisc)` is on
the next line. Not a bug, just worth seeing.

---

## 3. The thirteen stages

Put these in a script (File → New File → R Script), not the console — you will want to re-run
earlier stages to compare. Run each block with Cmd+Return and look at the Plots pane before moving on.

### Setup

```r
library(ggplot2)
# No palmerpenguins. `penguins` is built into R 4.5+.
names(penguins)
```

---

### Stage 1 — the blank canvas

```r
ggplot(penguins)
```

**Look for:** nothing. A grey rectangle. This is the point — you have declared a dataset and nothing
else, so there is nothing to draw.

---

### Stage 2 — add aesthetics

```r
ggplot(penguins, aes(x = flipper_len, y = body_mass))
```

**Look for:** axes appear, with ranges and labels, but still no data. The *scales* have been built
from the data; no `geom` has put ink on the page yet.

---

### Stage 3 — add a geom

```r
ggplot(penguins,
       aes(x = flipper_len, y = body_mass)) +
    geom_point()
```

**Look for:** the scatter plot, and a warning in the console:

```
Warning: Removed 2 rows containing missing values or values outside the scale range
```

The document addresses this directly: *"This is fine and safe to ignore because we know our data has
some NAs, but if you are getting this in unexpected circumstances, you may want to look into it
further."* Two penguins have missing measurements.

**This is the one place in the assignment that connects to the trap you have been collecting all
semester.** ggplot *tells you* it dropped rows. `filter()` does not. Same silent-deletion problem,
opposite behaviour.

---

### Stage 4 — axis labels

```r
ggplot(penguins,
       aes(x = flipper_len, y = body_mass)) +
    geom_point() +
    xlab("Flipper Length (mm)") +
    ylab("Body Mass (g)")
```

**Why:** *"we never want to let variable names 'leak' in plots we intend to share with others."*
Note that this adds the units, which the column names do not carry.

---

### Stage 5 — theme

```r
ggplot(penguins,
       aes(x = flipper_len, y = body_mass)) +
    geom_point() +
    xlab("Flipper Length (mm)") +
    ylab("Body Mass (g)") +
    theme_bw()
```

**The distinction to hold on to:** `theme()` controls *non-data* elements — background, fonts, grid.
It never changes how the data itself is drawn. That line is likely quiz material.

---

### Stage 6 — colour by species

```r
ggplot(penguins,
       aes(x = flipper_len,
           y = body_mass,
           color = species)) +
    geom_point() +
    xlab("Flipper Length (mm)") +
    ylab("Body Mass (g)") +
    theme_bw()
```

**Three things happened automatically**, and the document lists them because they are testable:

1. The `color` aesthetic **propagated into `geom_point`** — top-level aesthetics are inherited by any
   geom that can use them.
2. A legend was created.
3. A colour scale was chosen.

Also noted: `geom_point` **requires** `x` and `y`; `color` is **optional**. Check it yourself with
`?geom_point` and read the Aesthetics section.

---

### Stage 7 — a better palette

```r
ggplot(penguins,
       aes(x = flipper_len,
           y = body_mass,
           color = species)) +
    geom_point() +
    xlab("Flipper Length (mm)") +
    ylab("Body Mass (g)") +
    theme_bw() +
    scale_color_brewer(type = "qual", palette = 2)
```

**Why `type = "qual"`:** *"there is no inherent ordering to the penguin types."* Qualitative palettes
for categories with no order; sequential for magnitude; diverging for polarity around a midpoint.
Using a sequential ramp for species would imply Adelie < Chinstrap < Gentoo, which is meaningless.

Try `palette = 1` through `8` to see the alternatives.

---

### Stage 8 — name the legend

```r
    scale_color_brewer(type = "qual",
                       palette = 2,
                       name = "Species")
```

(Append to stage 7 — the full block is in §4.)

**Note where the legend title lives:** inside the *scale*, not in `labs()` or `theme()`. The scale
owns the mapping, so it owns the guide that explains the mapping.

---

### Stage 9 — move the legend

```r
    theme(legend.position = "bottom")
```

**Why `theme()` and not the scale:** *"This involves changing a 'non-data' element of the plot, so we
go through the theme machinery."* Position is layout; the colour-to-species mapping is unchanged.

---

### Stage 10 — a regression line

```r
    geom_smooth(method = "lm")
```

Insert after `geom_point()`. **Look for:** three lines, one per species, each with a shaded
confidence band. Also note that `color = species` was inherited here too — that is why you get three
lines rather than one.

The document connects this to the statistics: for univariate regression the slope is

$$\hat\beta = \rho_{XY} \frac{\sigma_Y}{\sigma_X}$$

Then turn the bands off:

```r
    geom_smooth(method = "lm", se = FALSE)
```

---

### Stage 11 — facet

```r
    facet_wrap(~species)
```

**The document's own framing:** *"a 'small multiples' plot: this is, in essence, a `group_by` for
plotting."* That is the sentence to remember — faceting is to plots what `group_by` is to summaries.

Note the `~` (formula notation), not a bare column name.

---

### Stage 12 — override the line colour

```r
    geom_smooth(method = "lm",
                se = FALSE,
                color = "black")
```

**This is the single most likely quiz question in the whole document.** `color` appears twice in this
plot and means two different things:

| Where | Form | What it does |
|---|---|---|
| Inside `aes()` | `color = species` | **Maps** a column to colour. Produces a legend and a scale |
| Inside `geom_smooth()`, outside `aes()` | `color = "black"` | **Sets** a constant. No legend, no scale |

Mapping versus setting. If you put `color = "black"` *inside* `aes()`, ggplot would treat `"black"`
as a one-level categorical variable, create a legend entry labelled "black", and paint it salmon.
Try it once so you have seen it.

---

### Stage 13 — `ggpmisc`, actual numbers on the plot

```r
library(ggpmisc)
```

Replace `geom_smooth(...)` with the two `stat_` layers:

```r
    stat_poly_line(se = FALSE, color = "black") +
    stat_poly_eq() +
```

**The new vocabulary:** `stat_` layers are *statistical transformations*. The document notes these are
usually applied "automagically" — `geom_smooth` was silently running one — but here you build the
regression explicitly so `stat_poly_eq()` can print R² on each panel.

`ggpmisc` is a **ggplot2 extension package**: it supplies new `geom_`, `scale_` and `stat_` objects
that slot into the same grammar. The point the professor is making: *"even though we're using
functionality from outside ggplot2, the structure of the 'grammar' makes it easy for all these tools
to work well together."*

---

## 4. The complete final plot

**Two strings in the PDF are cut off at the right margin.** I have completed them below from context,
and marked them — check these two lines against the live page at
`https://michael-weylandt.com/STA9750/pre/pa06.html` before relying on the exact wording, since the
quiz could conceivably quote them.

```r
library(ggplot2)
library(ggpmisc)

ggplot(penguins,
       aes(x = flipper_len,
           y = body_mass,
           color = species)) +
    geom_point() +
    stat_poly_line(se = FALSE,
                   color = "black") +
    stat_poly_eq() +
    xlab("Flipper Length (mm)") +
    ylab("Body Mass (g)") +
    theme_bw() +
    scale_color_brewer(type = "qual",
                       palette = 2,
                       name = "Species") +
    theme(legend.position = "bottom") +
    facet_wrap(~species) +
    # [TRUNCATED IN THE PDF — reconstructed]
    ggtitle("Correlation of Flipper Length and Body Mass across Penguin Species") +
    # [TRUNCATED IN THE PDF — reconstructed]
    labs(caption = "Data provided by Dr. K. Gorman and the Palmer Station, Antarctica LTER") +
    guides(color = "none")
```

### The last two moves

**`labs(caption = ...)`** — *"In good academic practice, we should always add a footnote citing the
source of our data."* Worth internalising for the crime track: your plots use NYPD Complaint Data and
Open-Meteo, and both belong in a caption.

**`guides(color = "none")`** — removes the legend, because after faceting the panel strips already
name each species. The principle:

> *"the concept of a 'ink-to-information' ratio is useful: if you can remove some ink without removing
> any (relevant) information, you should generally do so."*

Note the interaction: once `guides(color = "none")` is in, the earlier
`theme(legend.position = "bottom")` has nothing left to position. Both lines stay in the
professor's final version — that is the residue of iterative refinement, not a mistake.

### The optional subtitle

```r
    ggtitle("Correlation of Flipper Length and Body Mass across Penguin Species",
            subtitle = "Flipper Length and Body Mass are positively correlated") +
```

The professor's own caveat, which reads like quiz material:

> *"This is not always a good idea — it requires 'hard-coding' the insights for your reader and takes
> up some space. In scientific writing, I generally prefer to put this sort of summary in a figure
> caption, while I tend to 'say the point' verbally if the figure is destined for a presentation.
> Personally, I only use this sort of 'here is the point' text if I expect a figure to 'escape beyond'
> my presentation and need to stand fully on its own."*

**Directly relevant to Thursday.** Your check-in figures will be *spoken to*, so the point goes in
your narration, not in a subtitle. The report figures might travel alone, so those take captions.

---

## 5. Likely quiz ground

Nothing here is a prediction of specific questions — it is the vocabulary and the distinctions the
document goes out of its way to define.

**The four grammar components, as the document names them:**

| Component | What it is |
|---|---|
| **Aesthetics** | Mappings between data columns and visual properties ("put grade on the y axis") |
| **Scales** | Convert data values to aesthetics (position on an axis, value to colour) |
| **Geoms** | How the data is represented on the page — points, lines, map boundaries |
| **Guides** | Interpretational assistance for the viewer. *"Most guides take the form of legends"* |

**Tidy data, the four bullets:** rectangular array · homogeneously typed within a column · one
observation per row · one value per cell.

**Distinctions worth being able to state:**

- **`+` versus `|>`.** The document calls this out: ggplot2 *adds* elements to build a plot; dplyr
  *pipes* data from one step to the next. Adding layers, not transforming data.
- **Mapping versus setting** — `aes(color = species)` against `geom_smooth(color = "black")`. §Stage 12.
- **Scale versus theme.** The legend *title* lives in `scale_color_brewer(name = ...)`; the legend
  *position* lives in `theme(legend.position = ...)`. Data mapping versus layout.
- **Required versus optional aesthetics.** `geom_point` requires `x` and `y`; `color` is optional.
- **Aesthetic inheritance.** Top-level `aes()` propagates to every geom that can use it — which is
  why one `color = species` produced three regression lines.
- **Qualitative versus sequential palettes** — `type = "qual"` because species have no ordering.
- **`stat_` layers** are statistical transformations, usually applied implicitly (`geom_smooth`),
  explicit here so the R² can be printed.
- **Ink-to-information ratio** — remove ink that carries no information.

**Two names:** the Grammar of Graphics was designed by **Leland Wilkinson** and popularised by
**Hadley Wickham**; `gg` in `ggplot2` stands for Grammar of Graphics.

---

## 6. Gotchas

| | |
|---|---|
| **palmerpenguins masking** | Do not load it. Base R 4.5+ `penguins` uses `flipper_len` / `body_mass`. Check with `names(penguins)` first |
| **ggpmisc install time** | Several dependencies, possibly compiled from source. Start it before stage 1 |
| **The NA warning at stage 3** | Expected — 2 rows dropped. Not an error |
| **`facet_wrap(~species)`** | Formula notation. `facet_wrap(species)` fails |
| **`se = FALSE` placement** | Argument of `geom_smooth` / `stat_poly_line`, not a top-level layer |
| **`color = "black"` must be outside `aes()`** | Inside, it becomes a mapped one-level factor and paints salmon |
| **Two truncated strings** | The `ggtitle` and `labs(caption=)` text in §4 is reconstructed from the cut-off PDF. Verify against the live page |

---

## 7. Order of work tonight

1. `names(penguins)` — 10 seconds, prevents the most likely failure.
2. `install.packages("ggpmisc")` — start it and leave it running.
3. Stages 1–9 in a script. Run each, look at the Plots pane, use the back-arrow to compare.
4. Stages 10–13 once `ggpmisc` has finished.
5. Run the complete plot from §4.
6. **One experiment of your own**, because it is the fastest way to lock in the one distinction most
   likely to be tested: move `color = "black"` inside `aes()` and see what happens.
7. Take the quiz.

If a quiz question turns on the exact wording of the title or caption, open the live page rather than
trusting my reconstruction — those are the only two places in this walkthrough where I am filling a
gap rather than transcribing.
