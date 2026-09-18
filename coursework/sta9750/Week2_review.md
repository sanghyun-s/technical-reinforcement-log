# STA 9750 — Week 2 Review Note

**Lecture #02: Tools for Reproducible Research** · Thursday section, 10 Sep 2026
Slides: <https://michael-weylandt.com/STA9750/slides/slides02.html>

*Reading time ~25 minutes. This is the lecture you missed, reorganised for catch-up rather than for delivery.*

---

## The one idea underneath all three topics

Everything in Week 2 serves a single goal: **your analysis and your write-up should never be two
separate things you keep in sync by hand.**

The failure mode it replaces is familiar — run the analysis, screenshot the chart, paste it into Word,
then change the data and have to redo all of it, hoping you caught every number in the text. Markdown,
Quarto and git each remove one part of that problem:

| Tool | What it removes |
|---|---|
| **Markdown** | Formatting locked inside proprietary software |
| **Quarto** | Copy-and-paste between code and document |
| **git / GitHub** | "final_v2_REALLY_final.docx" |

Hold onto that framing. Every mini-project and both final reports are submitted this way.

---

## 1. Markdown

### What it is

A **light-weight, plain-text markup language**. Three words, each doing work:

- **Light-weight** — simple, focused on content over formatting
- **Plain-text** — readable in any editor; you are not locked into one program
- **Markup language** — a mini-language for describing text structure, like HTML or XML but far smaller

It's standardised as **CommonMark**, though many tools add extensions.

### The syntax you need

```markdown
I can write in *italics* or **bold**.

Lists are easy:
1. First
1. Second
1. Third

as are links to [helpful info](https://commonmark.org).
```

Note the numbered list uses `1.` three times — the renderer numbers them correctly. That means you can
reorder items without renumbering by hand.

**Links and images** differ by one character:

```markdown
[link text](https://the.url/goes/here)
![Image Caption](https://the.url/goes/here.png)
```

The leading `!` means *embed the thing* rather than *link to it*. You can nest them for a clickable image:

```markdown
[![Elephant](elephant.png)](https://en.wikipedia.org/wiki/Elephant)
```

**Headers** use `#`, and the level matters more than you'd think — in slides, `#` makes a section-divider
slide and `##` makes an ordinary slide. You'll use this in Lab 2.

**Tables** have two syntaxes: a simple one with little control, and pipe tables for alignment and widths.
For results tables in mini-projects you'll use the **`gt`** R package instead, starting with MP#01.

### WYSIWYM, not WYSIWYG

This distinction came up repeatedly and is worth internalising.

- **WYSIWYG** — *What You See Is What You Get.* Image editors, Squarespace, Word as most people use it. You manipulate appearance directly.
- **WYSIWYM** — *What You See Is What You **Mean**.* You write `**bold**` to declare intent; something else decides how bold looks.

The payoff is that one source file can render to HTML, PDF, Word, slides, or an ebook without rewriting.
The renderer doing that conversion is usually **Pandoc**, working behind the scenes.

### Where it's used

Markdown is everywhere now — GitHub, Reddit, Stack Exchange, Discord, MS Teams, OpenStreetMap,
Bitbucket, Drupal, and ChatGPT's own output. Learning it is not a course-specific skill.

---

## 2. Quarto

**Quarto is Markdown with data-analytic extensions.** The addition that matters: you embed code inside
the document and its output appears in the rendered page.

````markdown
```{r}
library(tidyverse)
ggplot(txhousing, aes(x=ym(year, month, 1), y=sales, color=city)) +
    geom_point() +
    geom_smooth() +
    xlab("Date") +
    ylab("Number of Sales")
```
````

Change the code, re-render, and the figure changes. Quarto handles creating the image file and linking
to it. No copy-and-paste, and no possibility of the chart and the data drifting apart.

### How it becomes a website

- Each `.qmd` file renders to one `.html` page
- All the files in a directory together make a site
- Your GitHub project becomes that website — which is precisely what MP#00 builds

### A code block is not an indented list

He was asked this directly. Indentation in a nested list is *just text*. A code block, written with
triple backticks and `{r}`, is a signal to the rendering engine that it should **execute** the contents.
Different thing entirely.

### Resources he recommends

- [Quarto user guide](https://quarto.org/docs/guide/) — he calls it fantastic
- The [course repo source](https://github.com/michaelweylandt/STA9750) — read `syllabus.qmd` for real table examples
- [RStudio cheat sheet](https://rstudio.github.io/cheatsheets/rstudio-ide.pdf) and [Quarto cheat sheet](https://rstudio.github.io/cheatsheets/quarto.pdf)

---

## 3. Git and GitHub

### What version control is for

Git saves versions of your work and lets you return to any of them. Two things distinguish it from
Dropbox:

- **It is not automatic.** You save a version when you *decide* to. That intentionality is the point — each saved version means something.
- **A commit is both a change and a state.** It records what changed *and* is a complete snapshot of everything up to that moment.

A branch name like `main` is just a pointer to "the latest commit." This course won't use branches.

### The box model

His mental model, and a good one:

| Command | The metaphor | In RStudio |
|---|---|---|
| `git add` | put changes in the box | tick the checkbox in the Git pane |
| `git commit` | seal the box and write the label | Commit button (twice) |
| `git push` | ship the box to GitHub | green up arrow |

### Reading the Git pane

- **Yellow `?`** — git has never seen this file
- **Blue `M`** — modified since the last commit
- After staging, the marker moves to the first column
- After a successful push, the file disappears from the pane — nothing left to save

### Two rules he stated as warnings

> **Never make edits on the GitHub website.** "This will cause you significant pain."

> **Push from one computer only.** Moving changes up *and* down between machines is a much more
> complex workflow than this course covers.

Changes flow one direction: your computer → GitHub.

### GitHub Pages

Free hosting for **static** sites — no logins, no server-side code, limited JavaScript. Fine for
documents, and it's how every submission in this course is published.

Default URL shape:

```
https://YOUR_GITHUB_ID.github.io/YOUR_REPO_NAME
```

One trick worth knowing: if you name a repo `YOUR_GITHUB_ID.github.io`, the URL collapses to just
`https://YOUR_GITHUB_ID.github.io/`. Not needed for this course, but useful for a personal site later.

### On placeholders

Where instructions say `YOUR_GITHUB_ID`, substitute the whole string, no brackets. The submission
automation reads these literally, so formatting is not cosmetic here.

---

## 4. How to ask for help

He spent real time on this, which suggests he'll reward it. His framing: **professional programming is
at least half looking things up**, and more than half when you're starting.

**1. Official documentation first.** Free software rarely becomes popular without good docs. R and the
tidyverse have excellent ones.

**2. Search engines, used well.**

- Include `R` or `rstats` in the query — otherwise you get results for the letter R
- **Search for what you want to do, not how you think it should be done.** If you search your assumed solution, you only find confirmation of your assumption
- Stack Overflow for specific code errors; blogs and course material for conceptual questions
- The same advice applies to asking an LLM

**3. Ask on a forum with a minimal reproducible example.**

- **Minimal** — the fewest lines that still show the problem
- **Reproducible** — self-contained, all packages loaded, standard data

> His best line: **you'll solve over half your problems just building the reproducible example.** The
> act of stripping a problem to its core usually reveals the cause.

Practical tips: show the code even when broken, send it as **text not a screenshot** so someone can run
it, avoid file input/output unless that's the actual problem. The `reprex` package automates this.

For this course, the discussion board is the first stop.

---

## 5. Logistics from the slides

Course scale: **87 students**, roughly **17 project teams** of 4–6, **3–4 mini-projects to peer-review
per cycle**.

**Your deadlines — Thursday section:**

| Date | Item |
|---|---|
| Thu 17 Sep, 6:00pm | **Pre-Assignment #03** |
| Thu 24 Sep, 6:00pm | Pre-Assignment #04 |
| Thu 24 Sep, 11:59pm | **Team roster** |
| Fri 25 Sep, 11:59pm | **Mini-Project #00** |
| Thu 1 Oct, 6:00pm | Team contract + proposal slides |
| Mon 5 Oct, 11:59pm | MP#00 peer feedback |
| Thu 8 Oct, 6:00pm | Pre-Assignment #05 |

Other notes worth acting on:

- **The course project description is now online** — structure, deadlines, rubrics. Read it before choosing teammates.
- **A special session on finding good data sources** is coming. Relevant to your project search.
- **Pre-assignments release after 9:00pm** the night of class, on the website and Brightspace.
- **Brightspace now only re-shows questions you got wrong** on retakes — so retaking is cheap.
- **Check Brightspace "feedback"** if you're alerted; he sometimes leaves comments there.
- **Optional DataCamp:** "Reporting with R Markdown." R Markdown is proto-Quarto — similar, not identical.

### His teammate-selection tip

> Before committing to a team with someone, you can look up their GitHub and see how they did MP#00
> (how early, how technical). This might be helpful to find teammates whose work patterns are
> calibrated to your own.

Every MP#00 submission is a public issue in his repo. That's a roster of who finished, when, and how
well — available to you, and to everyone looking at you.

---

## Checklist before Week 3

- [ ] Syllabus quiz submitted (it gates every other graded submission)
- [ ] Registered on the discussion board (MS Teams)
- [ ] Quarto installed and a test document renders
- [ ] RStudio set to **Source** mode, not Visual
- [ ] Pre-Assignment #03 done — **Thursday 6:00pm**
- [ ] Lab #2 attempted
- [ ] GitHub account created and Teams message sent
