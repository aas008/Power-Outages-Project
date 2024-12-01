# Data-Driven Analysis of Major U.S. Power Outages

by Aanya Sharma & Nishant Begani

---

## Introduction

What happens when the lights go out for over 50,000 people at once? Between January 2000 and July 2016, the United States experienced numerous major power outages that significantly impacted communities across the country. This analysis delves into a comprehensive dataset from the Department of Energy that tracks these major disruptions, defined as outages affecting at least 50,000 customers or causing an unplanned loss of at least 300 megawatts of energy.

Our investigation centers around a critical question: **How do climate patterns and regional characteristics influence the severity of power outages, and can we predict their duration to improve emergency response planning?** Understanding these patterns is crucial for utility companies, emergency responders, and policymakers to better prepare for and mitigate the impact of future outages.

The dataset, sourced from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure, contains 1,534 major outage events. For each event, we analyze several key variables:

| Column | Description |
|--------|-------------|
| `'YEAR'` | Year an outage occurred |
| `'MONTH'` | Month an outage occurred |
| `'U.S._STATE'` | State the outage occurred in |
| `'NERC.REGION'` | North American Electric Reliability Corporation (NERC) regions involved in the outage event |
| `'CLIMATE.REGION'` | U.S. Climate regions as specified by National Centers for Environmental Information (9 Regions) |
| `'ANOMALY.LEVEL'`| Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season |
| `'CAUSE.CATEGORY'` | Categories of all the events causing the major power outages |
| `'OUTAGE.DURATION'` | Duration of outage events (in minutes) |
| `'DEMAND.LOSS.MW'` | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported] |
| `'CUSTOMERS.AFFECTED'` | Number of customers affected by the power outage event |
| `'TOTAL.PRICE'` | Average monthly electricity price in the U.S. state (cents/kilowatt-hour) |
| `'TOTAL.SALES'` | Total electricity consumption in the U.S. state (megawatt-hour) |
| `'TOTAL.CUSTOMERS'` | Annual number of total customers served in the U.S. state |

Through our analysis, we aim to build a predictive model that can estimate the duration of power outages based on initial conditions and regional characteristics. By analyzing factors such as climate patterns, geographic location, and regional infrastructure, we will attempt to predict how long an outage might last when it first occurs. This prediction capability could be invaluable for:

- Emergency Response Teams: Better resource allocation and response planning
- Utility Companies: More accurate customer communication and restoration timelines
- Local Governments: Improved disaster preparedness and mitigation strategies

Whether you're a utility company planning infrastructure improvements, an emergency response team preparing for future events, or a concerned citizen wanting to understand power grid vulnerabilities, this analysis provides valuable insights into the patterns and predictability of major power disruptions.

---

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning Process

Our initial dataset required several preprocessing steps to prepare it for analysis. Here are the key cleaning steps we performed:

1. **Date and Time Processing**
   - Combined separate date and time columns into unified datetime objects
   - Created two new columns: `OUTAGE.START` and `OUTAGE.RESTORATION` from their respective date and time components
   - Removed the original separate date/time columns to avoid redundancy

2. **Column Selection**
   We focused on these key features for our analysis:
   - Time-related: `YEAR`, `MONTH`, `OUTAGE.START`, `OUTAGE.RESTORATION`
   - Location-based: `U.S._STATE`, `NERC.REGION`, `CLIMATE.REGION`
   - Climate-related: `CLIMATE.CATEGORY`, `ANOMALY.LEVEL`
   - Impact metrics: `CAUSE.CATEGORY`, `OUTAGE.DURATION`, `DEMAND.LOSS.MW`, `CUSTOMERS.AFFECTED`
   - Economic indicators: `TOTAL.PRICE`, `TOTAL.SALES`, `TOTAL.CUSTOMERS`

3. **Missing Value Treatment**
   - Converted zero values to `NaN` in key metrics where zero was logically impossible:
     - `OUTAGE.DURATION`
     - `DEMAND.LOSS.MW`
     - `CUSTOMERS.AFFECTED`
   - This decision was made because major outages (affecting 50,000+ customers) would not have true zero values for these metrics

4. **Data Type Conversions**
   - Ensured numeric columns had proper data types using `pd.to_numeric`
   - Applied this to columns such as:
     - `ANOMALY.LEVEL`
     - `OUTAGE.DURATION`
     - `DEMAND.LOSS.MW`
     - `CUSTOMERS.AFFECTED`
     - `TOTAL.PRICE`
     - `TOTAL.SALES`
     - `TOTAL.CUSTOMERS`

Here's a glimpse of our cleaned DataFrame:

### EDA

### Univariate Analysis

Our univariate analysis revealed several interesting patterns in the power outage data:

<iframe
  src="assets/climate_region_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Looking at the distribution of outages across climate regions, we can see that the Northeast region experienced the highest number of major outages during this period, followed by the South and West regions. This suggests that certain geographic and climatic factors may contribute to outage frequency.

<iframe
  src="assets/outage_causes_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/geographic_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

-------- This is the research question one -------------

<iframe
  src="assets/climate_category_dist2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


### Bivariate Analysis

To understand relationships between different factors affecting outages:

<iframe
  src="assets/duration_vs_anomaly.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The relationship between outage duration and number of customers affected shows a positive correlation, though with significant variation. Notably, severe weather events (shown in blue) tend to result in both longer durations and higher numbers of affected customers.

<iframe
  src="assets/duration_vs_customers.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/duration_by_climate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/duration_by_month_climate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

-------- This is the research question one -------------
<iframe
  src="assets/duration_by_climate_category.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/duration_by_month_climate_category.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>



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