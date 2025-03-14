### Power Outage Severity - Predicting and Understanding Grid Disruptions
A Data Science Project

# Introduction

Power outages can have widespread impacts, disrupting daily life, businesses, and emergency services. The ability to predict the number of customers affected during an outage is crucial for infrastructure planning, disaster preparedness, and improving grid resilience. By understanding the key factors that contribute to large-scale outages, utility companies and policymakers can better allocate resources, improve response times, and minimize disruptions.

In this project, I analyze a dataset of major power outages across different states in the continental U.S. from January 2000 to July 2016. This dataset not only includes information on major outages but also geographical locations, regional climatic conditions, land-use characteristics, electricity consumption patterns, and economic factors of the states affected.


Thus, this project is centered around the following question:
**"What factors influence the severity and duration of power outages, and how can we predict severe outages to improve response efforts?"**

By modeling outage severity in terms of affected customers, I aim to identify key contributing factors. Then develop a predictive framework that could assist with risk assessment and mitigation strategies.

### Data Overview 
The original data contains 1534 rows, each corresponding to an outage, and 57 column features. However, I am only considering a portion of them for my analysis.


| Column                     | Description |
|----------------------------|-------------|
| `'YEAR'`                   | Year an outage occurred |
| `'MONTH'`                  | Month an outage occurred |
| `'U.S._STATE'`             | State the outage occurred in |
| `'NERC.REGION'`            | North American Electric Reliability Corporation (NERC) regions involved in the outage event |
| `'CLIMATE.REGION'`         | U.S. Climate regions as specified by National Centers for Environmental Information (9 Regions) |
| `'ANOMALY.LEVEL'`          | Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season |
| `'CLIMATE.CATEGORY'`       | Represents the climate episodes corresponding to the years. The categories—"Warm", "Cold" or "Normal"—are based on a threshold of ±0.5°C for the Oceanic Niño Index (ONI) |
| `'OUTAGE.START.DATE'`      | Day of the year when the outage event started |
| `'OUTAGE.START.TIME'`      | Time of the day when the outage event started |
| `'OUTAGE.RESTORATION.DATE'` | Day of the year when power was restored to all the customers |
| `'OUTAGE.RESTORATION.TIME'` | Time of the day when power was restored to all the customers |
| `'CAUSE.CATEGORY'`         | Categories of all the events causing the major power outages |
| `'OUTAGE.DURATION'`        | Duration of outage events (in minutes) |
| `'DEMAND.LOSS.MW'`        | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported] |
| `'CUSTOMERS.AFFECTED'`     | Number of customers affected by the power outage event |
| `'TOTAL.PRICE'`            | Average monthly electricity price in the U.S. state (cents/kilowatt-hour) |
| `'TOTAL.SALES'`            | Total electricity consumption in the U.S. state (megawatt-hour) |
| `'PC.REALGSP.STATE'`       | Per capita real gross state product (GSP) in the U.S. state (measured in 2009 chained U.S. dollars) |
| `'POPDEN_URBAN'`           | Population density of the urban areas (persons per square mile) |
| `'AREAPCT_URBAN'`          | Percentage of the land area of the U.S. state represented by the land area of the urban areas (in %) |


# Data Cleaning and Exploratory Data Analysis

Cleaning the dataset was essential to ensure the accuracy and reliability of the analysis. Below is a step-by-step explanation of the cleaning process and how each step relates to the data-generating process and overall analysis.

## Data Cleaning

**Selecting Relevant Columns**: The dataset contained 57 columns, but many were not needed for the analysis. Only variables relevant to the study were retained such as outage duration, causes, climatic conditions, economic indicators, and population density metrics. These columns were corrected to appropriate data types.

**Handling Missing and Zero Values**: Certain columns, such as demand loss, customers affected, and outage duration, contained values recorded as zero.
Given the nature of power outages, it was unlikely that these values were genuinely zero; instead, they likely represented missing or unreported data.
These zero values were replaced with missing values to better reflect the underlying data collection process.

**Dropping Rows with Missing Key Information**: Entries where `OUTAGE.START/END.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, or `OUTAGE.RESTORATION.TIME` were missing were removed because an outage with missing time stamps and thus duration cannot be analyzed meaningfully.

**Combining Timestamps**: Since the dataset originally stored date and time as separate columns for both start and restoration times, I merged the two columns to create new timestamp columns `OUTAGE.START` and `OUTAGE.RESTORATION`. This transformation allows for cross-verifying `OUTAGE.DURATION` calculations.

The first few rows of the cleaned DataFrame are shown below, with a selection of columns chosen

|   YEAR |   MONTH | U.S._STATE   | CLIMATE.REGION     | CAUSE.CATEGORY     | OUTAGE.START        |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |
|-------:|--------:|:-------------|:-------------------|:-------------------|:--------------------|------------------:|-----------------:|---------------------:|
|   2015 |       7 | Minnesota    | East North Central | severe weather     | 2015-07-18 02:00:00 |              1740 |              250 |               250000 |
|   2010 |      11 | Minnesota    | East North Central | severe weather     | 2010-11-13 15:00:00 |              1860 |              nan |                60000 |
|   2010 |       7 | Minnesota    | East North Central | severe weather     | 2010-07-17 20:30:00 |              2970 |              nan |                63000 |
|   2005 |       6 | Minnesota    | East North Central | severe weather     | 2005-06-08 04:00:00 |              3960 |               75 |               300000 |
|   2015 |       3 | Minnesota    | East North Central | intentional attack | 2015-03-16 07:31:00 |               155 |               20 |                 5941 |
|   2013 |       6 | Minnesota    | East North Central | severe weather     | 2013-06-21 17:39:00 |              3621 |              nan |               400000 |

## Exploratory Data Analysis

Exploratory Data Analysis provides insights into the dataset's structure, distributions, and relationships between variables. 

### Univariate Analysis

- **Number of Outages Over Time**:

    Key Observations:
    - The number of outages generally increased from 2000 - 2011 and spiked in 2011, reaching over 250 recorded outages.
    - After 2011, there was a sharp decline, suggesting possible improvements to infrastructure, policy changes, etc.

<iframe
  src="assets/univariate-1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

- **Causes of 2011's Outages**

    Key Observations:
    - The large majority of causes were intentional attacks, hinting perhaps at increased civil unrest, crime, etc. 
    - Given these findings, utility companies might need to allocate more resources to combat physical/cyber attacks.

<iframe
  src="assets/univariate-2.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

- **Causes of 2000-2016 Outages**

    Key Observations:
    - More than half of all outages were caused by severe weather, suggesting that storm mitigation and climate durability should be a top priority for utility companies .
    - While intentional attacks are less frequent than weather events, it is still the second largest category and should be taken seriously.
    - Notably, equipment failure is surprisingly low, suggesting that the grid itself is usually well maintained.

<iframe
  src="assets/univariate-3.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>
<br>

- **Geospatial Distribution of Power Outages**

    Key Observations:
    - California and Texas experienced the highest number of recorded outages which could be due to population density, extreme weather conditions, etc. 
    - The East Coast and parts of the Midwest show moderate outage frequencies which may be attributed to seasonal storms, hurricanes, etc. 

<iframe
  src="assets/us_outage_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
<br>

### Bivariate Analysis

- **Outage Duration vs. Cause Category**

    Key Observations:
    - Severe weather appears to be associated with some of the longest outages, with many instances lasting over 14,000 minutes (~10 days).
    - Intentional attacks and equipment failures generally lead to shorter outages, indicating a quick resolution time with the exception of an outlier.

<iframe
  src="assets/bivariate-1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
<br>
- **Demand Loss vs. Outage Duration**

    *Note: Bubble size denotes number of customers affected*

    Key Observations:
    - The majority of outages are concentrated at shorter durations and lower demand losses, indicating that most power disruptions are short and affect a limited power supply.
    - There is a cluster of high demand loss in the first 5,000 minutes, likely representing outages in urban areas with high energy consumption.
    - 2008's Hurricane Ike stands out as an extreme event, with an outage lasting around 30,000 minutes (~21 days) and causing severe demand loss.

<iframe
  src="assets/bivariate-2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
<br>

### Grouping and Aggregates

    

- **Outages by U.S. State**

    *Note: Only first 5 rows are shown*

    Key Observations:
    - Sorted by counts, California and Texas have the most recorded outages, while Michigan, Washington, and New York have few outages. However, CA and TX have shorter average outage durations compared to Michigan, Washington, and New York.
    - This suggests that states like California and Texas might focus on reducing outage frequency, while the other states might prioritize improving recovery speed.

<br>

| U.S._STATE   |  Count |   Average OUTAGE.DURATION |
|:-------------|-------------------------------:|------------------------------:|
| California   |                            197 |                       1674.8  |
| Texas        |                            121 |                       2727.17 |
| Michigan     |                             95 |                       5302.98 |
| Washington   |                             73 |                       1838.7  |
| New York     |                             66 |                       6400.71 |

<br>

- **Outages by Climate Region**

    *Note: Only first 5 rows shown* 

    Key Observations:
    - Sorted by outage duration, the northeast has the longest average outage durations (~77 hours), suggesting that harsh winters and older infrastructure might lead to longer disruptions
    - The northeast's demand loss is also on the higher side, possibly due to high population density and reliance on electricity
    - The South experiences the highest number of customers affected per outage but has shorter outage durations, implying that utilities in the region may have faster response times despite more large-scale disruptions.

<br>

| CLIMATE.REGION     |   Average CUSTOMERS.AFFECTED |  Average DEMAND.LOSS.MW |  Average OUTAGE.DURATION |
|:-------------------|---------------------:|-----------------:|------------------:|
| East North Central |               149816 |          633.902 |           4626.5  |
| Northeast          |               175915 |          991.154 |           3330.52 |
| Central            |               145721 |          586.896 |           2882.21 |
| South              |               212031 |          475.686 |           2872.45 |
| Southeast          |               201340 |          865.318 |           2247.66 |

<br>


# Assessment of Missingness

| DataFrame columns        |   Missing Value Counts (sorted) |
|:-------------------|---------:|
| DEMAND.LOSS.MW     |      857 |
| CUSTOMERS.AFFECTED |      621 |
| OUTAGE.DURATION    |       78 |
| TOTAL.SALES        |       12 |
| TOTAL.PRICE        |       12 |

### NMAR Analysis:

#### **Is `CUSTOMERS.AFFECTED` NMAR?**

I believe that `CUSTOMERS.AFFECTED` is Not Missing At Random (NMAR) because the likelihood its missingness is related to the value itself rather than being randomly distributed or dependent on other observed variables. Various reasonings for missingness may be:

- Severe outages: Large scale disasters may overwhelm reporting systems
- Intentional non-disclosure: Utility companies might not report the impact of an outage for perhaps legal liabilities or bad press

To confirm whether the missingness is actually NMAR or instead Missing at Random (MAR), I could collect additional data such as:

- Cause of outage: The reason for missingness might be correlated with specific cause categories, suggesting MAR instead of NMAR
- Outage duration: If longer outages tend to have missing values for customers affected, the missingness could be associated with severeity rather than the customer count itself


### Missingness Dependency: 

#### **Is `DEMAND.LOSS.MW` NMAR?** 

The column is missing 857 times out of approximately 1457 observations, meaning that nearly 58% of the data in this column is missing. To determine if the missingness is NMAR or MAR, I will test this column against the columns `CUSTOMERS.AFFECTED` and `OUTAGE.DURATION`

##### DEMAND.LOSS.MW vs CUSTOMERS.AFFECTED

First, I examine the distribution of customers affected when demand loss is missing and not missing.

Null Hypothesis: The distribution of customers affected is the same when demand loss is missing versus not missing.

Alternate Hypothesis:  The distribution of customers affected is not the same when demand loss is missing versus not missing.

<iframe
  src="assets/missing1.html"
  width="800"
  height="430"
  frameborder="0"
></iframe>

I found the observed difference in means to be 15735.22 which has a p-value of 0.4704. Since this p-value is above the signifiance threshold of 0.05, I fail to reject the null hypothesis. This suggests that the missingness in `DEMAND.LOSS.MW` is not significantly dependent on `CUSTOMERS.AFFECTED`, and that the missingness could be NMAR. Below is the empirical distribution of the difference in means. 

<iframe
  src="assets/missing2.html"
  width="800"
  height="420"
  frameborder="0"
></iframe>

##### DEMAND.LOSS.MW vs OUTAGE.DURATION

Here, I conduct a similar difference-in-means test, comparing outage duration between rows where demand loss is missing and not missing.

Null Hypothesis: The distribution of outage duration is the same when demand loss is missing versus not missing.

Alternate Hypothesis:  The distribution of outage duration is not the same when demand loss is missing versus not missing.

<iframe
  src="assets/missing3.html"
  width="800"
  height="440"
  frameborder="0"
></iframe>

The observed difference in means was 807.36 , and the computed p-value was 0.008. Given this p-value, we reject the null hypothesis, indicating that there is a statistically significant relationship between the missingness of DEMAND.LOSS.MW and OUTAGE.DURATION. This suggests that outage duration may play a role in whether demand loss is recorded or not.

<iframe
  src="assets/missing4.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

#### **So, is `DEMAND.LOSS.MW` NMAR?** 
Since we reject the null hypothesis, it indicates that demand loss is more likely to be missing when outages have a significantly different duration (either shorter or longer). This suggests that the missingness of DEMAND.LOSS.MW is not completely random (NMAR) and is instead dependent on outage duration (MAR)

This makes sense in a real-world context: shorter outages might not have demand loss reported because the impact was minimal or not recorded in time, while extreme outages might also be missing data due to infrastructure failures or reporting inconsistencies.

# Hypothesis Testing

Power outages disrupt lives and infrastructure, but different causes may require different mitigation strategies. If severe weather leads to longer outages, investments in storm-proof infrastructure, grid resilience, and disaster recovery should be prioritized. Conversely, if intentional attacks cause comparable disruptions, cybersecurity and physical grid protection may require greater attention.

**Null Hypothesis**: The mean outage durations for severe weather and intentional attacks are the same.

**Alternative Hypothesis**: The mean outage duration for severe weather is greater than that of intentional attacks.

**Test Statistic**: The difference in means between the outage durations of severe weather and intentional attacks is 3,377.78 (~56 hours)

**P-Value**: 0.0, meaning the probability of observing this result under the null hypothesis is extremely low


<iframe
  src="assets/hypothesis1.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

Since the p-value is effectively 0, we reject the null hypothesis in favor of the alternative. This suggests that severe weather outages tend to last significantly longer than those caused by intentional attakcs

# Framing a Prediction Problem

For this project, I am formulating a binary classification problem to predict whether a power outage is severe (lasting more than 2 days) or non-severe (lasting 2 days or less). Thus, my respose variable will be IS_SEVERE (1 if the outage lasts more than 2 days, 0 otherwise). This is an important problem because accurately predicting whether an outage will be severe allows utility companies to proactively allocate resources, mobilize emergency response teams, and minimize downtime for critical infrastructure. By identifying severe outages early, companies can prioritize high-risk regions, deploy repair crews more efficiently, and mitigate economic and public safety risks associated with prolonged power disruptions. 

My model evaluation metric will be the F1-score as my dataset is imbalanced in severe and non-severe outages. The F1-score balances precision and recall, making it the best choice when false negatives and false positives are important concerns. 

At the time of an outage severity prediction, we would have access to the features: `YEAR`, `MONTH`, `U.S._STATE`, `NERC.REGION`, `CLIMATE.REGION`, `CLIMATE.CATEGORY`,  `CAUSE.CATEGORY`, `CUSTOMERS.AFFECTED`, `SEASON`, `START_HOUR`,  `ANOMALY.LEVEL`.

# Baseline Model

To establish a baseline model, I trained a Random Forest Classifier using only three features: Month (numerical), U.S. State (categorical), and Customers Affected (numerical). This serves as a simple benchmark to compare against more complex models later.

The features were then passed through a pipline where I one-hot-encoded `U.S._STATE` and passed the numerical features through. To make up for a portion of missing values, I imputed missing values for `CUSTOMERS.AFFECTED` with it's median to avoid biasing the dataset toward any extreme values.

### Baseline Model Performance

| Metric        | Class 0 (Non-Severe) | Class 1 (Severe) | Overall |
|--------------|---------------------|------------------|---------|
| **Precision**  | 0.38                | 0.91             | -       |
| **Recall**     | 0.44                | 0.89             | -       |
| **F1-Score**   | 0.41                | 0.90             | -       |
| **Accuracy**   | -                   | -                | 0.82    |

These performance metrics indicate that while the baseline model performs well in identifying severe outages (Class 1) with a high recall (0.89) and F1-score (0.90), it struggles to correctly classify non-severe outages (Class 0), as shown by its low precision (0.38) and F1-score (0.41). The accuracy of 82% suggests overall strong performance, but due to class imbalance, accuracy alone is not a sufficient indicator of model quality. The model's tendency to prioritize severe outages (high recall for Class 1) may be useful for emergency response planning, but the high false positive rate for severe outages could lead to an over-allocation of resources.

While the model is not bad, it could use significant improvement in distinguishing between severe and non-severe outages to ensure resources are allocated efficiently.

# Final Model

Building upon the baseline model, I introduced new features and optimized hyperparameters to enhance the model’s ability to predict whether an outage will be severe. This section outlines the feature engineering process, the modeling algorithm, and the performance improvements over the baseline.

### Feature Engineering

To improve the model's predictive power, I introduced four new features derived from the outage start time and event characteristics. These features were chosen because they capture time-based trends and operational patterns that may influence outage severity. I believe that the time of day impacts outage severity because outages that occur during overnight hours may experience delayed response times due to reduced availability of repair crews, while outages during peak hours might be resolved more quickly due to higher staffing levels. Additionally, seasonal variations and weekends may impact outage durations due to weather conditions, workforce availability, and infrastructure demand.

- `START_HOUR`: Extracts the hour of the day when the outage began (0-23).
- `SEASON`: Categorizes the month into Winter, Spring, Summer, or Fall.
- `IS_WEEKEND`: Binary feature (1 if the outage occurred on a weekend, 0 otherwise).
- `IS_SEVERE`: The response variable, indicating whether an outage lasted more than 12 hours.

### Model Algorithm and Hyperparameters

For my final model, I continued using the Random Forest Classifier, as it effectively handles both numerical and categorical data, is robust to outliers, and provides feature importance insights. Additionally, Random Forest naturally handles class imbalance through its ability to adjust class weights, making it a strong choice for this prediction task. My model incorporated these features

- `YEAR` (Quantitative): Captures long-term trends in outage severity due to infrastructure changes and climate patterns.
- `MONTH` (Ordinal): Accounts for seasonal weather variations (e.g., hurricanes in summer, snowstorms in winter).
- `U.S._STATE` (Nominal): Reflects state-level differences in infrastructure, regulations, and response times.
- `NERC.REGION` (Nominal): Represents regional grid reliability and preparedness variations.
- `CLIMATE.REGION` (Nominal): Identifies areas more prone to extreme weather events that affect outage severity.
- `CLIMATE.CATEGORY` (Nominal): Categorizes climate conditions (Warm, Cold, Normal) to assess grid performance impacts.
- `CAUSE.CATEGORY` (Nominal): Helps differentiate outages by cause (weather, equipment failure, or attacks).
- `CUSTOMERS.AFFECTED` (Quantitative): Measures outage scale, influencing severity and emergency response needs.
- `START_HOUR` (Quantitative): Captures time-of-day effects on response times (e.g., night outages may take longer to restore).
- `SEASON` (Nominal): Groups months into Winter, Spring, Summer, or Fall for broader seasonal trends.
- `ANOMALY.LEVEL` (Quantitative): Detects unusual weather deviations that may contribute to prolonged outages.

To further improve model performance, I conducted hyperparameter tuning using GridSearchCV with 5-fold cross-validation, optimizing for F1-score to ensure a balance between precision and recall. The best hyperparameters found were:

- max_depth: 20
- min_samples_leaf: 1
- min_samples_split: 2
- n_estimators: 200

These hyperparameters allowed the model to learn complex patterns in the data while avoiding overfitting, leading to improved generalization.

### Final Model Performance

| Metric        | Class 0 (Non-Severe) | Class 1 (Severe) | Overall |
|--------------|---------------------|------------------|---------|
| **Precision**  | 0.63                | 0.93             | -       |
| **Recall**     | 0.59                | 0.94             | -       |
| **F1-Score**   | 0.61                | 0.94             | -       |
| **Accuracy**   | -                   | -                | 0.89    |

### Baseline vs. Final Model Comparison

| Metric        | Baseline Model | Final Model |
|--------------|---------------|-------------|
| **Accuracy**  | 0.82          | **0.89**    |
| **F1 (Non-Severe)** | 0.41    | **0.61**    |
| **F1 (Severe)** | 0.90       | **0.94**    |

The final model shows signifanct improvement over the baseline model, making it more reliable for predicting outage severity and guiding emergency response. With a higher accurracy, the model is better at distinguishing between severe and non-severe outages. 

One of the most impactful improvements is in identifying non-severe outages, where the F1-score increased from 0.41 to 0.61. This means the model is far less likely to misclassify minor outages as severe, preventing unnecessary emergency responses and resource overuse. At the same time, the model maintains a strong ability to detect truly severe outages, with an F1-score of 0.94 compared to 0.90 in the baseline, ensuring that high-risk outages continue to receive urgent attention. 

By reducing false positives and improving ranking ability, this final model enhances disaster preparedness, response efficiency, and overall grid resilience.

# Fairness Analysis

To assess the fairness of my final model, I analyzed whether it performs equally well for outages caused by severe weather compared to intentional attacks. Since these two causes represent different types of disruptions—one driven by natural events and the other by human actions—disparities in model performance could indicate bias in how the model predicts different outage scenarios.

Groups Defined:
- Group X: Outages caused by severe weather

- Group Y: Outages caused by intentional attacks

- Evaluation Metric: I chose precision as my evaluation metric because it measures how often the model’s severe outage predictions are correct. If precision is lower for one group, it means the model is more likely to incorrectly classify outages in that group as severe, leading to misallocation of emergency response resources.

**Null Hypothesis**: The model’s precision is the same for severe weather outages and intentional attacks. Any observed difference is due to random chance.

**Alternative Hypothesis**: The model’s precision is lower for intentional attacks than for severe weather, meaning it struggles to correctly classify these outages.

### Results
- Observed Precision Difference (Severe Weather - Intentional Attacks): 0.06
- P-Value: 0.0

Since the p-value is 0.0, I reject the null hypothesis. This indicates that the observed difference in precision between the two groups is statistically significant and unlikely to be due to random variation. In particular, the model exhibits lower precision for intentional attacks, meaning it is more prone to misclassifying intentional attacks as severe outages compared to severe weather-related outages.

<iframe
  src="assets/fairness1.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>
