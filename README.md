# Causal Inference Analysis of factors influencing market value of soccer players

*This project is in continuation to the Part 1 of this project - Predicting market value of soccer players ([Repo Link](https://github.com/reojackson31/soccer-players-market-value-prediction))*

## 1. Introduction

In the part 1 of this project, I had created a regression model to predict the market value of soccer players, using a dataset that included various attributes of players. This model provided an initial understanding of the different factors affecting a player's value in the soccer transfer market.

Building upon the insights gained from this initial analysis, in Assignment 2, I am focusing on a specific aspect of player valuation: their contract expiry. The expiration of a player's contract is a crucial point in the transfer market, potentially altering their market value significantly. This stage is critical for strategic negotiations and decisions, as players may seek new opportunities, contract extensions, or even experience a shift in their market appeal.

So, the underlying dynamics at play during this stage offers a strong reasoning to perform causal inference analysis, enabling us to isolate and understand the causal effect of being in the final year of a contract on a player's market value.

## 2. Hypothesis

The core hypothesis driving this causal inference analysis is that "The market value of soccer players is negatively influenced as they enter the final year of their contract". This hypothesis is based on the premise that when a player is in their final year of contract, it would be a more strategic decision for a club to defer offering a contract to a player for a short duration until contract expiry to avoid paying the transfer fee. This anticipation not only pressures the selling club into possibly accepting a lower fee but also introduces a strategic dimension into player valuation, underscoring the relationship between contract duration and market dynamics.

The analysis in this notebook aims to test this hypothesis using causalml to quantify the effect and provide empirical backing to understand the causal impact of contract expiry on a player's market valuation.

## 3. Choosing the Target, Control Features and Treatment

- **Target Variable**: Current market value of a player

- **Control Features**: All other player attributes except market value (target), and treatment

- **Treatment Group**: Players who are in the last year of their contract i.e. contract expiry year = 2024 (treatment=1)

- **Control Group**: Players who have more than 1 year remaining in their contract i.e contract expiry year >=2025 (treatment=0)


## 4. Average Treatment Effect (ATE) using different learners

| Model | Method | ATE Estimate | Lower Bound | Upper Bound |
|-------|--------|--------------|-------------|-------------|
| S-learner | Linear Regression | -0.493 | -0.769 | -0.217 |
| T-learner | XGBoost | -0.387 | -0.502 | -0.273 |
| T-learner | Linear Regression | -0.529 | -0.798 | -0.260 |
| X-learner | XGB with Propensity Score | -0.343 | -0.429 | -0.256 |
| X-learner | Linear Regression with Propensity Score | -0.529 | -0.798 | -0.260 |
| X-learner | XGB without Propensity Score | -0.353 | -0.439 | -0.266 |
| X-learner | Linear Regression without Propensity Score | -0.529 | -0.798 | -0.260 |
| R-learner | XGB with Propensity Score | -0.309 | -0.311 | -0.308 |
| R-learner | Linear Regression with Propensity Score | -0.519 | -0.521 | -0.517 |
| R-learner | XGB without Propensity Score | -0.286 | -0.288 | -0.284 |
| R-learner | Linear Regression without Propensity Score | -0.762 | -0.764 | -0.760 |


### 4.1 Interpretation of ATE Estimates

1. **Interpretation of Negative ATE**: Across all models, the negative ATE estimates suggest that being in the final year of a player's contract tends to decrease their market value. This aligns with the initial hypothesis that players nearing the end of their contracts may be perceived as less valuable due to the potential for clubs to acquire them without a transfer fee in the near future.

2. **Consistency in Linear Regression**: Linear Regression consistently shows more negative ATE estimates across different learners (S, T, X, R), suggesting a stronger effect of being in the final year of a contract on reducing a player's market value compared to models using XGBoost.

3. **Confidence Intervals**: The confidence intervals for R-learner models, especially with XGB, are notably narrower (e.g., -0.311 to -0.308) than those for other models. This indicates a higher level of precision in the R-learner's estimates, possibly due to the model's ability to better handle the complexity of the data or the inclusion of propensity scores.

4. **Effect of Propensity Scores**: For X and R learners, incorporating propensity scores does not uniformly improve the confidence intervals across models. However, it seems to slightly increase the ATE estimate's precision in some cases, such as the X-learner with XGB, suggesting that propensity score adjustment can influence the estimated treatment effect's stability.

5. **Variability in Estimates**: The ATE estimates vary across different models, ranging from -0.762 to -0.286. This variation shows the sensitivity of causal inference results to the choice of model and methodology.

## 5. Individual Treatment Effect (ITE/CATE)

Looking at the Individual Treatment Effect (ITE) shifts the focus from the average impact of the treatment across all players to the impact of the treatment on individual players. This analysis can provide personalized insights, especially for clubs who are targeting to buy specific players. Also, by examining ITE, clubs can identify specific players who are most likely to experience a significant increase in market value as they enter the final year of their contracts. This information can guide negotiations, whether it involves renewing contracts early to avoid market value spikes or waiting to sell players at their peak value.

For example, in Section 7, I have calculated the ITE for one of the most popular players in the world today, K Mbappe, who is in the final year of his contract. The estimated ITE is -0.4928, which represents the predicted change in his market value due to being in the final year of his contract, compared to if he were not in the final year. The negative sign indicates a decrease in market value attributable to this specific condition. This scenario might impact how clubs, including his current club and potential future clubs, approach contract negotiations. The perceived decrease in market value might lead to more cautious offers from other clubs or even his current club, assuming they might acquire or retain him for a lower fee or wage than expected if he had more time left on his contract.

## 6. Feature Importance

![feature_importance](https://github.com/reojackson31/soccer-analytics-causal-inference/assets/148725712/ef1c837f-f79c-4fee-83eb-85990025d27b)

Feature importance scores are calculated using the S-learner with LightGBMRegressor. Analyzing the feature importance can provide valuable insights into what factors most significantly influence the average treatment effect (ATE) on a player's market value, especially in the context of being in the final year of their contract. By looking at the results, we get the following insights:

1. **Overall Player Rating**: The 'overall' rating has the highest importance score (0.695670), indicating it is the most influential factor in determining a player's market value when approaching the end of their contract.

2. **Impact of Player Age**: Following the overall rating, 'age' has the next highest importance score (0.093663). Younger players in the final year of their contract might have higher market values due to their potential for future development, whereas older players may see a decline in value due to approaching career end.

3. **Importance of certain skills**: Attributes like 'passing' (0.031361), 'shooting' (0.020371), 'pace' (0.019074), and 'defending' (0.018560) also contribute to the model's predictions, indicating their importance in deciding a player's value.

4. **Physical Attributes**: 'Weight_kg' (0.016958) and 'height_cm' (0.016932) have similar importance scores, indicating physical attributes also play a role in determining market value, likely due to their impact on a player's suitability for certain positions or styles of play.

5. **Playing country**: An interesting observation from the model is that players playing for clubs in 'England' (0.020333) appear to have a higher influence, possibly because the English Premier League is the most popular league in the world, offering high visibility and marketing potential to players leading to higher market value.

## 7. SHAP Values

![shap_values](https://github.com/reojackson31/soccer-analytics-causal-inference/assets/148725712/0848e095-e9f2-48ee-a406-ba4dd5e19468)

From the SHAP plot, we can get the following insights:

1. **Overall Rating**: The 'overall' rating has the most substantial positive impact on the market value of players. Higher overall ratings are associated with an increase in market value, as indicated by the concentration of points on the right-hand side of the plot.

2. **Age**: The 'age' feature has the next most significant impact, though it shows a mix of positive and negative effects on the market value. This could indicate that younger and older players have different influences on their market value, with possibly younger players tending to increase market value.

3. **Playing in England**: Players who are playing in the English Premier League have a positive impact on market value, suggesting that these players are valued higher in the market.

4. **Work Rates**: Different work rates ('WorkRateAttack_High', 'WorkRateDefense_High', etc.) appear towards the bottom of the plot, indicating that the work rate of players is not considered as much in deciding their market value.

5. **Other Countries and Player Type Features**: Features like 'Italy', 'Portugal', 'Lean', and 'Forward' are at the bottom of the plot, suggesting they have the least impact on the model's predictions. The SHAP values for these features are spread around zero, showing they don't consistently increase or decrease market value across the dataset.

## 8. Conclusions & Next Steps

Through this analysis, we have confirmed our initial hypothesis that players in the final year of their contract tend to experience a decrease in market value, with the effect size estimated to be in the range of 0.3 to 0.7 across various models. By examining feature importance scores and SHAP values, we have identified specific factors that significantly influence a player's market value when they are in their final year. This model provides an initial framework for clubs, agents, and other stakeholders in making informed decisions regarding player transactions, such as buying, selling, or renewing contracts.

The next steps for this analysis would be to analyze the treatment effect as a continuous variable. In this model, we have restricted our approach to a binary treatment, focusing solely on whether a player is in their final year of contract. However, we can analyze the remaining contract years as a continuous variable using causal inference packages like econml, which could provide insights about how different durations until contract expiry can causally affect a player's market value.
