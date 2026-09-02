# CLAUDE.MD -- Tanzania M&A Policy Reforms Project (FCC Regulatory & Benchmarking Review)

<!-- HOW TO USE: Keep this file under ~150 lines — Claude loads it every session.
     See the guide at docs/workflow-guide.html for full documentation. -->

**Project:** Merger & Acquisition (M&A) Policy Reforms — Implications for Tanzania
**Institution:** External consulting/research engagement for the Fair Competition Commission (FCC), Tanzania
**Branch:** main

---

## Scope Discipline

**Do exactly what was asked — nothing adjacent.** Do not add README files, build scripts,
`.gitignore` edits, helper utilities, or extra tooling that was not requested. If an addition
looks valuable, **list it as a suggestion at the end** and let the user decide.

A request for a codebook is a request for a codebook. Delivering a codebook plus a README plus
build scripts plus gitignore edits means the user now has to review four things to accept one,
and the usual outcome is that all four get thrown away.

**Before adding anything not named in the request, ask.** One line is cheaper than a revert.

---

## Core Principles

- **Plan first** -- enter plan mode before non-trivial tasks; save plans to `quality_reports/plans/`
- **Verify after** -- run/check Stata output (or confirm manually if `stata-mcp` isn't installed) and confirm figures/tables at the end of every task
- **Single source of truth** -- Stata `.do` files (via `esttab` / `graph export`) are authoritative for every number, table, and figure; the Word report cites them, never hand-typed values
- **Quality gates** -- nothing ships below 80/100
- **[LEARN] tags** -- when corrected, save `[LEARN:category] wrong → right` to [MEMORY.md](MEMORY.md)

Cross-session context lives in [MEMORY.md](MEMORY.md); past plans, specs, and session logs are in [quality_reports/](quality_reports/).

**How we verify** — the references and rules that carry the verification discipline:

- [`verification-ladder.md`](.claude/references/verification-ladder.md) — the seven rungs, from *qualify the checker* to the external oracle, and how the review loop converges.
- [`external-oracle-process.md`](.claude/references/external-oracle-process.md) — running an independent frontier-model referee (Claude Code → GPT-5.6 Sol Pro) and adjudicating what it returns.
- [`provenance-and-ground-truth.md`](.claude/references/provenance-and-ground-truth.md) — naming and pinning your oracles, classifying divergence, and the clean-room boundary.
- [`review-fencing.md`](.claude/rules/review-fencing.md) — reviewer independence is a property of the environment, not an instruction: a neutral copy outside the checkout, prior verdicts withheld, and the answer keys the repo already commits fenced off.
- [`release-engineering.md`](.claude/references/release-engineering.md) — shipping research software: message and silent-resolution censuses, frozen feature matrices for ports, hash-claimed inherited tests, and downstream consumers pinned by commit SHA.

**How we write** — [`writing-with-ai.md`](.claude/rules/writing-with-ai.md): internal vs external-facing documents, why a model cannot make its own output stop reading as model output, and the human-readable standard for anything with your name on it.

**Theory work** — [`theory-proving.md`](.claude/references/theory-proving.md): proof contracts, portfolio search with isolated explorers, counterexample-hunting your own lemmas, adversarial audits, and the rule that an AI-generated proof is a claim, not a theorem.

**The laws** — [`research-agent-laws.md`](.claude/references/research-agent-laws.md): 21 laws for running agents on research infrastructure, each paid for by a real incident.

**How we remember** — the record lives in the repo, not the transcript:

- [`progress-reports.md`](.claude/rules/progress-reports.md) — GitHub issues as defect memory, `quality_reports/` as work memory, `MEMORY.md` as lesson memory.
- [`issue-ledger.md`](.claude/rules/issue-ledger.md) — the evidence standard an issue must meet, and the seven-section closure comment.
- [`repo-hygiene.md`](.claude/rules/repo-hygiene.md) — **scratch must not become main.** Enforced by `check-repo-hygiene.py` on every commit.

Nothing clears work until it has a row in [`quality_reports/qualification/LEDGER.md`](quality_reports/qualification/LEDGER.md) — run [`/vaccinate`](.claude/skills/vaccinate/SKILL.md) to put one there.

---

## Folder Structure

```
tanzania-ma-policy/
├── CLAUDE.MD                    # This file
├── .claude/                     # Rules, skills, agents, hooks
├── Report/                      # Word master document + section drafts (primary deliverable)
├── Data/
│   ├── raw/                     # Confidential/restricted FCC source data — NEVER committed (see confidential-data.md)
│   └── clean/                   # Derived, disclosure-cleared datasets
├── Figures/                     # Stata graph export targets (figures for the report)
├── scripts/
│   └── stata/                   # Numbered .do pipeline (see stata-code-conventions.md)
│       └── _outputs/            # esttab tables, logs, sessionInfo.txt, exported figures
├── quality_reports/             # Plans, session logs, merge reports, decision records
├── explorations/                # Research sandbox (see rules)
├── templates/                   # Session log, quality report templates
└── master_supporting_docs/      # FCC benchmarking materials, prior reviews, comparator-jurisdiction papers
```

**Present but inactive for this project** (kept as template scaffolding, not deleted):
`Slides/`, `Quarto/`, `Preambles/`, `Bibliography_base.bib`, `docs/` — these support a Beamer/Quarto
lecture-slide workflow this project doesn't use. Left in place per owner decision (2026-09-02);
revisit only if a policymaker slide deck is later commissioned.

---

## Commands

```bash
# Stata: single-command reproduction (numbered pipeline, see stata-code-conventions.md)
do scripts/stata/99_run_all.do          # from repo root, runs 01..0N in order

# Stata: one stage at a time
do scripts/stata/01_clean.do

# Backtest: is the repo internally consistent and currently true?
# (surface-sync + skill-integrity + model-versions + links + spec-conformance + staleness + repo-hygiene + derived-counts + ledger-coverage + hook-battery)
# Run this after ANY change. Also runs in pre-commit and CI.
./scripts/backtest.sh
```

**Note:** `scripts/quality_score.py` and `scripts/check-palette-sync.sh` are Quarto/Beamer-specific
and do not run against this project's artifacts (Stata `.do`, Word `.docx`) — inactive, not deleted.

**Note:** by default Claude Code can write/edit `.do` files but cannot execute them — no Stata MCP
server is installed. Verification of Stata output currently requires you to run the script and report
back, or paste the output. See the suggestions list in
[`quality_reports/plans/2026-09-02_adapt-config-for-ma-tanzania-project.md`](quality_reports/plans/2026-09-02_adapt-config-for-ma-tanzania-project.md)
if you'd like `stata-mcp` installed so Claude can run/verify Stata output directly.

---

## Quality Thresholds (advisory)

| Score | Checkpoint | Meaning |
|-------|------|---------|
| 80 | Commit | Good enough to save |
| 90 | PR | Ready for deployment |
| 95 | Excellence | Aspirational |

Enforced by `/commit` (halts + asks for override) **and** — once you run `./scripts/install-hooks.sh` — by a real git pre-commit hook (`.githooks/pre-commit`) that runs the full backtest gate suite plus the quality (≥80) gate on every commit. Bypass sparingly with `SKIP_QUALITY_GATE=1` or `--no-verify`.

---

## Skills Quick Reference

The full table of all skills lives in [README.md](README.md#skills-claudeskills). Most-used, by this
project's workflow:

- **Data / modelling (Stata):** `/stata-replication` `/data-analysis` `/audit-reproducibility` `/diagnose` `/capture-environment`
- **Confidentiality / disclosure:** `/disclosure-check` `/data-management-plan`
- **Report / review:** `/review-paper` `/proofread` `/humanize` `/verify-claims` `/credible-claims`
- **Research:** `/lit-review` `/interview-me` `/research-ideation`
- **Verification / rigor:** `/challenge` `/oracle-review` `/adjudicate-review` `/differential-audit` `/blast-radius` `/verify-artifact` `/deep-audit`
- **Meta / workflow:** `/commit` `/learn` `/checkpoint` `/context-status` `/triage-inbox`

Lecture/slide skills (`/create-lecture`, `/compile-latex`, `/deploy`, `/qa-quarto`,
`/slide-excellence`, `/translate-to-quarto`, `/syllabus`, `/teach-from-paper`, `/scaffold-exercises`)
remain installed but aren't part of this project's workflow — see the README for the complete index.

---

## Report & Analysis Conventions

Not applicable: this project has no Beamer/Quarto deck, so the environment/CSS-class tables the
template ships here are removed rather than left as unfillable placeholders. The conventions that
actually govern this project's generated artifacts (do-file header scaffolding, table/figure output
naming, `esttab` conventions) live in
[`.claude/rules/stata-code-conventions.md`](.claude/rules/stata-code-conventions.md).

---

## Current Project State

| Component | Status | Key Content |
| --- | --- | --- |
| Regulatory & Benchmarking Review | Not started | Comparative review of M&A regulatory regimes; benchmarking Tanzania's FCC framework against comparator jurisdictions |
| Economic Modelling | Not started | Stata modelling of the effects of policy, legal, and administrative reform options |
| Final Report (Word) | Not started | Combined analytical + modelling findings; evidence-based recommendations for FCC policymakers |

*(Source materials pending — this table gets filled in with real sections and dates once
`master_supporting_docs/` receives the FCC's existing materials.)*
