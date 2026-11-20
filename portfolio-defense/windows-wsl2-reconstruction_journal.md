> **Skill:** portfolio-defense / environment operations · **Source:** self (new Windows laptop rebuild) · **Mode:** Applied project (environment reconstruction) · **Date:** 2026-11-17 to 2026-11-19
> Covers: [PREPARE](./prepare/) · [CASSIA](./cassia/) · [LUCENT](./lucent/) · index: [portfolio-defense README](./README.md)
> *Artifacts: `Accounting_Meets_AI_Windows_WSL2_Technical_Reinforcement_Log_EN_2026-11-20.md`, `Windows_Development_Environment_Technical_Reinforcement_Log.docx`, PowerShell activation logs for PREPARE and CASSIA.*

# Windows 11 + WSL2 reconstruction — rebuilding the portfolio on a second OS

**Why this lives in portfolio-defense:** my primary Mac broke (10+ days at the Genius Bar), so I
rebuilt the entire Accounting-Meets-AI environment on a Windows laptop. That rebuild is itself the
strongest possible evidence for "**I can reproduce my portfolio from a cold machine**" — the exact
cold-reproducibility claim this repo is built to support. The broken Mac turned into the test.

## The one lesson that frames everything
**A `git clone` restores *source*, not *state*.** Cloning does **not** recreate: Python virtualenvs,
`node_modules`, private `.env` secrets, locally-installed skills, generated SQLite databases, or
ChromaDB vector indexes. Every one of those had to be rebuilt by hand, by layer. Knowing *which*
pieces git doesn't carry is the difference between "it won't start" and a 20-minute recovery.

## The environment, by layer
- **Three distinct Python contexts — not interchangeable:** Windows Python 3.14.10, Ubuntu (WSL2)
  Python 3.14.4, and a separate **3.13.18** for PREPARE. "Installed" ≠ "selected" ≠ "on PATH" ≠
  "the one pip is writing to."
- **WSL2 Ubuntu 26.04** as the real Linux layer; **nvm → Node 22**; **`uv`** for per-app venvs; git
  clones recovered under `~/portfolio_projects`; VS Code wired into WSL.
- **`.env` discipline confirmed, not assumed:** `git check-ignore -v .env` to *prove* the secrets
  file is gitignored before trusting it — which is also *why* the clone didn't carry it.

## Debug-by-layer wins (the defensible part)
- **Windows interpreter resolution:** `py`/`python` "not recognized" even though Python was installed
  — diagnosed with `import sys; print(sys.executable)`. The failure was **command resolution
  (PATH/alias), not a missing executable.** Separate concerns: install vs. VS Code selection vs.
  PowerShell resolution vs. pip target.
- **nbconvert WebPDF → fallback:** exporting CIS 9760 Project 1 to PDF hit a Windows `asyncio
  NotImplementedError` on the WebPDF path. Rather than treat the (successful) pip install as broken,
  I switched methods: notebook → **HTML → open in Edge → Ctrl-P → Save as PDF** → verified the
  28-page `Project_Analysis_PDF.pdf`. *A fallback that completes the real task beats fighting the
  preferred automated path.* ("installed successfully" ≠ "ran successfully" — two different claims.)
- **PREPARE — `CLINotFoundError`:** the Claude Agent SDK couldn't find the Claude Code CLI →
  `npm install -g @anthropic-ai/claude-code@latest` (v2.1.329). Then a clean run: 3 synthetic PDFs,
  type breakdowns reconciled ($4,820 / $6,947.50 / $9,025.25), ~3m28s.
- **CASSIA — 401 then rebuild:** `rag/phase1_ingest.py` first failed `openai.AuthenticationError 401`
  (bad key); with a valid key, **the partial `chroma.sqlite3` was renamed/preserved before the
  rebuild** → 5 PDFs / 172 pages → **1,015 chunks → 1,015 vectors** (collection `irs_pub15`). Also
  `sql/phase1_load.py` built 7 accounting tables (COA, GL, BS, P&L, AR, AP, Revenue).
  Version note caught: README says Python 3.13, the Dockerfile says 3.12 — a real inconsistency, not
  a mistake to paper over.
- **LUCENT:** FastAPI :8000 + Next.js :3000, 2,000 GL rows → 69 flagged.

## Development layer — fluency
- **Can now do:** stand up the full stack (WSL2, Python toolchains, `uv`, nvm/Node, VS Code) on a
  fresh OS and recover all three apps — including the *non-source state* git doesn't carry.
- **Still shaky:** nothing blocking; the per-app version pins (3.12 vs 3.13) need a written matrix.
- **Reproduce cold?** **Yes** — this *was* the cold reproduction, from a bare Windows machine.

## Defense layer — interview
- **Line:** *"When my Mac died I rebuilt all three apps on a fresh Windows + WSL2 machine. The real
  lesson: a git clone restores source, not state — it doesn't recreate virtualenvs, node_modules,
  .env secrets, SQLite databases or vector indexes, so I rebuilt each of those by layer and verified
  each one actually ran, not just installed."*
- **Follow-up risk:** "walk me through your exact PATH fix on Windows" — I diagnosed via
  `sys.executable` and fixed resolution; I'd show the log rather than recite the alias config.

## Honest limit
This is environment *operations*, not application development — it proves I can reproduce and run the
portfolio cold, not that I re-architected it. That's exactly the claim I want it to make.
