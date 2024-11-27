# Decoding Power Grid Disruptions: A Data-Driven Analysis of Major U.S. Power Outages

by Aanya Sharma & Nishant Begani

This is a project for our Data Science Class at UCSD (DSC 80), where we work with a Power Outage dataset and do hypothesis testing and make insightful predictions.

---

## Introduction

What happens when the lights go out for over 50,000 people at once? Between January 2000 and July 2016, the United States experienced numerous major power outages that significantly impacted communities across the country. This analysis delves into a comprehensive dataset from the Department of Energy that tracks these major disruptions, defined as outages affecting at least 50,000 customers or causing an unplanned loss of at least 300 megawatts of energy.

Our investigation centers around a critical question: **How do climate patterns and regional characteristics influence the severity of power outages, and can we predict their duration to improve emergency response planning?** Understanding these patterns is crucial for utility companies, emergency responders, and policymakers to better prepare for and mitigate the impact of future outages.

The dataset, sourced from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure, contains 1,534 major outage events. For each event, we analyze several key variables:

| Category | Variables |
|----------|-----------|
| Timing | Year, Month, Start/End Dates and Times |
| Location | State, NERC Region, Climate Region |
| Climate | Anomaly Level, Climate Category |
| Impact | Outage Duration, Customers Affected, Demand Loss |
| Regional Context | Total Customers, Total Price, Total Sales |

This data allows us to explore how different factors contribute to outage severity and duration. Whether you're a utility company planning infrastructure improvements, an emergency response team preparing for future events, or a concerned citizen wanting to understand power grid vulnerabilities, this analysis provides valuable insights into the patterns and predictability of major power disruptions.

---

## Cleaning and EDA

<iframe src="assets/10-80-enrollment.html" width=800 height=600 frameBorder=0></iframe>

---

## Assessment of Missingness

Here's what a Markdown table looks like. Note that the code for this table was generated _automatically_ from a DataFrame, using

```py
print(counts[['Quarter', 'Count']].head().to_markdown(index=False))
```

| Quarter     |   Count |
|:------------|--------:|
| Fall 2020   |       3 |
| Winter 2021 |       2 |
| Spring 2021 |       6 |
| Summer 2021 |       4 |
| Fall 2021   |      55 |

---

## Hypothesis Testing


---