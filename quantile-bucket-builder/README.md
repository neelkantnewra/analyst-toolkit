# Quantile Bucket Builder

A browser-based tool that turns a numeric variable — pasted as raw values or as a value+count table straight from a `GROUP BY` — into population-balanced buckets, and generates a ready-to-use SQL `CASE WHEN` to recreate them.

Runs entirely client-side. Nothing you paste in is sent anywhere.

## Why

Splitting a variable like age or income into buckets by hand (0-18, 19-35, 36-50...) usually means some buckets end up nearly empty and others hold most of your data — which is a problem the moment you're comparing treatment effects, conversion rates, or anything else across those groups. This tool builds the bucket edges from the actual distribution instead, so each bucket holds roughly the target share of your data, and hands you the SQL to reproduce it.

## How it works

1. **Choose a data format**: paste raw values (comma, space, or newline separated), or paste a `value, count` table copied straight from a SQL aggregate query.
2. **Set a target % per bucket** (e.g. 20 → roughly quintiles) and, optionally, a rounding step so edges land on clean numbers (e.g. nearest 5) instead of exact percentile values like 34.7.
3. **List any sentinel/outlier codes** (e.g. `99999`, `-1`) that should become their own standalone bucket instead of being folded into the quantile calculation — this keeps placeholder values from distorting the real bucket edges.
4. Click **Compute buckets** to get:
   - A lettered, population-balanced bucket table (`A) under 25`, `B) 25–40`, ...) with the count and % of data in each.
   - A generated `CASE WHEN` statement using the same buckets and labels, ready to paste into SQL.
5. Copy the SQL with the **Copy** button under the output.

## Notes

- If the true minimum or maximum of your data is a clean number (e.g. exactly 0, or a multiple of your rounding step), the tool uses it as a real fixed edge instead of an open-ended "under X" or "X+" label.
- Rounding bucket edges can cause two adjacent edges to collide — these are merged automatically, so you may end up with fewer buckets than the target % implies. This is expected: it's better than forcing an empty bucket.
- With grouped `value, count` input, percentiles are computed on the weighted distribution (a step function over cumulative counts), not by expanding every row — so it works fine even with counts in the millions.
- This is a quantile-based approach, not a clustering or optimal-binning method — it won't detect natural breakpoints in your data, only split by population share.

## Usage

Open `index.html` in any browser. No install, no dependencies, no server required.