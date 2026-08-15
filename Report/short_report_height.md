# Short Report — Hypsometric Relationship and Height Estimation (Stand 8, Eucalyptus)

## 1. Height sampling pattern (Task 1)

Height was measured in exactly **109 of 215 trees (50.7%)**, and the pattern is
perfectly consistent: **only rows 3, 4, and 5** (the three interior rows) have
measured height; **rows 1, 2, 6, and 7** (the four border rows) have HT = 0 for
every single tree. This confirms the assignment's description — a deliberate
systematic subsample of interior rows, designed to avoid border/edge effects
(edge trees typically grow differently — often taller and more open-crowned — due
to reduced competition at the plot boundary).

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

**Does the systematic center-row subsample introduce bias? Yes — specifically for
dominant height, and the direction is explainable.**

**Why mean height is not meaningfully biased, but dominant height is:**
Dominant height, by definition, is driven by the *largest* trees. If the largest
trees in a plot are disproportionately likely to occur in the *border rows*
(rows 1, 2, 6, 7) — which is biologically plausible, since border trees face less
competition for light and growing space than interior trees — then a model fit
using *only* interior-row trees will never actually see the plot's true largest
individuals. It only sees the (systematically slightly smaller) interior
population, and the hypsometric model built from that population, when applied
back to estimate heights for border trees, still assumes the DBH→HT relationship
learned from the interior sample. If the true dominant trees are large-DBH border
trees, the naive "measured-only" dominant height calculation *never includes them
at all* (since they weren't measured), silently excluding exactly the individuals
that matter most for this metric.

**Direction of the bias:** the measured-only dominant height consistently
underestimating the true dominant height (complete dataset). This is consistent
with the interpretation above — border-row release effects allow the true largest
trees in the stand to be concentrated in border positions, and a
subsample that structurally excludes ~57% of the tree population (4 of 7 rows)
from ever being candidates for the "dominant" ranking will tend to systematically
miss some of the actual largest individuals.

**Practical implication:** for any application where dominant height matters —
site index classification, growth/yield modeling, silvicultural decision-making —
using only the interior-row subsample's own trees to rank "dominant" individuals
would understate site productivity. The fix implemented here (estimating heights
for ALL 215 trees via the hypsometric model, THEN ranking across the complete
population to find dominant height) largely corrects this, because it lets
border-row trees compete for the "dominant" ranking based on their (estimated)
height, even though their height was never directly measured.

**Remaining caveat:** this correction still depends entirely on the hypsometric
model being valid for border trees, which were never actually measured — if
border trees have a systematically different DBH-height relationship than
interior trees (plausible, given they may express the DBH-height allometry
differently under the release effect), the model built purely on interior trees
could still mis-estimate their true heights, even if it correctly identifies which
individuals are DBH-dominant.

**Recommendation:** if resources allow in future inventories, measuring height on
at least a handful of border-row trees per plot (not necessarily all of them)
would allow testing whether the border and interior populations share the same
hypsometric relationship — directly validating (or correcting) the assumption this
entire estimation approach rests on.
