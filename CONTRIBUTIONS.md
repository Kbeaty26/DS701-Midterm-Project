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
