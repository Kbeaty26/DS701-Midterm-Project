# Project Questions: Ames Housing

## Question 1: Can property characteristics predict sale prices more accurately than neighborhood-level price estimates?

**Decision-maker:** Real estate investors and property assessors could use price estimates to identify properties that warrant further valuation or investment review.

## Question 2: Do homes form distinct property segments based on their physical characteristics?

**Decision-maker:** Real estate analysts and developers could use property segments to identify comparable homes and investigate potential investment or renovation opportunities.

## Question 3: Which property characteristics are consistently associated with sale price?

**Decision-maker:** Homeowners and real estate investors could use the findings to prioritize which property features to investigate when evaluating purchases or renovations.

## Validation plan
### Validation Plan

We will use the provided Ames Housing training and test files. The test set will remain untouched throughout model development and will not influence feature selection, preprocessing decisions, hyperparameter tuning, or the number of clusters. We will use five-fold cross-validation on the provided training set to select supervised models and estimate predictive performance variability. All preprocessing steps will be fitted within each training fold to prevent data leakage.

**Question 1:** We will compare a neighborhood-median baseline with a supervised regression model using five-fold cross-validation. Neighborhood medians will be calculated using only the training portion of each fold. We will compare MAE, RMSE, and R² across folds and report their means and standard deviations.

**Question 2:** We will fit K-means clustering using physical property characteristics, excluding sale price and identifiers. We will select the number of clusters using training-only silhouette scores, cluster sizes, and interpretability. We will assess stability through repeated resampling and adjusted Rand index (ARI), and examine whether the resulting clusters remain meaningful under reasonable preprocessing alternatives.

**Question 3:** We will reuse the supervised model from Question 1 to assess feature importance. We will compare coefficients and/or permutation importance across cross-validation folds to identify consistently important features. We will interpret these as predictive associations rather than causal effects.

After all modeling and analysis choices are frozen, we will evaluate the final supervised model on the provided test set only if it contains observed sale prices and an appropriate evaluation procedure is available. If the provided test set is unlabeled, final supervised evaluation will rely on the cross-validation results from the labeled training set, and this limitation will be stated explicitly.