Repository Link: https://github.com/Kbeaty26/DS701-Midterm-Project.git

## Question 1: Can property characteristics predict sale prices more accurately than neighborhood-level price estimates?

### Approach
The baseline predicts each home's price as its **neighborhood median**. The floor predicts the overall median. We compare these with **Ridge regression** (interpretable, regularized for ~200 correlated columns) and **gradient boosting** (captures non-linearities and interactions such as quality × size). Both use **property characteristics only**: we removed location (`Neighborhood`, `MSZoning`, `Condition1/2`) so the test isolates characteristics against location, and removed sale-transaction fields (`SaleType`, `SaleCondition`, month/year sold), which are not known before a sale. A third model adds location back. NA in quality and type columns means "absent" (no garage, no pool, etc.). Ordinal quality codes map to 1–5 with absent = 0, and categorical NAs get their own level. All models fit log price, since the target is skewed (skew 1.9 to 0.1), and predictions are converted back to dollars for scoring. We evaluated with **5-fold CV** on `train.csv`. Neighborhood medians, imputation, scaling, encoding, and the Ridge penalty are all fitted inside each training fold. Gradient-boosting settings were left at standard defaults, so we did not tune on the reported folds. `test.csv` is unlabeled and unused.

### Evidence 
(mean ± SD over 5 folds; all 1,460 homes):

| Method | MAE | RMSE | R² |
|---|---|---|---|
| Overall median | \$55.7k ± 3.2k | \$81.3k ± 5.9k | −0.05 ± 0.02 |
| **Neighborhood median (baseline)** | **\$36.2k ± 1.4k** | **\$55.3k ± 3.2k** | **0.51 ± 0.03** |
| Ridge – characteristics | \$17.2k ± 2.7k | \$41.1k ± 30.7k | 0.58 ± 0.69 |
| **GBoost – characteristics** | **\$15.7k ± 1.5k** | **\$28.9k ± 9.3k** | **0.85 ± 0.12** |
| GBoost – characteristics + location | \$14.8k ± 1.7k | \$27.8k ± 10.0k | 0.86 ± 0.13 |

- Gradient boosting on characteristics cuts MAE by **57%** relative to the neighborhood median. It wins in **all 5 folds** (by 50–62%), is closer for **75%** of homes, and beats the baseline in **all 25 neighborhoods**. Median absolute error falls from 14.7% to 5.9% of price.
- Adding location helps only slightly (MAE \$15.7k → \$14.8k), so most of what the neighborhood median captures is already reflected in the house itself.
- Gains are largest in neighborhoods with mixed housing stock. Model gain correlates with within-neighborhood price variability (r = 0.43), e.g. StoneBr and Veenker improve by ~21 percentage points.
- *Ridge's large RMSE spread comes from one home.* Id 1299 is a 5,642 sq ft partial sale at \$160k, and Ridge predicts \$1.6M because linear models extrapolate. Excluding the 4 homes over 4,000 sq ft, as the dataset's author recommends, both Ridge and GBoost reach R² ≈ 0.92, compared with 0.52 for the baseline. The conclusion is unchanged.

![Q1 predictions](results/q1_figure.png)

### Limitations
(1) There is no independent test score. `test.csv` has no prices, so all estimates come from cross-validation on one dataset. (2) Very large or partial sales are badly mispredicted. Estimates should flag homes for review by a person, not price them automatically. (3) The folds are random, not time-based. We do not show performance on future sales, such as after the 2008 downturn; that would need a time-based split or newer data. (4) Gradient-boosting hyperparameters were not tuned, so its performance may be slightly understated. (5) The results are predictive associations in one market (Ames, 2006–2010), not causal effects. Q3 examines which features drive them.

## Question 2: Do homes form distinct property segments based on their physical characteristics?

### Approach

Real estate analysts and developers could use property segments to identify comparable homes and investigate potential investment or renovation opportunities. We applied K-means clustering to physical property characteristics, excluding `SalePrice` so that sale price did not determine cluster assignments. We compared two feature configurations: an initial set of 18 features and a reduced set of 15 features after removing correlated variables. We evaluated candidate cluster counts using silhouette scores and assessed sensitivity to feature selection using the adjusted Rand index (ARI). We then interpreted the clusters using characteristics such as construction year, overall quality, and living area. Sale prices were reserved for post-clustering comparison.

### Evidence

The results suggest that homes do not form strongly separated groups, with silhouette scores ranging from approximately 0.14 to 0.23. The 18-feature configuration favored three clusters (silhouette = 0.220), identifying spacious, high-quality two-story homes (407 homes), newer, high-quality ranch-style homes (355), and older, smaller, lower-quality homes (698). However, after removing correlated features, the best solution was two clusters (silhouette = 0.227): older, modest homes (median construction year approximately 1952, overall quality 5.18, and living area 1,204 sq. ft.) and newer, larger, higher-quality homes (median construction year approximately 1991, overall quality 7.06, and living area 1,841 sq. ft.).

The ARI between the two feature configurations was 0.94 at \(k=2\), indicating strong agreement, compared with 0.62 at \(k=3\), indicating less consistent assignments. Together with the higher silhouette score, this suggests that the two-cluster solution is more robust to feature selection, while the three-cluster solution provides more detailed but less stable segments.

| 15 Features | 18 Features |
|:---:|:---:|
| ![15-feature clustering](results/15features.png) | ![18-feature clustering](results/18features.png) |

### Limitations

The low silhouette scores indicate substantial overlap between clusters, so the segments should be interpreted as approximate property profiles rather than distinct housing categories. The three-cluster solution was more sensitive to feature selection, particularly the inclusion of second-floor area, suggesting that correlated features influenced the segmentation. K-means also favors compact, roughly spherical clusters and can be sensitive to skewed variables such as lot area. Future work could test log transformations, alternative clustering methods such as hierarchical clustering or Gaussian mixture models, and categorical features such as house style or neighborhood. In practice, real estate analysts should use these segments alongside neighborhood and other property characteristics rather than as strict rules for selecting comparable homes.

## Question 3: Which property characteristics are consistently associated with sale price?

### Approach

We fit a random forest regressor using property characteristics, excluding identifiers, location, and sale-transaction fields. We evaluated it with five-fold cross-validation on the labeled training set; imputation and categorical encoding were fitted within each training fold. To identify robust predictors, we measured permutation importance on each held-out fold: the decrease in R² when a feature was shuffled. We also examined three plausible combinations of important features—total floor area × overall quality, total floor area × garage capacity, and house age × overall quality. For each pair, we compared the effect of jointly shuffling both features with shuffling either one alone, and used two-way partial-dependence surfaces to assess non-additive patterns. The unlabeled test set was not used.

### Evidence

Across the five folds, the random forest achieved a mean MAE of **$16,989 ± $1,967**, RMSE of **$30,244 ± $7,620**, and R² of **0.843 ± 0.099**. Total floor area (`TotalSF`) and overall quality (`OverallQual`) were the strongest individual predictors: both had positive permutation importance and ranked in the top 10 in all five folds. Overall condition was also positive and top-10 in all folds; garage capacity, lot area, and house age were positive in all folds and top-10 in three or four.

| Feature combination | Joint permutation R² decrease | Additional decrease vs. stronger single feature | Partial-dependence H score |
|---|---:|---:|---:|
| **Total floor area × overall quality** | **0.706 ± 0.044** | **0.431** | **0.253 ± 0.013** |
| Total floor area × garage capacity | 0.303 ± 0.032 | 0.026 | 0.028 ± 0.015 |
| House age × overall quality | 0.169 ± 0.026 | 0.021 | 0.022 ± 0.005 |

The area-quality pair carried the most combined predictive information, with a larger non-additive pattern in its partial-dependence surface than the other pairs. The other combinations added relatively little beyond their stronger individual feature. These findings suggest that home size and quality are particularly informative when considered together.

### Limitations

The pair analysis is exploratory and limited to three combinations selected from the important features; it does not establish that adding a handcrafted interaction improves predictive performance. Joint permutation importance measures reliance on the pair, not a pure interaction effect, while partial dependence can evaluate unrealistic combinations when features are correlated. Importance shows predictive association, not causation or whether a feature raises or lowers price. The cross-validation results come from one Ames Housing sample, and `test.csv` has no sale prices for independent evaluation.
