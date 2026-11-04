### Alexa Slaughter — Contributions

**Exploratory analysis (EDA notebook, Question 1 section)**
- Engineered exploratory features (`TotalSF`, `TotalBath`, `HouseAgeAtSale`), filled categorical NAs as "None" (feature absent), and created quality and size tiers.
- Measured price variability within each neighborhood (std / median) to show where a single neighborhood median describes homes poorly.
- Built a heatmap of median price by neighborhood × quality tier, showing that quality moves price substantially even within the same neighborhood.
- Plotted each home's size and price relative to its own neighborhood median, isolating what property characteristics add on top of location.
- Quantified the neighborhood-median baseline error (distribution of % difference from neighborhood median, median absolute % error, share of homes missed by >20%).
- Examined lot shape vs. sale price, and checked whether the gap is explained by lot size, house size, and quality.

**Modeling (Question 1 notebook)**
- Built the modeling feature set: ordinal quality encoding (absent = 0), missingness treated as information, engineered TotalSF/TotalBath/HouseAge/YearsSinceRemodel, and removed location and sale-transaction fields.
- Implemented 5-fold cross-validation with all preprocessing and neighborhood medians fitted inside each training fold to prevent leakage.
- Compared overall-median and neighborhood-median baselines with Ridge regression and gradient boosting, with and without location features.
- Ran honesty checks: fold-by-fold paired comparison, diagnosis of the outlier driving Ridge's error (Id 1299), sensitivity analysis excluding homes over 4,000 sq ft, and error analysis by neighborhood.
- Wrote the Question 1 report section and created its figure.

### Katherine Beaty - Contributions

**Exploratory analysis (EDA notebook, Question 2 section)**
- Selected six physical features (GrLivArea, OverallQual, YearBuilt, TotalBsmtSF, GarageCars, LotArea) for clustering, checked their summary statistics and missing values, and plotted their distributions.
- Plotted living area vs. year built colored by overall quality, showing that size, age and quality move together.
- Built a pairplot and a correlation heatmap of the selected features to check how related they are.
- Compared living area, basement area, garage capacity and year built across overall quality ratings with boxplots.
- Built a full numeric correlation heatmap to identify redundant features, which informed the reduced feature set in the modeling notebook.
- Created HouseAgeAtSale and YearsSinceRemodel and plotted each against sale price.
- Checked SalePrice, GrLivArea and LotArea for outliers with boxplots.

**Modeling (clustering notebook)**
- Built the clustering feature set from physical characteristics only (excluded SalePrice and Neighborhood), with median imputation and standardization.
- Chose the number of clusters for K-means (k=2 to 8) using elbow and silhouette plots.
- Run 1 (18 features, k=3): profiled the clusters and named them: spacious high-quality two-story homes, newer high-quality ranch-style homes, and older, smaller, lower-quality homes.
- Run 2 (15 features, k=2): removed correlated features (1stFlrSF, 2ndFlrSF, GarageArea) and reran, giving the highest silhouette of any configuration (0.227). The two clusters were older, modest homes and newer, larger, higher-quality homes.
- Visualized clusters by year built and living area, and compared sale price by cluster after clustering (price was not used to build the clusters).
- Ran a stability check with the Adjusted Rand Index (ARI) between runs: 0.94 at k=2 and 0.62 at k=3, showing the two-cluster split is robust to feature choice and the three-cluster split is more sensitive.
- Wrote the clustering results section, covering the cluster naming tables, the comparison between runs, practical uses for agents, and limitations and next steps.

### Rachel Olson - Contributions

**Exploratory analysis (EDA notebook, question 3 section)**
- Compared sale prices across building types and house styles, including category counts and median prices to account for groups with few sales.
- Examined sale-price distributions by kitchen quality, heating quality, and central air, preserving the ordered quality categories from the data.
- Compared basement finish and garage finish categories, treating missing values as "None" to represent homes without those features.
- Calculated total bathroom count, including half baths, and compared its distribution with sale price.
- Compared sale prices for homes with and without decks and porch types, summarizing group sizes and medians.

**Modeling (Question 3 notebook)**
- Built a random forest model using property characteristics, excluding identifiers, location, and sale-transaction fields, and evaluated it with 5-fold cross-validation.
- Kept imputation, categorical encoding, and all other preprocessing inside each training fold to prevent data leakage.
- Measured permutation importance on each held-out fold and summarized feature importance by average score and consistency across folds.
- Compared three feature pairs (TotalSF × OverallQual, TotalSF × GarageCars, and HouseAge × OverallQual) using joint permutation importance and two-way partial-dependence surfaces.
- Found that total floor area and overall quality were the strongest individual predictors, and that their combination showed the clearest non-additive pattern among the pairs examined.
- Wrote the Question 3 report section, including the model results, interpretation, and limitations.
