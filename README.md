# Data-Driven Analysis of Major U.S. Power Outages

by Aanya Sharma & Nishant Begani

---

# Introduction

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

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning Process

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

|   YEAR |   MONTH | U.S._STATE   | NERC.REGION   | CLIMATE.REGION     | CLIMATE.CATEGORY   |   ANOMALY.LEVEL | CAUSE.CATEGORY     | OUTAGE.START        | OUTAGE.RESTORATION   |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   TOTAL.PRICE |   TOTAL.SALES |   TOTAL.CUSTOMERS | Month   |
|-------:|--------:|:-------------|:--------------|:-------------------|:-------------------|----------------:|:-------------------|:--------------------|:---------------------|------------------:|-----------------:|---------------------:|--------------:|--------------:|------------------:|:--------|
|   2011 |       7 | Minnesota    | MRO           | East North Central | normal             |            -0.3 | severe weather     | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |              3060 |              nan |                70000 |          9.28 |   6.56252e+06 |       2.5957e+06  | July    |
|   2014 |       5 | Minnesota    | MRO           | East North Central | normal             |            -0.1 | intentional attack | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |                 1 |              nan |                  nan |          9.28 |   5.28423e+06 |       2.64074e+06 | May     |
|   2010 |      10 | Minnesota    | MRO           | East North Central | cold               |            -1.5 | severe weather     | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |              3000 |              nan |                70000 |          8.15 |   5.22212e+06 |       2.5869e+06  | October |
|   2012 |       6 | Minnesota    | MRO           | East North Central | normal             |            -0.1 | severe weather     | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |              2550 |              nan |                68200 |          9.19 |   5.78706e+06 |       2.60681e+06 | June    |
|   2015 |       7 | Minnesota    | MRO           | East North Central | warm               |             1.2 | severe weather     | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |              1740 |              250 |               250000 |         10.43 |   5.97034e+06 |       2.67353e+06 | July    |


## EDA

### **Univariate Analysis**

Our univariate analysis revealed several interesting patterns in the power outage data:

Looking at the distribution of outages across climate regions, we can see that the Northeast region experienced the highest number of major outages during this period, followed by the South and West regions. This suggests that certain geographic and climatic factors may contribute to outage frequency.

<iframe
  src="assets/climate_region_dist.html"
  width="640"
  height="401"
  frameborder="0"
></iframe>

Severe weather dominates as the primary cause with around 700 incidents, while intentional attacks rank second with approximately 400 cases. All other causes, including system disruptions and equipment failures, account for significantly fewer outages, indicating weather resilience should be a key focus for grid improvements.

<iframe
  src="assets/climate_category_dist2.html"
  width="640"
  height="401"
  frameborder="0"
></iframe>

<iframe
  src="assets/geographic_dist_map.html"
  width="1000"
  height="600"
  frameborder="0"
></iframe>

### **Bivariate Analysis**

To understand relationships between different factors affecting outages:

Most outages cluster in the lower ranges (under 20,000 minutes and affecting fewer than 500,000 customers), but notable outliers extend up to 100,000 minutes and affect up to 3 million people. Severe weather events (shown in blue) tend to cause both longer durations and higher customer impact, appearing frequently among the most severe cases.

<iframe
  src="assets/duration_vs_anomaly.html"
  width="640"
  height="401"
  frameborder="0"
></iframe>

While most cause categories show similar median durations, fuel supply emergencies produce the most extreme outliers (up to 100,000 minutes). Severe weather events consistently show longer durations with multiple outliers, while intentional attacks typically resolve more quickly, suggesting better response protocols for deliberate disruptions.

<iframe
  src="assets/duration_by_climate_category.html"
  width="640"
  height="401"
  frameborder="0"
></iframe>

Cold climate episodes show the highest mean outage duration (2,901 minutes) but affect fewer customers on average (1,586K) compared to normal conditions (2,666 minutes, 1,901K customers). This suggests that while cold weather events last longer, they typically impact smaller populations.

<iframe
  src="assets/duration_by_month_climate_category.html"
  width="640"
  height="401"
  frameborder="0"
></iframe>

### **Interesting Aggregates**

Cold climate episodes show the highest mean outage duration (2,901 minutes) but affect fewer customers on average (1,586K) compared to normal conditions (2,666 minutes, 1,901K customers). This suggests that while cold weather events last longer, they typically impact smaller populations.


| CLIMATE.CATEGORY   |   ('OUTAGE.DURATION', 'mean') |   ('OUTAGE.DURATION', 'median') |   ('OUTAGE.DURATION', 'std') |   ('CUSTOMERS.AFFECTED', 'mean') |
|:-------------------|------------------------------:|--------------------------------:|-----------------------------:|---------------------------------:|
| cold               |                       2901.35 |                            1036 |                      6989.72 |                           158671 |
| normal             |                       2666.11 |                             757 |                      5473.82 |                           190121 |
| warm               |                       2837.37 |                             900 |                      6006.77 |                           181581 |

--- 

# Assessment of Missingness

## NMAR Analysis 

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

## Missisgness Dependency 

We investigated the missingness patterns in our dataset, focusing on the `CUSTOMERS.AFFECTED` column which shows substantial missingness (42.7%). Our analysis tested whether this missingness depends on other variables in the dataset.

We conducted a thorough analysis of missingness patterns in our dataset, particularly focusing on the relationships between different variables. Our analysis revealed:

### 1. **Highest Missing Percentages:**

To understand our missingness analysis, let's first look at the overall missingness patterns in our dataset:
<iframe
  src="assets/missingness_heatmap.html"
  width="1000"
  height="600"
  frameborder="0"
></iframe>
<iframe
  src="assets/missingness_bars.html"
  width="800"
  height="350"
  frameborder="0"
></iframe>
These visualizations show that `DEMAND.LOSS.MW` (58.74%) and `CUSTOMERS.AFFECTED` (42.7%) have the highest rates of missing values, while most other variables have very low missingness rates (< 10%).

### 2. **Missingness Dependency Tests**

We performed permutation tests to understand what factors the missingness of `CUSTOMERS.AFFECTED` depends on. Our analysis revealed:

 **Dependency on OUTAGE.DURATION**:
   - **Null Hypothesis**: The distribution of outage duration is the same whether CUSTOMERS.AFFECTED is missing or not
   - **Alternative Hypothesis**: The distribution of outage duration differs based on whether CUSTOMERS.AFFECTED is missing
   - Test Statistic: Difference in means
<iframe
  src="assets/missingness_duration.html"
  width="1000"
  height="550"
  frameborder="0"
></iframe>
<iframe
  src="assets/missingness_permutation.html"
  width="700"
  height="550"
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

 Analysis of CUSTOMERS.AFFECTED missingness revealed one dependent and one independent relationship:

**Dependent (MAR) - OUTAGE.DURATION:**
The missingness of CUSTOMERS.AFFECTED depends on OUTAGE.DURATION (p-value = 0.0010 < 0.05). The distribution plots and permutation test results show that outages with missing customer counts tend to have significantly different durations compared to those with recorded customer counts, with an observed test statistic of 1243.53 being far from what we'd expect by random chance.

**Dependent (MAR) - MONTH:**
The missingness of CUSTOMERS.AFFECTED also depends on MONTH (p-value = 0.0000 < 0.05), indicating strong evidence against MCAR. This suggests that the likelihood of missing customer count data varies significantly with different months of the year, perhaps due to seasonal patterns in reporting practices or outage characteristics.

**Independent (MCAR) - TOTAL.PRICE:**
TOTAL.PRICE shows characteristics of MCAR with only 1.43% missing values. Since electricity prices are set through regulatory processes independent of outage reporting systems, and missing values show no systematic patterns, the missingness mechanism appears random and unrelated to outage characteristics.

These findings help inform how we approach missing values in our analyses of power outage patterns.
---

# Hypothesis Testing

**Null Hypothesis (H₀)**: There is no difference in the average outage duration between warm and cold climate episodes (any observed differences are due to random chance)
**Alternative Hypothesis (H₁**): There is a significant difference in the average outage duration between warm and cold climate episodes
You planned to use:

**Test Statistic**: The difference in means of outage durations between warm and cold episodes

**Reason for Choosing Test Statistic**: We chose the difference in means between warm and cold climate outage durations because it directly measures our quantity of interest (duration differences) and is interpretable in our original units of minutes. When comparing two numerical distributions with similar shapes like our outage durations, the difference in means is an appropriate measure that captures both magnitude and direction of any potential climate effect while remaining easy to interpret in the context of power outages.

**Significance Level (α)**: 0.05

**The permutation test was conducted to examine whether there is a difference in outage durations between warm and cold climate episodes.**

**Our analysis showed:**

**Observed difference:** On average, outages during warm episodes were about 64 minutes shorter than outages during cold episodes (observed difference = -63.98 minutes)
With a p-value of 0.9067, which is much larger than our significance level (α = 0.05), **we fail to reject the null hypothesis**
<iframe
  src="assets/hypothesis_distribution.html"
  width="640"
  height="401"
  frameborder="0"
></iframe>
The empirical distribution of simulated differences shows our observed difference falls well within the range we would expect due to random chance. 
<iframe
  src="assets/hypothesis_boxplot.html"
  width="640"
  height="401"
  frameborder="0"
></iframe>
The box plots visually support this conclusion, showing similar distributions of outage durations between warm and cold climate episodes, with both categories having some extreme outliers

**This means the data does not provide sufficient evidence to conclude there is a significant difference in outage durations between warm and cold climate episodes.**

However, it's important to note that:

- This doesn't prove the durations are exactly the same
- Other climate-related factors not captured in this simple warm/cold categorization might still affect outage durations
- The presence of extreme outliers in both categories suggests that particularly long outages can occur regardless of climate category
<iframe
  src="assets/hypothesis_histogram.html"
  width="640"
  height="401"
  frameborder="0"
></iframe>
The overlapping histograms show the probability density for each climate category, making it easier to directly compare their distributions. We used semi-transparent bars so you can see where they overlap. We also normalized the histograms to show probabilities rather than raw counts, which makes the comparison more meaningful given that we might have different numbers of observations in each category.

---

# Framing a Prediction Problem 

### Prediction Problem and Type
The task is to predict the duration of a power outage, measured in minutes, using information available at the start of the outage. This is a regression problem because the target variable, `OUTAGE.DURATION`, is a continuous numerical value.

### Response Variable
The response variable is `OUTAGE.DURATION`, chosen because it is critical for emergency response planning and resource allocation. Accurately predicting the duration of an outage enables utility companies to optimize their operations and helps communities and emergency services prepare more effectively for extended outages.

### Evaluation Metric
The model will be evaluated using **Root Mean Square Error (RMSE)**. RMSE is appropriate because it penalizes large errors more heavily, which is crucial for this application. For instance, underestimating a 24-hour outage as 1 hour is far more problematic than a slight overestimate of a shorter outage. Additionally, RMSE is expressed in minutes, making the results intuitive and directly comparable to the target variable.

### Features Excluded from Prediction
The model excludes variables that are either unknown or unavailable at the time of prediction, such as:  

- `OUTAGE.RESTORATION`: Directly determines outage duration but is unknown when the outage begins.  
- `DEMAND.LOSS.MW`: Requires post-outage assessment.  
- `CUSTOMERS.AFFECTED`: Known only after assessing the outage's impact.  

By adhering to these constraints, the model remains practical and realistic for deployment.

---

# Baseline Model 

My baseline model is a regression model predicting the duration of a power outage (measured in minutes) based on two features: `CLIMATE.REGION` (nominal) and `TOTAL.CUSTOMERS` (quantitative). These features were selected because `CLIMATE.REGION` captures differences in weather and climate conditions across regions, which may influence the length of outages, and `TOTAL.CUSTOMERS` provides an estimate of the scale of energy demand. The target variable was `OUTAGE.DURATION`, which measures the length of an outage.

For preprocessing, missing values in the features and target were dropped to ensure clean data. The categorical feature `CLIMATE.REGION` was encoded using one-hot encoding, and `TOTAL.CUSTOMERS` was standardized. No additional transformations or handling of outliers were performed.

The baseline model achieved a **Train RMSE of 5952.03** and a **Test RMSE of 6116.95**, with **R² scores of 0.04 and 0.02** on the training and test sets, respectively. These results indicate that the model's ability to explain the variance in the target variable is very low, suggesting that additional features and improved preprocessing are necessary to enhance performance.

---

# Final Model 

The final model incorporates a more sophisticated preprocessing pipeline and an expanded set of features aimed at capturing the complex relationships in the data. The selected features were:

- **MONTH_SIN, MONTH_COS (quantitative)**: Cyclic encodings of the month to capture seasonal trends in outages.
- **OUTAGE.HOUR (quantitative)**: Represents the hour of the outage's start, potentially affecting response times and repair efficiency.
- **CLIMATE_IMPACT (quantitative)**: Interaction term between `ANOMALY.LEVEL` and whether the climate condition is "normal," capturing the severity of weather events.
- **INFRASTRUCTURE_SCALE (quantitative)**: Normalized product of `TOTAL.CUSTOMERS` and `TOTAL.SALES`, representing the scale of the energy infrastructure.
- **ANOMALY_CUSTOMER_INTERACTION (quantitative)**: Interaction term between `ANOMALY.LEVEL` and `TOTAL.CUSTOMERS` to account for combined effects of weather anomalies and energy demand.
- **CLIMATE.REGION (nominal)**: Encoded using one-hot encoding to account for regional differences.
- **TOTAL.PRICE (quantitative)**: Represents economic factors affecting energy consumption.

Outliers in the target variable (`OUTAGE.DURATION`) were handled using two strategies: Winsorization at the 95th percentile and log transformation to stabilize variance. GridSearchCV was employed to optimize the hyperparameters of a RandomForestRegressor, with the following results:

1. **Log Transformation Model**: Achieved a **Train RMSE of 1.29** and **Test RMSE of 2.03** with **R² scores of 0.702 (train) and 0.305 (test)**. Log transformation effectively reduced skewness and improved performance.
2. **Winsorization Model**: Achieved a **Train RMSE of 1889.29** and **Test RMSE of 2868.94** with **R² scores of 0.634 (train) and 0.184 (test)**. Winsorization helped manage outliers without altering the scale of the target variable.

The **final models significantly improved over the baseline**, as they incorporated domain-specific features and addressed outliers, leading to more meaningful predictions. While the R² scores indicate modest improvements, the results suggest that additional exploration of feature engineering and advanced modeling techniques could further enhance performance.

| Model              |   Train RMSE |   Test RMSE |   Train R² |   Test R² | Comments                                      |
|:-------------------|-------------:|------------:|-----------:|----------:|:----------------------------------------------|
| Baseline Model     |      5952.03 |     6116.95 |      0.04  |     0.02  | Basic linear regression with minimal features |
| Log Transformation |         1.29 |        2.03 |      0.702 |     0.305 | Log-transformed target variable               |
| Winsorization      |      1889.29 |     2868.94 |      0.634 |     0.184 | Capped extreme values at 95th percentile      |


---

# Fairness Analysis