# rule-waterfall-builder

Traces records through the sequential stages of an approval journey and shows **which rule caused the drop at each stage**, as a waterfall chart and a rule-by-stage table.

Runs entirely in your browser. Nothing you paste or upload leaves your machine.

## How it works

Records flow through Stage 1, then Stage 2, then Stage 3, and so on.

- A record that fires a rule at a stage **drops out there**.
- A record with no rule (blank or `(null)`) **moves on** to the next stage.
- Records that clear every stage are the final **Passed** bar.
- A record dropped at an earlier stage is **not counted again** at later stages, even if the data shows a rule for it there.

The same rule can exist in more than one stage (for example "Rule A" at Stage 1 and Stage 2). It keeps the same colour everywhere so you can follow it across the journey.

## Usage

1. Open `index.html` (or the GitHub Pages link).
2. Paste your data or upload a CSV/TSV, then click **Build waterfall**.
3. Tick or untick the stages to include. Stage order follows the column order in your file.
4. Pick how overlapping rules are credited (see below).
5. Hover any bar segment for its exact count and % of the starting total.

Try it with `sample-pivot.csv`.

## Input formats

The format is detected automatically.

### 1. Pivot export (e.g. Google Sheets)

One column per stage, then a count column as the last column. Quoted exports (every cell in `"..."`, empty cells as `""`) and header names such as `PLACEHOLDER_01` are handled. Stages with placeholder or missing names become Stage 1, Stage 2, and so on, and you can rename them in the app. Subtotal rows and `(null)` cells are fine. Only the fully broken-down rows are used, so nothing is double counted.

| Stage 1 | Stage 2 | Stage 3 | Count |
|---|---|---|---|
| Rule A | | | 1,101 |
| | Rule A | | 306 |
| | | (null) | 306 |
| | (null) | | 717 |
| | | (null) | 717 |

A header row is optional. Without one, stages are named Stage 1, Stage 2, and so on.

### 2. One row per record

One column per stage. Each cell holds the rule(s) that fired. Separate several rules in one cell with `|` or `;` (not a comma, since rule names often contain commas). Blank or `(null)` means no rule fired.

```
id,Stage 1,Stage 2,Stage 3
1,DNC|Opt-out,,
2,,Low score,
3,,,Model hold
4,,,
```

## Handling overlapping rules

When one record fires several rules in the same stage, the drop can only be counted once. Choose how to credit it:

| Option | Behaviour |
|---|---|
| Biggest rule (priority) | The record is credited to the rule with the most hits in that stage |
| Split equally | A record with 2 rules gives 0.5 to each |
| Exact rule combinations | Segments such as `DNC + Opt-out` appear on their own |

## Outputs

- **Waterfall chart:** Start, one stacked drop bar per stage (coloured by rule), then Passed.
- **Rules by stage table:** one row per rule, one column per stage, plus a total. Each cell shows the count dropped and its % of all starting records. Summary rows give total dropped, entering stage, and passed to next.

## Limitations

- Stage order is the column order in the file.
- One rule value per stage per path in pivot format. Overlaps inside a stage need the `|` separator.
- Percentages are always relative to the starting total, not to the stage entry count.
