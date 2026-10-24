---
name: stata-regression
description: "Stata regression code scaffolds: reghdfe, xtreg, clustered SEs, eststo/esttab loops, basic robustness. Use for writing or debugging the do-file. Not for the delivered table layout or results workbook (use xjs-data-output), nor for method recipes from JAR replication files (use stata-accounting-research)."
workflow_stage: analysis
compatibility:
  - claude-code
  - cursor
  - codex
  - gemini-cli
author: Awesome Econ AI Community
version: 1.0.0
tags:
  - stata
  - regression
  - esttab
  - econometrics
---

# Stata Regression

## Purpose

This skill produces reproducible regression analysis workflows in Stata, including model diagnostics and publication-ready tables using `esttab` or `outreg2`.

## When to Use

- Estimating linear or nonlinear regression models in Stata
- Producing tables for academic papers and reports
- Running robustness checks and alternative specifications

## Instructions

Follow these steps to complete the task:

### Step 1: Understand the Context

Before generating any code, ask the user:

- What is the dependent variable and key regressors?
- What controls and fixed effects are required?
- How should standard errors be clustered?
- What output format is needed (LaTeX, Word, or CSV)?

### Step 2: Generate the Output

Based on the context, generate Stata code that:

1. **Loads and checks the data** - Handle missing values and verify variable types
2. **Runs the requested specification** - Use `regress`, `reghdfe`, or `xtreg` as appropriate
3. **Adds robust or clustered standard errors** - Match the study design
4. **Exports tables** - Use `esttab` or `outreg2` with clear labels

### Step 3: Verify and Explain

After generating output:

- Explain what each model estimates
- Highlight assumptions and diagnostics
- Suggest robustness checks or alternative models

## Example Prompts

- "Run OLS with firm and year fixed effects, clustering by firm"
- "Estimate a logit model and export results to LaTeX"
- "Create a regression table with three specifications"

## Example Output

```stata
* ============================================
* Regression Analysis with Stata
* ============================================

* Load data
use "data.dta", clear

* Summary stats
summarize y x1 x2 x3

* Main regression with clustered SEs
regress y x1 x2 x3, vce(cluster firm_id)
eststo model1

* Alternative specification with fixed effects
reghdfe y x1 x2 x3, absorb(firm_id year) vce(cluster firm_id)
eststo model2

* Export table
esttab model1 model2 using "2_result/tables/regression_table.tex", replace se label
```

## Requirements

### Software

- Stata 17+

### Packages

- `estout` (for `esttab`)
- `reghdfe` (optional, for high-dimensional fixed effects)

Install with:

```stata
ssc install estout
ssc install reghdfe
```

## Permutation Grids (User Rule — Binding)

When the user asks for results across permutations of specifications —
control sets, fixed-effects structures, clustering levels, alternative
designs, alternative variable definitions — that request IS the spec:

1. **Run the full grid, every combination named.** Do not prune to a subset
   you consider informative; cherry-picking permutations is as wrong as
   adding unrequested ones. Conversely, do not add specifications beyond the
   grid without asking.
2. **Deliver one consolidated table built for comparison** — a single
   `esttab` with one column per specification, never a scatter of separate
   tables the user has to line up.
3. **Label every column with its exact spec** (controls, FE, clustering,
   variable definition, sample) using indicator rows, and always report N
   per column; the user requires N and fixed effects confirmed for every
   regression.
4. **For large grids, state the count and estimated runtime first** (e.g.,
   4 control sets x 3 FE x 2 clusterings = 24 regressions), then run all of
   it on confirmation.

```stata
* --- permutation grid: loop over the full requested spec set ---
eststo clear
local spec = 0
foreach fe in "firm_id" "firm_id year" "firm_id industry#year" {
    foreach cl in firm_id industry {
        local ++spec
        eststo m`spec': reghdfe y x1 x2 x3, absorb(`fe') vce(cluster `cl')
        estadd local fe_used "`fe'"
        estadd local cluster_used "`cl'"
    }
}
esttab m* using "2_result/tables/spec_grid.tex", replace se label ///
    scalars("fe_used Fixed effects" "cluster_used Clustered by" "N Observations") ///
    mtitles

di as result "GRID DONE: `spec' specifications estimated"
```

## Best Practices

1. **Match standard errors to the design** (cluster where treatment varies)
2. **Report all model variants** used in the analysis
3. **Document variable definitions** and transformations

## Common Pitfalls

- Not clustering standard errors at the correct level
- Omitting fixed effects when required by the design
- Exporting tables without clear labels and notes

## References

- [Stata Regression Reference Manual](https://www.stata.com/manuals/rregress.pdf)
- [reghdfe documentation](https://github.com/sergiocorreia/reghdfe)
- [estout documentation](https://repec.sowi.unibe.ch/stata/estout/)

## Changelog

### v1.1.0

- Add binding permutation-grid rule: run the full requested spec grid, one consolidated esttab, spec labels + N per column

### v1.0.0

- Initial release
