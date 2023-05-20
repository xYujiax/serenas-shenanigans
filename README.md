# What's the culprit behind your blackout?



---

## Introduction 

In this project, we'll explore this [dataset](https://www.sciencedirect.com/science/article/pii/S2352340918307182) containing 1534 rows of data pertaining to major power outages in the continental U.S. from January 2000 to July 2016. Some relevant columns we will be exploring for this analysis are included in the following table:

| Column                 | Description                                                                                                                                                                                                                      |
|:-----------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OUTAGE.DURATION        | Duration of outage events (in minutes)                                                                                                                                                                                          |
| OUTAGE.START.TIME      | Time of the day when the outage event started (as reported by the corresponding Utility in the region)                                                                                                                         |
| CAUSE.CATEGORY         | Categories of all the events causing the major power outages                                                                                                                                                                     |
| CAUSE.CATEGORY.DETAIL  | Detailed description of the event categories causing the major power outages                                                                                                                                                    |
| CLIMATE.CATEGORY       | Climate episodes corresponding to the years. The categories—“Warm”, “Cold” or “Normal” episodes of the climate are based on a threshold of ± 0.5 °C for the Oceanic Niño Index (ONI)                                          |
| HURRICANE.NAMES        | If the outage is due to a hurricane, then the hurricane name is given by this variable                                                                                                                                          |
| YEAR                   | Year when the outage event occurred                                                                                                                                                                                             |                                  |


Information from the above columns will be used to help us analyze the following question: **What characteristics are associated with different categories of cause, and how have these changed over time?**
This is particularly relevant to us (especially those of us living in the U.S.) because finding trends and understanding the characteristics of causes of power outages may help us predict the behavior of future power outages. 



---

## Cleaning and EDA

### Data Cleaning

There were some columns in the excel that contained notes about the sheet and noted the units of each variable. These were made for improved readibility but could not be used in actual data collection and analysis, so I excluded these from my dataframe before applying analysis. Additionally, I combined OUTAGE.START.DATE and OUTAGE.START.TIME into one timestamp in the column OUTAGE.START. The same was done to create the OUTAGE.RESTORATION column based on outage restoration date and time. This was done to compute 

### Univariate Analysis

The following is a univariate bar chart listing the different categories of power outage causes and the percent of outages they contribute to. The prime contributer to power outages appears to be severe weather.
<iframe src="ass/eda1.html" width=800 height=600 frameBorder=0></iframe>


### Bivariate Analysis

To explore the bivariate relationship between power outage causes and average power outage duration, we can look at this following bar chart. The chart suggests that on average, the longest power outages appear to be caused by fuel supply emergencies. 
<iframe src="ass/eda2.html" width=800 height=600 frameBorder=0></iframe>


### Interesting Aggregate

This table shows an aggregation of power outages based on the CLIMATE.CATEGORY column corresponding to whether an outage occurs during a cold, normal, or warm climate episode (based on the Oceanic Niño Index). The Mean column represents the average number of outages that occur within each Climate Category. This aggregation is potentially useful in exploring whether a certain climate type has some affect over the number of power outages that occur.


| Climate Category   |    Mean |
|:-------------------|--------:|
| cold               | 753.262 |
| normal             | 759.289 |
| warm               | 804.055 |



---

## Assessment of Missingness

### NMAR Analysis

I do not believe any of the missing data is not missing at random (NMAR). This is because most of the missing data can be inferred from other columns in the data. For example, entries in the HURICANE.NAMES column are missing whenever a power outage is not caused by a hurricane, so entries in this column are probably missing by design. Other missing entries are more likely to be MAR rather than NMAR. This can be seen with missing OUTAGE.START.TIME entries. If an outage occurred because of equipment failure or severe weather, it might've been difficult to record the exact time of outage, so the missingness of OUTAGE.START.TIME could depend on CAUSE.CATEGORY.

### Missingness Dependency

In order to better understand what characteristics are associated with different categories of cause, it may be helpful to explore the CAUSE.CATEGORY.DETAIL column to determine how likely its missingness was dependent on the CAUSE.CATEGORY column. The CAUSE.CATEGORY.DETAIL column provides extra details on the cause of a power outage (the CAUSE.CATEGORY column).

Null Hypothesis: The missingness of CAUSE.CATEGORY.DETAIL is NOT dependent on CAUSE.CATEGORY.

Alt Hypothesis: The missingness of CAUSE.CATEGORY.DETAIL IS dependent on CAUSE.CATEGORY.

To test whether or not to reject the Null Hypothesis, I calculated the observed Total Variation Distance (TVD) of the data. Then, I shuffled the values in the CAUSE.CATEGORY.DETAIL and calculated the shuffled TVD 1000 times to create the empirical distribution below. Because the observed TVD is much greater than 95% of the empirical TVDs I simulated, I reject my Null Hypothesis in favor of the Alternate that suggests the missingness of CAUSE.CATEGORY.DETAIL is probably dependent on CAUSE.CATEGORY. 

A plausible interpretation for why the the missingness of a cause category's details is dependent on the kind of cause category an outage has could be made by looking at how cause categories are split into seven categories: severe weather, intentional attack, system operability disruption, equipment failure, public appeal, fuel supply emergency, and islanding. For more vague categories like public appeal and intentional attack, it might be difficult to come up with extra details, so those in charge of data collection might not bother to specify any details in CAUSE.CATEGORY.DETAIL and opt to leave it blank instead.
<iframe src="ass/miss1.html" width=800 height=600 frameBorder=0></iframe>
I conducted the same kind of test to determine whether CAUSE.CATEGORY.DETAIL and OUTAGE.START.TIME and concluded that it is unlikely that CAUSE.CATEGORY.DETAIL is MAR based on OUTAGE.START.TIME. This is helpful in understanding that the start time of an outage does not really have any bearing over whether or not the cause category details will be disclosed for a power outage.



---

## Hypothesis Testing

To help answer some aspects of my question, I will conduct the following hypothesis test to allow me to better determe whether the frequency of power outages caused by different categories of cause have drastically changed in 2016 compared to other years.

Null Hypothesis: The proportion of power outages caused by each category of cause is in 2016 is similar to the overall proportion of outages caused by every category of cause recorded from 2000-2016.

Alternate Hypothesis: The proportion of power outages caused by each category of cause in 2016 is NOT similar to the overall proportion of outages caused by every category of cause recorded from 2000-2016.

Observe: The distribution of power outages caused by each category of cause in 2016 (severe weather, intentional attack, system operability disruption, equipment failure, public appeal, fuel supply emergency, and islanding)
<iframe src="ass/hyp.html" width=800 height=600 frameBorder=0></iframe>

Test Statistic: Total Variation Distance

Significance Level: 0.01

Resulting P-Value: 0.0

Conclusion: The null hypothesis may be rejected in favor of the possibility that the distribution of power outages caused by each category of cause in 2016 is different from the overall distribution of power outages caused by each category from 2000-2016. HOWEVER, there wasn't much data for 2016 specifically, so the statistical test may have very skewed results.


