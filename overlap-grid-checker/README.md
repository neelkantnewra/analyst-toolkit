# Overlap Grid Checker

A browser-based tool that takes a multi-variable count table — e.g. straight from `GROUP BY var1, var2, ...` in SQL — and finds which combinations of those variables have little or no data behind them.

Runs entirely client-side. Nothing you paste in is sent anywhere.

## Why

Before trusting a conditional estimate (a conversion rate, a treatment effect, an average — anything sliced by more than one variable), you need every combination you care about to actually have enough observations behind it. A pivot table shows you the grid, but it won't tell you which of the combinations that *could* exist never showed up in your data at all, and that check gets harder to do by hand as soon as you add a third or fourth variable.

## How it works

1. **Paste your data** with a header row: any number of variable columns, followed by a `count` column — e.g.: segment treatment,age_group,count Active,Postcard,18-25,120
2. **Set a threshold** — any cell at or below this count gets flagged as low-confidence or missing.
3. Click **Analyze** to get:
   - A **global combination list**: every possible combination across *all* variables (the full cross-product of each variable's observed values), sorted by count, so the emptiest or sparsest combinations surface immediately — including combinations that never appear in your data at all, not just ones with a literal zero row.
   - A **2D heatmap slice**: pick any two variables as rows/columns; every other variable gets a dropdown to either fix it at one value or sum across it ("All"), so you can visually inspect any pairwise cut of a higher-dimensional space without trying to render all of it at once.

## Notes

- If the full cross-product of all variables' values exceeds 20,000 combinations, the global list falls back to only the combinations that actually appear in your pasted data (flagged with an asterisk) rather than trying to enumerate every theoretical one.
- This tool assumes your input categories are mutually exclusive per row (no double-counting from overlapping definitions or an un-deduplicated join upstream) — check that at the query level first, since no downstream tool can detect that kind of overlap for you.
- Blank/missing combinations are treated as a count of 0, same as an explicit zero row.

## Usage

Open `index.html` in any browser. No install, no dependencies, no server required.