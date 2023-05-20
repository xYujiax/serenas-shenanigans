## What's the culprit behind your blackout?

---

### Introduction

In this project, we studied the effectiveness of spice challenges in building team morale.

---

### Cleaning and EDA

There were some columns in the excel that contained notes about the sheet and noted the units of each variable. These were made for improved readibility but could not be used in actual data collection and analysis, so I excluded these from my dataframe before applying analysis. Additionally, I combined 'OUTAGE.START.DATE' and 'OUTAGE.START.TIME' into one timestamp in the column 'OUTAGE.START'. The same was done to create the 'OUTAGE.RESTORATION' column based on outage restoration date and time.

#### Univariate Analysis

The following is a univariate bar chart listing the different categories of power outage causes and the percent of outages they contribute to. The prime contributer to power outages appears to be severe weather.
<iframe src="ass/eda1.html" width=800 height=600 frameBorder=0></iframe>


#### Bivariate Analysis

To explore the bivariate relationship between power outage causes and average power outage duration, we can look at this following bar chart. The chart suggests that on average, the longest power outages appear to be caused by fuel supply emergencies. 
<iframe src="ass/eda2.html" width=800 height=600 frameBorder=0></iframe>


Continuing with bivariate exploration, the following line plot includes the three overall most common power outage causes (severe weather, intentional attack, and system operability disruption), and it shows the proportion of power outages they cause each year from 2000-2016. The graph suggests there may have been a decrease in the proportion of outages caused by severe weather and an increase in the proportion of outages caused by intentional attacks.
<iframe src="ass/eda3.html" width=800 height=600 frameBorder=0></iframe>




---

### Assessment of Missingness

Here's what a Markdown table looks like. Note that the code for this table was generated _automatically_ from a DataFrame, using

```py
print(climate.head().to_markdown(index=False))
```

| Climate Category   |    Mean |
|:-------------------|--------:|
| cold               | 753.262 |
| normal             | 759.289 |
| warm               | 804.055 |

---

### Hypothesis Testing
