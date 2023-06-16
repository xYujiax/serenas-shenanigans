# How long was your power outage?


---

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
|                     | YEAR                    |

I performed One Hot Encoding on my nominal variables (POSTAL.CODE and CLIMATE.CATEGORY) to encode them into a numeric format that I can use for regression. The remaining quantitative variables were directly passed into my model without any transformation.

Using cross-validation to test this baseline model resulted in an RMSE of a whopping **3225 minutes**. An interpretation of this is that on average, the predicted outage durations from my model deviate from the actual outage duration by ≈ 3225 minutes -- that's more than 2 days! Considering that the entire dataset's average power outage duration is 2625 minutes, this puts into perspective how the current model is not very good at providing accurate predictions. 



---

## Final Model

To improve upon my baseline model, I engineered two new features: (imputed) CUSTOMERS.AFFECTED and (standardized) ANOMALY.LEVEL

I considered adding CUSTOMERS.AFFECTED as a new feature because power outages that affect many customers could be seen as more "severe". This gave the possibility that more severe power outages that were longer-lasting had some kind of correlation with power outages affecting more customers. However, since there were several missing CUSTOMERS.AFFECTED entries which were most likely Missing At Random (MAR), I decided to perform *conditional mean imputation* on missing CUSTOMERS.AFFECTED entries by grouping data based on their POSTAL.CODE variable (eg. CA, NV, AZ, etc.). I did this  under the assumption that outages occurring in similar areas affected a similar number of customers. Unfortunately, ALL outage data for North and South Dakota were missing CUSTOMERS.AFFECTED values, so I instead performed regular *mean imputation* on those entries by using the average of all CUSTOMERS.AFFECTED data. These transformations were all done by implementing a custom imputation transformer.


I considered standardizing ANOMALY.LEVEL (representing )



1. State the features you added and why they are good for the data and prediction task. Note that you can’t simply state “these features improved my accuracy”, since you’d need to choose these features and fit a model before noticing that – instead, talk about why you believe these features improved your model’s performance from the perspective of the data generating process.

2. Describe the modeling algorithm you chose, the hyperparameters that ended up performing the best, and the method you used to select hyperparameters and your overall model. Describe how your Final Model’s performance is an improvement over your Baseline Model’s performance.

### NMAR Analysis

I do not believe any of the missing data is not missing at random (NMAR). This is because most of the missing data can be inferred from other columns in the data. For example, entries in the HURICANE.NAMES column are missing whenever a power outage is not caused by a hurricane, so entries in this column are probably missing by design. Other missing entries are more likely to be MAR rather than NMAR. This can be seen with missing OUTAGE.START.TIME entries. If an outage occurred because of equipment failure or severe weather, it might've been difficult to record the exact time of outage, so the missingness of OUTAGE.START.TIME could depend on CAUSE.CATEGORY.

### Missingness Dependency

In order to better understand what characteristics are associated with different categories of cause, it may be helpful to explore the CAUSE.CATEGORY.DETAIL column to determine how likely its missingness is dependent on the CAUSE.CATEGORY column.

**Null Hypothesis:** The missingness of CAUSE.CATEGORY.DETAIL is NOT dependent on CAUSE.CATEGORY.

**Alternate Hypothesis:** The missingness of CAUSE.CATEGORY.DETAIL IS dependent on CAUSE.CATEGORY.

To test whether or not to reject the Null Hypothesis, I calculated the observed Total Variation Distance (TVD) of the data. Then, I shuffled the values in the CAUSE.CATEGORY.DETAIL and calculated the shuffled TVD 1000 times to create the empirical distribution below. Because the observed TVD is much greater than 95% of the empirical TVDs I simulated, I reject my Null Hypothesis in favor of the Alternate that suggests the missingness of CAUSE.CATEGORY.DETAIL is probably dependent on CAUSE.CATEGORY. 

A plausible interpretation for why the the missingness of a cause category's details is dependent on the kind of cause category an outage has could be made by looking at how cause categories are split into seven categories: severe weather, intentional attack, system operability disruption, equipment failure, public appeal, fuel supply emergency, and islanding. For more vague categories like public appeal and intentional attack, it might be difficult to come up with extra details, so those in charge of data collection might not bother to specify any details in CAUSE.CATEGORY.DETAIL and opt to leave it blank instead.
<iframe src="ass/miss1.html" width=800 height=600 frameBorder=0></iframe>
I conducted the same kind of test to determine whether CAUSE.CATEGORY.DETAIL and OUTAGE.START.TIME and concluded that it is unlikely that CAUSE.CATEGORY.DETAIL is MAR based on OUTAGE.START.TIME. This is helpful in understanding that the start time of an outage does not really have any bearing over whether or not the cause category details will be disclosed for a power outage.



---

## Fairness Analysis

To help answer our overarching question, I will conduct the following hypothesis test to better determine whether the frequency of power outages caused by different categories of cause have drastically changed in 2015 & 2016 compared to other years (since data only goes up to July 2016, I combined 2016 with 2015 data).

**Null Hypothesis:** The proportion of power outages caused by each category of cause in 2015 & 2016 combined is similar to the overall proportion of outages caused by every category of cause recorded from 2000-2016.

**Alternate Hypothesis:** The proportion of power outages caused by each category of cause in 2015 & 2016 combined is NOT similar to the overall proportion of outages caused by every category of cause recorded from 2000-2016.

**Observe:** The distribution of power outages caused by each category of cause in 2015 & 2016 (severe weather, intentional attack, system operability disruption, equipment failure, public appeal, fuel supply emergency, and islanding)
<iframe src="ass/hyp.html" width=800 height=600 frameBorder=0></iframe>

**Test Statistic:** Total Variation Distance (TVD)

**Significance Level**: 0.01

**Resulting P-Value:** 2 x 10<sup>-5</sup>

**Conclusion:** The null hypothesis may be rejected in favor of the possibility that the distribution of power outages caused by each category of cause in more recent years (2015 & 2016) is different from the overall distribution of power outages caused by each category from 2000-2016. 


