# What's the culprit behind your blackout?

by Serena Xie (scxie@ucsd.edu)


---

## Introduction

In this project, we studied the effectiveness of spice challenges in building team morale.

---

## Cleaning and EDA

The following is a univariate bar chart listing the different categories of power outage causes and the percent of outages they contribute to. The prime contributer to power outages appears to be severe weather.
<iframe src="ass/eda1.html" width=800 height=600 frameBorder=0></iframe>

To explore the bivariate relationship between power outage causes and average power outage duration, we can look at this following bar chart. The chart suggests that on average, the longest power outages appear to be caused by fuel supply emergencies. 

<iframe src="ass/eda2.html" width=800 height=600 frameBorder=0></iframe>


---

## Assessment of Missingness

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

## Hypothesis Testing
