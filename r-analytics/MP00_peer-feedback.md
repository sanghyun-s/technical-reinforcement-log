> **Coursework · STA 9750 Software Tools for Reproducible Research · MP#00 + Peer Review** · Tag: 📚 **General Coursework** (with 🔁 transferable bits)
> Companion: [Lecture2-3_R-Quarto](./Lecture2-3_R-Quarto.md) · index: [coursework-README](../_legacy/coursework-README-v1.md)
> *🔁 Note: the render-snapshot ↔ Docker-image-snapshot synthesis in Part 4 is genuinely
> portfolio-relevant (the same reproducibility principle as §12.2). Original journal below,
> authored/submitted around 29 Sep 2026 — preserved verbatim.*

---

# STA 9750 — Portfolio Infrastructure: MP#00, Peer Review, and Fixing My Own Site

**Software Tools for Data Analysis · Baruch College · Prof. Michael Weylandt · Fall 2026**
Thursday section · Covers **8–29 September 2026**

Mini-Project #00 (submitted 25 Sep) · Peer Feedback #01 (submitted 29 Sep) · Site repair (29 Sep)

Live site: `sanghyun-s.github.io/STA9750-2026-FALL/`

---

## Where this sits in the course

| Component | Weight |
|---|---|
| Pre-assignments | 24% (best 8 of 10) |
| Mini-projects | 30% (best 3 of 4) |
| Course project | 46% |

**MP#00 is not graded — but it is required.** Weylandt: *"Without completing the activities
described in this section, you will not be able to submit later (graded) mini-projects, so don't
skip this!"* It's a gate, not a grade.

What *is* graded in this arc is the **meta-review** — his assessment of the quality of the peer
feedback I wrote, returned 11 October.

Also worth recording his framing of why MP#00 is deliberately fiddly:

> *"This project is designed to try to have you encounter as many potential issues as possible.
> It is better to solve these issues now (early in the semester, without a grade on the line)
> than later with deadlines fast approaching. 'Fail fast' is good advice in programming and in
> life in general."*

---

## Part 1 — Mini-Project #00

Not a data analysis. The deliverable was a working pipeline: a local RStudio project wired to
GitHub, a Quarto website, Pages deployment, and placeholder pages for everything that comes later.

### The seven stages

| Stage | What it does |
|---|---|
| 0 | Install `git` |
| 1 | GitHub account |
| 2 | Course repo from Weylandt's template, issues disabled, PAT created |
| 3 | Personal website — `_quarto.yml`, `index.qmd`, build script |
| 4 | Getting content to GitHub — add, commit, push |
| 5 | GitHub Pages deployment, serving from `docs/` |
| 6 | Placeholder pages for MP#01–#04 |
| 7 | Submission — Teams message, GitHub issue, PDF to Brightspace |

**Stage 7 is three separate actions and all three are required.** The Teams message is the
long-lead item, because it's the one that depends on a human: he processes it and then issues the
private passcode used for anonymous peer feedback. Nothing downstream works without it.

### What the repo history shows

```
Initial commit                                   3 days ago
Build initial Quarto website                     3 days ago
Create mini project files                        3 days ago
Add navbar links, expand homepage, fix date...   now
```

Four commits. The first three are MP#00; the fourth is this journal's Part 3.

### What carried over from Lab 2

Lab 2 was the rehearsal and it paid off. Already understood before starting:

- Quarto renders, chunk options, `echo: false`
- Source mode vs Visual mode, and why Visual is discouraged here
- `.gitignore`, and the `_` prefix convention that hides directories from Quarto
- Which pane does what — Console vs Terminal vs Source
- git name and email configured

New in MP#00: `_quarto.yml`, the GitHub PAT, pushing to a remote, and Pages deployment.

**The ratio from Lab 2 held.** The Quarto work was quick; environment and file organisation took
the time.

### Naming the local project to match the remote

I first created the local RStudio project as `STA9750-2026-FALL-PROJECT` against a remote called
`STA9750-2026-FALL`. Git allows that — the local folder name and the remote repository name are
genuinely independent — but I restarted the setup so the two matched exactly.

Worth the redo. Every later instruction, every path in the assignment, and every URL assumes one
name. A mismatch costs a small translation every single time.

Verified the connection from the **Terminal**:

```
pwd
git remote -v
git status
```

`main` branch, correct remote. Three commands, and they answer "am I where I think I am, pointed
at what I think I'm pointed at."

### Authentication: a PAT is not a password

`usethis::git_sitrep()` in the **Console** reports the whole picture at once — git installation,
GitHub username, Personal Access Token, project directory, config.

The distinction that matters: **a GitHub account password and a Personal Access Token are
different things.** The password logs you into the website; the PAT is what a specific computer
uses to push. When `gitcreds::gitcreds_set()` found an existing credential, I kept it rather than
replacing it for no reason.

And the real test of authentication isn't what `git_sitrep()` prints — **it's a successful push.**
Everything before that is a report; the push is the experiment.

### `.gitignore`, and confirming a file is actually saved

RStudio generates a basic `.gitignore`; the course requires more — generated files, datasets,
Quarto cache, PDFs, spreadsheets. I kept RStudio's entries and added the course rules rather than
overwriting.

Then verified from the Terminal:

```
cat .gitignore
```

> **Editing a file in RStudio is not the same as confirming the file on disk contains what you
> think.** Reading it back is a different act from writing it. This is the same instinct as
> checking `docker ps` after a `docker run` — trust the system's report, not your memory of what
> you did.

### The first full workflow

Staged three files through the Git pane:

```
.gitignore
README
STA9750-2026-FALL.Rproj
```

Then **Stage → Commit → Push**, and refreshed GitHub to see them land. That refresh was the first
proof that RStudio → Git → GitHub worked end to end. Everything after it was addition rather than
construction.

### The Quarto website

`_quarto.yml` declared the project a website, set the output directory to `docs`, chose the
`sandstone` theme, and recorded the Pages URL. `index.qmd` held a short introduction and an inline
R timestamp.

**Syntax lesson:** an R chunk has to be properly opened *and closed* with triple backticks before
ordinary Markdown or inline R can resume. An unclosed chunk swallows everything after it.

### Source versus generated output

```
index.qmd
    ↓  Render
docs/index.html
```

The `.qmd` is the editable source; the HTML under `docs/` is what GitHub Pages actually serves.
**Both have to be pushed**, because this course deploys from the `/docs` folder rather than
building on the server.

This is the same idea as a Docker image being a snapshot of your files at build time — and it
returns in Part 4, where rendering one document failed to update four others.

### Pages deployment

```
Repository → Settings → Pages → Deploy from a branch → main → /docs
```

And the site went live at `https://sanghyun-s.github.io/STA9750-2026-FALL/` — the first time a
document on my laptop became a public web page.

### Placeholders for MP#01–#04

An R loop created and rendered all four:

```
mp01.qmd → docs/mp01.html
mp02.qmd → docs/mp02.html
mp03.qmd → docs/mp03.html
mp04.qmd → docs/mp04.html
```

Staged, committed, pushed the same way. Future projects replace the placeholder content and inherit
the entire deployment structure unchanged.

### Submission — and the mistake the verifier caught

Three separate actions, all required: **Teams identification**, **GitHub Issue**, **Brightspace
PDF**.

I filed the GitHub Issue with the *repository* URL instead of the *Pages* URL. The course helper
caught it precisely:

```
Expected:   https://sanghyun-s.github.io/STA9750-2026-FALL/
Submitted:  https://github.com/sanghyun-s/STA9750-2026-FALL
```

Two URLs one character apart in spirit and completely different in function: one is where the code
lives, the other is where the website lives. He asks for the website, because the website is the
deliverable.

Corrected the issue, then:

```r
source("https://michael-weylandt.com/STA9750/load_helpers.R")
mp_submission_verify(0, "sanghyun-s")
```

```
Congratulations! Your mini-project appears to have been submitted correctly!
```

**Run the verifier.** It exists precisely because this submission has three moving parts and a
silent failure in any of them means graded work later can't be collected.

Then exported the live Pages homepage to PDF via the browser and submitted that to Brightspace —
the same print-to-PDF step rehearsed accidentally during Lab 2.

### The pipeline, end to end

```
RStudio project
    ↓
source files (.qmd / .yml)
    ↓  stage → commit → push
GitHub repository
    ↓  Quarto render → docs/
GitHub Pages
    ↓
public website
```

Three distinctions this made concrete: **Terminal vs R Console**, **Git authentication vs GitHub
login**, and **source files vs rendered deployment files**. All three come back in Part 4.

---

## Part 2 — The peer feedback cycle

### How it actually runs

Peer feedback isn't a Brightspace form. It runs through a course helper package, from the **R
Console**:

```r
source("https://michael-weylandt.com/STA9750/load_helpers.R")
mp_pf_perform(N = 0)
```

`N = 0` is the mini-project number. The same file provides `mp_submission_verify(0, "github_id")`,
used at submission time.

**`source()` loads into the Console session only.** Restart R and the functions are gone — you
re-source. Exactly the Part 6 rule from Lab 3 in different clothes: sessions are isolated, and
nothing you load in one is visible to another.

Loading pulls in `gitcreds`, `gh`, `tidyverse`, `rvest`, `glue`, `httr2` — so the tool talks to
GitHub's API and scrapes rendered pages. The tidyverse conflicts message (`dplyr::filter()` masks
`stats::filter()`) appears on load and is exactly the example PA #04 walks through. Message, not
error.

### The flow

1. GitHub ID, confirmed
2. Secret phrase, confirmed — case-sensitive, and it's what makes the feedback anonymous
3. It reports whether the submission passed automated checks
4. Offers to open: the MP instructions, the rendered HTML, the source `.qmd`, the GitHub issue
5. Then a sequence of free-text prompts:
   - one notable strength
   - any other strengths (repeats until blank)
   - one area for improvement
   - any other areas (repeats until blank)
   - concrete steps to improve
   - any additional advice
6. Moves straight on to the next assigned review

*"No rubric found for MP#00, so only requesting overall comments"* is correct, not a fault — MP#00
has no analysis to score. Later mini-projects will.

### What "passed all automated checks" actually means

**Plumbing only.** Repo exists, Pages deployed, issue filed correctly. It says nothing about
whether the site is any good. The automated check covers precisely what a script can verify —
which is the entire reason a human is in the loop.

### The meta-review rubric, read closely

| Band | Distinguished by |
|---|---|
| 18–20 | strengths accurately described **+ all weaknesses, major *and minor*** |
| 15–17 | major strengths and weaknesses only |
| 12–14 | **"overlook one or more minor weaknesses"** |
| 9–11 | brief, valid, superficial |
| 5 | scores only, no comments |
| TBD | **"Highly-Detailed Inaccurate Feedback"** — case by case |

Three things fall out of that table.

**The minor findings are the whole difference.** Catching only the big things caps you at 17.
Missing something small drops you to 14. On strong work, the small observation *is* the assignment.

**"Accurately describes strengths" — not "praises."** Naming the technique (*they used inline R to
generate the timestamp*) shows comprehension; "nice touch with the date" shows only that you saw it.

**He built a band for confident wrongness and declined to score it.** So don't speculate. A
precise small finding beats a speculative large one, and hedging ("if that's a `format()` call")
costs nothing.

And the explicit asymmetry: *"students need more detailed feedback on poor work — giving them an
opportunity to improve — than on strong work."* Strong submissions warrant accuracy, not volume.

---

## Part 3 — Review #01, and what it did to me

### The submission

A genuinely strong MP#00. Three things worth naming precisely:

- **Inline R generating the "Last Updated" line** — the technique PA #04 points at when it says to
  read his source and see *"how I included it in the rendered text."* Most people don't reach for
  it until MP#01
- **An interactive Leaflet map** — required by nothing in MP#00
- **A real bio** — named employers, a certification, an actual description of the work. Most
  homepages in the cohort still carry template text

### The finding

```
Last Updated: Tuesday 09 22, 2026 at 16:37PM
```

Two separate bugs sitting together:

- **`16:37PM`** — a 24-hour clock wearing a 12-hour suffix. `%H` paired with `%p`; `%I` is the
  token that belongs with `%p`
- **`09 22`** — a month *number* immediately after a weekday *name*. `%m` where `%B` was intended

Cosmetic, two characters, and invisible to any automated check. Which is what made it ideal: the
exact class of minor finding the 18–20 band is defined by.

### Then I looked at my own site

```
Ira:  Tuesday 09 22, 2026 at 16:37PM
Me:   Friday  09 25, 2026 at 17:16PM
```

**Identical bug.** Neither of us wrote it — it came from the template in Weylandt's own MP#00
instructions, so it's almost certainly on every site in the cohort.

That changed the feedback for the better. Saying *"this comes from the course template rather than
anything you did — it's on my site too"* is accurate, generous, and demonstrates understanding the
cause rather than spotting a symptom.

And it's the assignment's stated purpose, firing on the first review:

> *"In doing so, you will learn to evaluate data science work product and will develop a critical
> eye that can be turned to your own work."*

**I had diagnosed this correctly for someone else thirty minutes before failing to see it on my own
page.** Not carelessness — my own page reads as *familiar* rather than as *text*, and familiar
things don't get read. Worth remembering before proofreading my own mini-projects in October.

---

## Part 4 — Fixing my own site

Three faults, found by reviewing someone else's.

### 1. The navbar had no links in it

```yaml
  navbar:
    background: primary
    search: false
```

That's the entire block as it shipped. It sets a colour and disables search and says nothing about
navigation — so Quarto rendered a bar with a title and nothing else. `mp01.html` existed and was
unreachable by clicking.

Fixed by adding a `left:` list:

```yaml
  navbar:
    background: primary
    search: false
    left:
      - href: index.qmd
        text: Home
      - href: mp01.qmd
        text: "Mini-Project #01"
      - href: mp02.qmd
        text: "Mini-Project #02"
      - href: mp03.qmd
        text: "Mini-Project #03"
      - href: mp04.qmd
        text: "Mini-Project #04"
```

**Two YAML traps in that block.**

Indentation is structural and must be spaces, never tabs: `navbar:` at 2, its keys at 4, list items
at 6, their keys at 8.

And **`#` starts a comment in YAML.** Unquoted, `text: Mini-Project #01` yields a link labelled
`Mini-Project` with `#01` silently discarded. Every label containing `#` needs quotes.

### 2. The timestamp

```r
`r format(Sys.time(), "%A %m %d, %Y at %H:%M%p")`   # before
`r format(Sys.time(), "%A %B %d, %Y at %I:%M %p")`  # after
```

### 3. The homepage was two sentences

MP#00's stated goal: *"a secondary goal of this course is to help students build a web-presence and
a data science portfolio, giving you a place to showcase your skills to potential employers."*

Two sentences saying "I am a Master's student" doesn't serve that, and by then I had material —
Quarto, dplyr, a containerised API application on AWS, a project plan built around hypothesis
testing. None of it was on the page. Replaced with three short paragraphs naming the tools and the
interest.

### The render trap

After editing `_quarto.yml` and `index.qmd`, I rendered `index.qmd` and looked at the Git pane:

```
M  _quarto.yml
M  index.qmd
M  docs/index.html
M  docs/sitemap.xml
```

`docs/mp01.html` through `mp04.html` were **absent**. Rendering one document rebuilds one page —
but the navbar lives in the *config* and belongs on **every** page. The homepage would have had
five links and the four project pages none.

**A configuration change requires a full-site render**, not a single-document one: **Build → Render
Website**, or `quarto render` in the Terminal.

```
[1/5] index.qmd
[2/5] mp04.qmd
[3/5] mp03.qmd
[4/5] mp02.qmd
[5/5] mp01.qmd
```

After that, all four `docs/mp0*.html` appeared as modified — which was the confirmation the config
had reached every page.

**This is the same shape as the Docker stale-image trap from CIS 9760 the same week:** the artefact
is a snapshot, and changing the recipe doesn't update snapshots already made. Two courses, two
toolchains, one idea.

### And `docs/` is what actually ships

GitHub Pages serves from `docs/`. Commit the source files only and push, and the live site doesn't
change at all — the `.qmd` edits are invisible to it.

```
git add .      # everything: sources AND docs/
git commit -m "Add navbar links, expand homepage, fix date format"
git push
```

Push confirmed `db36b80..f5a7779`.

---

## Concepts worth keeping

**`source()` is session-scoped**, like `library()`. Restart R and you re-source. The pattern is now
familiar from three directions: Lab 3's Render-session lesson, `library()` in a `.qmd` chunk, and
this.

**Rendered output is a snapshot.** Change the config, rebuild everything. Change the recipe, rebake
the cake.

**Three copies of every page exist** — the `.qmd` source, the rendered `docs/` HTML, and what
GitHub serves — and they drift apart unless you render *and* push.

**`#` is a YAML comment.** Quote any string containing one.

**`%B` vs `%m`, `%I` vs `%H`.** `%p` requires a 12-hour clock to mean anything.

**A PAT is not a password.** One logs you into a website; the other authorises a machine to push.
And the real test of authentication is a successful push, not a status report.

**Editing a file is not saving it, and saving is not verifying.** `cat` it back. The same instinct
as `docker ps` after `docker run`.

**Run the verifier.** `mp_submission_verify()` caught a repository URL where a Pages URL belonged —
a mistake that would have sat silently until it blocked a graded submission.

**Automated checks verify plumbing, not quality.** Whatever a script can check is the floor, not
the bar. The verifier confirms the URL is the right *kind* of URL; it cannot tell you the page is
worth reading.

**Reviewing someone else's work is the cheapest way to find faults in your own.** Three of the four
things I fixed tonight were found by reading a classmate's page.

**You cannot proofread a page you wrote by reading it.** Familiarity defeats attention. Compare
against something, or check against a spec — don't just look.

---

## Open items

- [ ] **Peer review #02** (Issue #57, e2gar) — due Sun 5 Oct
- [ ] **Project proposal presentation** — Thu 1 Oct, 6:00 PM; PDF to Brightspace *before* class
- [ ] PA #04 — **no journal written**; the dplyr verbs are the one gap in this semester's notes
- [ ] Meta-review returned 11 Oct — the graded outcome of the feedback written here

---

## Reference — dates in this cycle

| | |
|---|---|
| MP#00 released | 8 Sep |
| MP#00 submitted | 25 Sep |
| Peer feedback assigned | 28 Sep |
| Review #01 submitted | 29 Sep |
| Site repaired and pushed | 29 Sep |
| Peer feedback due | 5 Oct |
| MP#00 comments returned | 2 Oct |
| **Meta-review returned** | **11 Oct** |
