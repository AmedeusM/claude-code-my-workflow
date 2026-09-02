---
paths:
  - "Slides/**/*.tex"
  - "Quarto/**/*.qmd"
  - "scripts/**/*.R"
  - "scripts/stata/**/*.do"
---

# Quality Review & Scoring Rubrics

> **Framing:** Thresholds are **advisory at the harness level**. The `/commit` skill runs `quality_score.py` and halts on failure until the user fixes or explicitly overrides. A **real git pre-commit hook** (`.githooks/pre-commit`, activated once via `./scripts/install-hooks.sh`) extends the same gates to *every* commit, so bypassing the skill no longer bypasses the review — unless you opt out with `SKIP_QUALITY_GATE=1` / `--no-verify`. "Gate" here means "checkpoint enforced by a skill or the pre-commit hook," not an unconditional harness-level block.

## Thresholds

- **80/100 = Commit** -- good enough to save
- **90/100 = PR** -- ready for deployment
- **95/100 = Excellence** -- aspirational

## Quarto Slides (.qmd)

| Severity | Issue | Deduction |
|----------|-------|-----------|
| Critical | Compilation failure | -100 |
| Critical | Equation overflow | -20 |
| Critical | Broken citation | -15 |
| Critical | Typo in equation | -10 |
| Major | Text overflow | -5 |
| Major | TikZ label overlap | -5 |
| Major | Notation inconsistency | -3 |
| Minor | Font size reduction | -1 per slide |
| Minor | Long lines (>100 chars) | -1 (EXCEPT documented math formulas) |

## R Scripts (.R)

| Severity | Issue | Deduction |
|----------|-------|-----------|
| Critical | Syntax errors | -100 |
| Critical | Domain-specific bugs | -30 |
| Critical | Hardcoded absolute paths | -20 |
| Major | Missing set.seed() | -10 |
| Major | Missing figure generation | -5 |

## Stata Do-files (.do)

Mirrors the discipline mandated by [`stata-code-conventions.md`](stata-code-conventions.md).

| Severity | Issue | Deduction |
|----------|-------|-----------|
| Critical | Syntax errors / script does not run cleanly | -100 |
| Critical | Hardcoded absolute paths (not repo-root-relative) | -20 |
| Critical | `merge` without `assert()` (silent key mismatch) | -15 |
| Major | Missing `set seed` / `set sortseed` where randomization or sorting affects output | -10 |
| Major | Missing reproducibility header (version pin, `clear all`, log) | -5 |
| Major | Hand-typed table/figure value not produced by `esttab` / `graph export` | -10 |
| Minor | Missing `sessionInfo.txt` capture | -3 |
| Minor | Not numbered / not wired into `99_run_all.do` | -2 |

## Beamer Slides (.tex)

| Severity | Issue | Deduction |
|----------|-------|-----------|
| Critical | XeLaTeX compilation failure | -100 |
| Critical | Undefined citation | -15 |
| Critical | Overfull hbox > 10pt | -10 |

## Enforcement (the /commit skill + an optional pre-commit hook)

- **Score < 80:** Halt within `/commit`. List blocking issues. User may override with an explicit natural-language signal ("commit anyway" / "skip quality gate") and a reason — the override is logged in the commit body.
- **Score < 90:** Allow commit within `/commit`, warn. List recommendations.
- **Direct `git commit`:** unenforced *until* you run `./scripts/install-hooks.sh`, which points `core.hooksPath` at the version-controlled `.githooks/pre-commit`. After that, every commit (skill or not) runs the full backtest gate suite plus the quality (≥80) gate. Bypass sparingly with `SKIP_QUALITY_GATE=1` (quality only) or `git commit --no-verify` (all hooks); record the reason in the commit body.

## Quality Reports

Generated **only at merge time**. Use `templates/quality-report.md` for format.
Save to `quality_reports/merges/YYYY-MM-DD_[branch-name].md`.

## Tolerance Thresholds (Research)

<!-- Customize for your domain -->

| Quantity | Tolerance | Rationale |
|----------|-----------|-----------|
| Point estimates | [e.g., 1e-6] | [Numerical precision] |
| Standard errors | [e.g., 1e-4] | [MC variability] |
| Coverage rates | [e.g., +/- 0.01] | [MC with B reps] |
