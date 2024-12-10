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


### NMAR Analysis 

In our examination of power outage data, we found that the column `CUSTOMERS.AFFECTED` (with 42.7% missing values) is likely NMAR (Not Missing At Random). This conclusion stems from understanding the data generation process rather than just the data itself:

- The data comes from various utility companies across different states, each with their own reporting systems and capabilities
- Missing customer counts might be due to:
  - Limited tracking capabilities during major outages
  - Different reporting standards across companies
  - Emergency situations where customer tracking wasn't feasible

To potentially make this column MAR (Missing At Random), we would need additional data about:
1. Individual utility companies' reporting capabilities
2. Emergency response protocols for different types of outages
3. Historical reporting compliance records

### Missisgness Dependency 

We investigated the missingness patterns in our dataset, focusing on the `CUSTOMERS.AFFECTED` column which shows substantial missingness (42.7%). Our analysis tested whether this missingness depends on other variables in the dataset.

We conducted a thorough analysis of missingness patterns in our dataset, particularly focusing on the relationships between different variables. Our analysis revealed:

#### 1. **Highest Missing Percentages:**

To understand our missingness analysis, let's first look at the overall missingness patterns in our dataset:

<iframe
  src="assets/missingness_heatmap.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


<iframe
  src="assets/missingness_bars.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

These visualizations show that `DEMAND.LOSS.MW` (58.74%) and `CUSTOMERS.AFFECTED` (42.7%) have the highest rates of missing values, while most other variables have very low missingness rates (< 10%).


#### 2. **Missingness Dependency Tests**

We performed permutation tests to understand what factors the missingness of `CUSTOMERS.AFFECTED` depends on. Our analysis revealed:

 **Dependency on OUTAGE.DURATION**:
   - **Null Hypothesis**: The distribution of outage duration is the same whether CUSTOMERS.AFFECTED is missing or not
   - **Alternative Hypothesis**: The distribution of outage duration differs based on whether CUSTOMERS.AFFECTED is missing
   - Test Statistic: Difference in means

<iframe
  src="assets/missingness_duration.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/missingness_permutation.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

   - **P-value**: 0.0010 (< 0.05)

   The permutation test visualization shows our observed test statistic (1243.53) is far from what we would expect under the null hypothesis, providing strong evidence for dependency. The distribution plots further illustrate how outage durations differ between cases where customer counts are missing versus reported.

   - **Conclusion**: We reject the null hypothesis; the missingness of CUSTOMERS.AFFECTED depends on OUTAGE.DURATION
  

**Dependency on MONTH**:
   - **Null Hypothesis**: The distribution of months is the same whether CUSTOMERS.AFFECTED is missing or not
   - **Alternative Hypothesis**: The distribution of months differs based on whether CUSTOMERS.AFFECTED is missing
   - **Test Statistic**: Difference in means
   - **P-value**: 0.0000 (< 0.05)
   - **Conclusion**: We reject the null hypothesis; the missingness of CUSTOMERS.AFFECTED also depends on MONTH

Based on our p-values, we reject both null hypotheses, concluding that the missingness of CUSTOMERS.AFFECTED depends on both OUTAGE.DURATION and MONTH.

 These findings suggest that the missingness in our customer impact data is not random but is systematically related to both the duration of outages and the time of year, which has important implications for our analysis of power outage patterns.



---

## Hypothesis Testing


---