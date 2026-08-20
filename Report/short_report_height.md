# Short Report — Hypsometric Relationship and Height Estimation (Stand 8, Eucalyptus)

## 1. Height sampling pattern (Task 1)

Height was measured in exactly **109 of 215 trees (50.7%)**, and the pattern is
perfectly consistent: **only rows 3, 4, and 5** (the three interior rows) have
measured height; **rows 1, 2, 6, and 7** (the four border rows) have HT = 0 for
every single tree. This confirms the assignment's description — a deliberate
systematic subsample of interior rows, stated as a design intended to avoid
border/edge effects. Section 5 tests this assumption directly against the data
rather than taking it at face value.

## 2. Hypsometric models (Task 2)

Two models were fit using only the 109 measured trees:

| Model | Equation | R² | RMSE (m) |
|---|---|---|---|
| Linear | HT = 9.496 + 1.118 × DBH | 0.852 | 1.234 |
| Log-log | ln(HT) = 1.404 + 0.689 × ln(DBH) | 0.864 (original scale) | 1.184 |

The log-log model was back-transformed to the original scale using the
Baskerville correction factor (1.0011) to avoid the systematic underestimation
that a naive `exp()` back-transform would introduce.

**Chosen model: log-log**, based on marginally better R² and lower RMSE. Both
models are strong (R² > 0.85), and the fitted curves are visually close across
most of the DBH range (see `hypsometric_scatter_fit.png`) — the log-log curve
tracks the data slightly better at the low end (DBH < 12 cm), where tree height
growth naturally flattens relative to diameter, a pattern a straight line cannot
capture but a power/log curve can.

**Residual analysis** (`hypsometric_residuals.png`): residuals for both models are
reasonably scattered around zero without a strong funnel shape (no obvious
heteroscedasticity), though there is a mild curve at the extremes — small,
negative residuals cluster at both the lowest and highest fitted heights, with
more positive residuals in the middle range. This is a common, mild pattern in
hypsometric fits and does not invalidate either model, but it does suggest a more
flexible model (e.g., a 3-parameter curve like Chapman-Richards) could tighten the
fit further if pursued beyond this assignment's scope.

## 3. Applying the model to unmeasured trees (Task 3)

The log-log model was applied to all 106 unmeasured trees (rows 1, 2, 6, 7),
producing a complete 215-tree dataset with every tree assigned a height — either
**measured** (109 trees) or **estimated** (106 trees). The full dataset, with a
`ht_source` column identifying which is which, is in
`complete_dataset_with_height.csv`.

## 4. Mean height and dominant height: complete data vs. measured-only (Task 4)

### Mean height per plot

| Plot | Mean HT — complete (m) | Mean HT — measured-only (m) | Difference (m) | Difference (%) |
|---|---|---|---|---|
| 1 | 27.560 | 28.558 | −0.998 | −3.5% |
| 2 | 26.663 | 26.429 | +0.233 | +0.9% |
| 3 | 26.192 | 26.747 | −0.555 | −2.1% |
| 4 | 27.011 | 26.933 | +0.078 | +0.3% |
| 5 | 28.278 | 28.753 | −0.475 | −1.7% |
| 6 | 27.359 | 27.162 | +0.197 | +0.7% |

The naive measured-only average is **not consistently biased in one direction**
across plots — some plots show the measured-only mean higher than the complete
mean (Plots 1, 3, 5), others lower (Plots 2, 4, 6). Magnitude is modest (under
3.5% in every case). This is a reassuring result: it suggests the interior rows
are broadly representative of overall plot height, at least for the *simple mean*.

### Dominant height: comparing definitions

Three definitions were compared per plot, all computed on the complete dataset:

| Plot | Assmann (top 4/plot) | Fixed top-6/plot | Single tallest tree |
|---|---|---|---|
| 1 | 32.122 | 31.912 | 32.810 |
| 2 | 30.557 | 30.451 | 31.804 |
| 3 | 30.386 | 30.174 | 31.042 |
| 4 | 30.382 | 30.162 | 30.819 |
| 5 | 32.379 | 32.358 | 34.317 |
| 6 | 30.528 | 30.401 | 30.000 |

**Selected definition: Assmann's classic dominant height** — the mean height of
the 100 largest-DBH trees per hectare, scaled to this plot's area (0.0441 ha →
top 4 largest-DBH trees per plot). This is chosen as "the most used" because it is
the internationally standard definition in forest mensuration (Assmann, 1970) and
the most common in Brazilian forestry practice and literature. The single-tallest-
tree definition, while simplest, is not recommended as a standard — it is highly
sensitive to a single tree's anomalies (breakage, forking, measurement error) and
does not represent a genuine "dominant class." The fixed-top-6 definition is a
reasonable practical proxy but is arbitrary relative to plot size.

### Dominant height bias: complete data vs. measured-only

| Plot | Assmann Hdom — complete (m) | Assmann Hdom — measured-only (m) | Difference (m) |
|---|---|---|---|
| 1 | 32.122 | 31.175 | +0.947 |
| 2 | 30.557 | 29.500 | +1.057 |
| 3 | 30.386 | 30.000 | +0.386 |
| 4 | 30.382 | 29.775 | +0.607 |
| 5 | 32.379 | 31.675 | +0.704 |
| 6 | 30.528 | 30.025 | +0.503 |

**This is the key finding of Task 4.** Unlike the simple mean height (which showed
no consistent direction of bias), **dominant height computed from the
measured-only subsample is consistently and systematically LOWER than the complete
dataset, in every single plot** (differences of +0.39 to +1.06 m, always positive
when subtracting measured-only from complete). See Section 5 for the explanation.

## 5. Critical discussion — risks of the systematic subsample (Task 5)

**Does the systematic center-row subsample introduce bias?** To answer this
properly — rather than by assumption — we tested representativeness directly,
since DBH (unlike height) was measured on all 215 trees. This lets us compare the
diameter distribution of the "measured" rows (3, 4, 5) against the "unmeasured"
rows (1, 2, 6, 7) with no modeling involved.

### Representativeness checks (evidence, not assumption)

**Distributional comparison — no significant difference overall:**

| Test | Statistic | p-value |
|---|---|---|
| Welch's t-test (means) | t = 1.070 | 0.286 |
| Mann-Whitney U (ranks) | U = 6020.5 | 0.594 |
| Kolmogorov-Smirnov (shape) | D = 0.094 | 0.677 |

All three tests return p-values well above 0.05 — there is no statistically
reliable evidence that the interior-row (measured) and border-row (unmeasured)
populations have different DBH distributions at the stand level.

**Tail comparison — where it matters most for dominant height:**

| | Interior rows (measured) | Border rows (unmeasured) |
|---|---|---|
| Top 10% largest (mean DBH) | 20.44 cm | 20.12 cm |
| Bottom 10% smallest (mean DBH) | 11.10 cm | 8.43 cm |
| 5th percentile | 11.58 cm | 8.86 cm |
| 95th percentile | 19.96 cm | 19.71 cm |

The **largest** trees are nearly identical between the two groups — meaning there
is no evidence that genuinely dominant (large-DBH) trees are disproportionately
concentrated in border positions, which is an initial hypothesis this report
considered and explicitly rules out here. What *does* differ is the **small end**:
border rows contain more small/suppressed trees, plausibly due to mortality
patterns or edge competition effects — but this does not, by itself, bias
dominant height (which depends on the top of the distribution, not the bottom).

### So why was dominant height still biased in Section 4?

**Per-plot check — where each plot's single largest tree actually sits:**

| Plot | Largest tree's row | Border row? |
|---|---|---|
| 1 | 7 | Yes |
| 2 | 6 | Yes |
| 3 | 6 | Yes |
| 4 | 7 | Yes |
| 5 | 1 | Yes |
| 6 | 3 | No |

In **5 of 6 plots**, the single largest-DBH tree happens to sit in a border row —
even though, as shown above, there is no stand-wide statistical tendency for large
trees to concentrate there. This is best explained as **plot-specific spatial
variation** (natural, expected randomness in where the biggest individual in a
small sample of ~35 trees happens to fall) rather than a systematic biological
bias. Because the measured-only subsample structurally excludes 4 of 7 rows,
it has a mechanical ~57% chance of missing any given plot's true largest tree
regardless of any underlying distributional difference — and with 5 of 6 plots
landing that way, that's exactly what happened here.

### Practical implication

For any application where dominant height matters — site index classification,
growth/yield modeling, silvicultural decision-making — using only the
interior-row subsample to rank "dominant" individuals risks missing a plot's true
largest trees, **not because border and interior populations are biologically
different, but simply because the subsample structurally excludes most of the
plot's area from ever being considered.** The fix implemented in Section 4
(estimating heights for all 215 trees via the hypsometric model, then ranking
across the complete population) corrects this, since it lets border-row trees
compete for the "dominant" ranking using their DBH (measured directly) and their
estimated height.

### Remaining caveat

This correction still depends on the hypsometric model being valid when applied
to border trees, which were never height-measured. Since the distributional
checks above found no significant DBH difference between interior and border
populations, there's no strong evidence the DBH→height relationship itself would
differ either — but this remains an assumption, not something directly verified,
since no border tree in this dataset has a measured height to check against.

### Recommendation

If resources allow in future inventories, measuring height on a handful of
border-row trees per plot (not necessarily all of them) would let the
DBH→height relationship itself be tested for interior-vs-border consistency —
directly validating the one assumption this entire estimation approach still
rests on, rather than inferring it indirectly from the DBH distributions alone.
