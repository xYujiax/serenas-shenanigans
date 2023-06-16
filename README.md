# How long was your power outage?


---
### Note: My exploratory data analysis of the power outage dataset used in this project can be found [here](https://www.youtube.com/watch?v=dQw4w9WgXcQ).

## Framing the Problem 

I would like to explore using regression to predict the severity of major power outages. More specifically, my aim is to predict OUTAGE.DURATION (the duration of a power outage in minutes); I chose this as my response variable under the assumption that more severe outages typically last longer, so I would like to use power outage duration to express power outage severity. 

Regarding my models, I will be using Root Mean Squared Error (RMSE) as the metric to evaluate their effectiveness. I chose this metric over R² (another popular metric for regression models) because R² typically increases when more features are added, regardless of whether they actually have substantial predictive power. This is because additional features have the potential to explain additional variance in the dependent variable, thereby resulting in a higher R². However, this would not necessarily indicate that my model has improved if I try adding multiple new features, so I will stick with RMSE to better see how far my model's predictions are from reality.

Because I am trying to predict OUTAGE.DURATION from the rest of my data, I won't use OUTAGE.DURATION as a feature for training my model because I'm not supposed to know that information at the time of prediction. For the same reason, I will also be excluding OUTAGE.RESTORATION.TIME and OUTAGE.RESTORATION.DATE from being potential features. I will however, consider OUTAGE.MONTH and OUTAGE.YEAR as potential features since I'm only predicting how long an outage lasts, not *when* it occurs.



---

## Baseline Model

To predict OUTAGE.DURATION, I decided to use a Decision Tree Regression model. This is a regression model that splits data from features into a tree-like structure, where each leaf node represents a prediction of the response variable. The following table indicates the types of features I used for my baseline model:

| Nomainal Features   | Quantitative Features   |
|:--------------------|:------------------------|
| POSTAL.CODE         | ANOMALY.LEVEL           |
| CLIMATE.CATEGORY    | MONTH                   |
| CAUSE.CATEGORY      | YEAR                    |

I performed One Hot Encoding on my nominal variables to convert them to a numeric format that I could use for regression. The remaining quantitative variables were directly passed into my model without any transformation.

Using cross-validation to test this baseline model resulted in an RMSE of a whopping **3225 minutes**. An interpretation of this is that on average, the predicted outage durations from my model deviate from the actual outage duration by ≈ 3225 minutes -- that's more than 2 days! Considering that the entire dataset's average power outage duration is 2625 minutes, this puts into perspective how the current model is not very good at providing accurate predictions. 



---

## Final Model

To improve upon my baseline model, I engineered two new features: (imputed) CUSTOMERS.AFFECTED and (standardized) ANOMALY.LEVEL

I considered adding CUSTOMERS.AFFECTED as a new feature because power outages that affect many customers could be seen as more "severe". This gave the possibility that more severe power outages that were longer-lasting had some kind of correlation with power outages affecting more customers. However, since there were several missing CUSTOMERS.AFFECTED entries which were most likely Missing At Random (MAR), I decided to perform *conditional mean imputation* on missing CUSTOMERS.AFFECTED entries by grouping data based on their POSTAL.CODE variable (eg. CA, NV, AZ, etc.). I did this  under the assumption that outages occurring in similar areas affected a similar number of customers. Unfortunately, ALL outage data for North and South Dakota were missing CUSTOMERS.AFFECTED values, so I instead performed regular *mean imputation* on those entries by using the average of all CUSTOMERS.AFFECTED data. These transformations were all done by implementing a custom imputation transformer.


I considered standardizing my remaining quantitative feature, ANOMALY.LEVEL (representing Oceanic Niño Index (ONI) scores), to create a new feature ensuring equal importance and comparability among my quantitative features. While this isn't necessary for Decision Tree Regression, in the case that I continue to add more quantitative features or try yet another regression model, having my features standardized would prevent variables with larger scales from dominating. 


For my final Decision Tree Regression model, I utilized GridSearchCV to help me choose an optimal max_depth hyperparameter that would limit the number of splits in my tree. Ultimately, a max_depth of 3 performed the best and allowed my model to generalize better on unseen data without overfitting to my training data. My final model's RMSE was **2748 minutes**. Although this is still a fairly large amount of error, it's a rougly 15% improvement from my baseline model's RMSE of 3225 minutes.



---

## Fairness Analysis

Given that almost half of all CAUSE.CATEGORY (cause of a power outage) entries in the power outage dataset are attributed to "severe weather", *Does my final model perform worse for outages NOT caused by severe weather than outages caused by severe weather?*

**Null Hypothesis:** My final model is fair -- outage duration predictions for outages caused by severe weather and outages caused by something else are roughly the same, and any differences are likely due to random chance.

**Alternate Hypothesis:** My final model isn't fair -- outage duration predictions for outages not caused by severe weather are lower than duration predictions for outages caused by severe weather.

**Evaluation Metric:** Root Mean Squared Error (RMSE)

**Test Statistic:** Difference in RMSE (Severe - Not Severe)

**Significance Level**: 0.05
<iframe src="ass/hyp.html" width=800 height=600 frameBorder=0></iframe>


**Resulting P-Value:** 0.35

**Conclusion:** The null hypothesis has NOT been rejected at the 0.05 significance level, so it is likely that power outage durations which are predicted using my final model are similar regardless of whether an outage is caused by severe weather or not. 





