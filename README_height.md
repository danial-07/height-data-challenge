# Height Data Challenge — Hypsometric Relationship (Stand 8, Eucalyptus)

## Contents

| File | Description |
|---|---|
| `altura_hipsometrica.py` | Commented Python script — all 4 analytical tasks |
| `short_report_height.md` | Short report with the critical discussion (Task 5) |
| `complete_dataset_with_height.csv` | Final dataset: 215 trees, all with height (measured or estimated), flagged |
| `hypsometric_scatter_fit.png` | Scatter plot of DBH × Height with both fitted curves |
| `hypsometric_residuals.png` | Residual diagnostics for both models |
| `mean_height_comparison.csv` | Per-plot mean height: complete data vs. measured-only |
| `dominant_height_comparison.csv` | Per-plot dominant height under 3 different definitions |
| `dominant_height_bias.csv` | Assmann dominant height: complete vs. measured-only |

## Methodology summary

**Sampling pattern confirmed:** height was measured only in rows 3, 4, and 5 (the
interior rows) — 109 of 215 trees. Rows 1, 2, 6, and 7 (border rows) have no
height measurement. This is a deliberate design choice (avoiding border/edge
growth effects), not a data quality issue.

**Hypsometric models fit** (using only the 109 measured trees):
- Linear: `HT = 9.496 + 1.118 × DBH` (R² = 0.852, RMSE = 1.234 m)
- Log-log: `ln(HT) = 1.404 + 0.689 × ln(DBH)` (R² = 0.864, RMSE = 1.184 m,
  back-transformed with the Baskerville correction factor to avoid systematic
  underestimation on the original scale)

The **log-log model** was selected (marginally better fit) and applied to
estimate height for the 106 unmeasured (border-row) trees.

**Dominant height definition:** Assmann's classic definition — mean height of the
100 largest-DBH trees per hectare — scaled to this plot's area (0.0441 ha, from
the diameter challenge), giving the top 4 largest-DBH trees per plot. This is the
internationally standard definition in forest mensuration and the most common in
Brazilian forestry practice. Two alternative definitions (fixed top-6 trees per
plot; single tallest tree) were also computed for comparison — see
`dominant_height_comparison.csv` and the short report for the rationale.

## Key finding

Comparing the complete (measured + estimated) dataset against a naive
measured-only calculation:
- **Mean height** shows no consistent bias direction across plots (differences
  ranged from −3.5% to +0.9%) — the interior-row subsample is broadly
  representative for this simple statistic.
- **Dominant height**, in contrast, is **consistently underestimated** by the
  measured-only approach in every single plot (by 0.39–1.06 m). This is the
  central result of the critical discussion (Task 5) — see
  `short_report_height.md` for the full explanation and its practical
  implications.

## How to reproduce

```bash
pip install pandas numpy matplotlib openpyxl
python3 altura_hipsometrica.py
```

The script auto-detects the `.xlsx` inventory file from the uploads/input folder
— update the `INPUT_FILE` detection logic if running outside the original
environment.
