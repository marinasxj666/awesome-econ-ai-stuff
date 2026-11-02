---
name: latex-tables
description: "Turn existing regression output (esttab .tex or .csv) into a clean booktabs LaTeX table with notes and stars. Use only after the numbers exist. Column order and the esttab-style layout come from xjs-data-output; the Stata code from stata-regression."
workflow_stage: writing
compatibility:
  - claude-code
  - cursor
  - codex
  - gemini-cli
author: Awesome Econ AI Community
version: 1.0.0
tags:
  - latex
  - tables
  - regression
  - booktabs
---

# LaTeX Tables

## Purpose

This skill creates clean, publication-ready tables in LaTeX for regression results and summary statistics, using standard academic formatting.

## When to Use

- Converting model output into LaTeX tables
- Standardizing table style across a paper
- Adding notes, significance stars, and labels

## Instructions

Follow these steps to complete the task:

### Step 1: Understand the Context

Before generating any code, ask the user:

- What type of table is needed (regression, summary stats, balance)?
- What software produced the results (Stata, R, Python)?
- Which formatting style is required (journal-specific, AEA, etc.)?

### Step 2: Generate the Output

Based on the context, generate LaTeX code that:

1. **Uses `booktabs`** for clean horizontal rules
2. **Includes labels and captions** for referencing in the paper
3. **Adds notes** for standard errors and significance
4. **Aligns numeric columns** for readability

### Step 3: Verify and Explain

After generating output:

- Explain how to compile the table
- Highlight any assumptions in the formatting
- Suggest refinements for journal submission

## Example Prompts

- "Create a regression table with three models in LaTeX"
- "Format summary statistics with mean and sd columns"
- "Add significance stars and standard error notes"

## Example Output

```latex
% ============================================
% Regression Table
% ============================================
\begin{table}[htbp]\centering
\caption{Effect of Treatment on Outcome}
\label{tab:main_results}
\begin{tabular}{lccc}
\toprule
 & (1) & (2) & (3) \\
\midrule
Treatment & 0.125*** & 0.118*** & 0.102** \\
 & (0.041) & (0.039) & (0.046) \\
Controls & No & Yes & Yes \\
Fixed Effects & No & Yes & Yes \\
\midrule
Observations & 2,145 & 2,145 & 2,145 \\
R-squared & 0.18 & 0.24 & 0.31 \\
\bottomrule
\end{tabular}
\begin{tablenotes}
\small
\item Notes: Standard errors in parentheses. * p<0.10, ** p<0.05, *** p<0.01.
\end{tablenotes}
\end{table}
```

## Requirements

### Software

- LaTeX distribution (TeX Live or MikTeX)

### Packages

- `booktabs`
- `threeparttable` (optional for notes)

## Permutation Grids (User Rule — Binding)

When the results being formatted come from a requested permutation of
specifications (control sets, fixed effects, clustering, designs, variable
definitions), the table must give a clear side-by-side picture:

1. **One consolidated table** holding the whole grid — never split the
   permutations across separate tables the user has to line up. If the grid
   is too large for one page, use a spec-curve figure or a landscape/small
   font table instead of splitting; tables must never break across pages.
2. **Every column identified by its exact spec** via indicator rows
   (Controls / Fixed effects / Clustered by / Variable definition / Sample),
   so no column's spec has to be inferred from the caption.
3. **Observations reported in every column** — N is mandatory per the user's
   standing verification rule.
4. **Include every specification that was run.** Dropping columns because
   they look redundant or unfavorable misrepresents the grid; the user asked
   for all of it.

```latex
% spec-grid skeleton: indicator rows identify each column's specification
\midrule
Controls        & No     & Base   & Base   & Full   \\
Fixed Effects   & Firm   & Firm+Yr& Ind$\times$Yr & Ind$\times$Yr \\
Clustered by    & Firm   & Firm   & Firm   & Industry \\
Outcome def.    & A      & A      & B      & B      \\
\midrule
Observations    & 2,145  & 2,145  & 2,101  & 2,101  \\
```

## Best Practices

1. **Keep tables compact** and readable
2. **Use consistent notation** for standard errors and stars
3. **Provide clear captions and labels**

## Common Pitfalls

- Overly wide tables that do not fit the page
- Missing notes for standard errors
- Inconsistent labeling across tables

## References

- [LaTeX booktabs documentation](https://ctan.org/pkg/booktabs)
- [AEA Author Guidelines](https://www.aeaweb.org/journals/policies/author-instructions)

## Changelog

### v1.1.0

- Add binding permutation-grid rule: one consolidated table for the full grid, spec-indicator rows, N in every column, no page breaks

### v1.0.0

- Initial release
