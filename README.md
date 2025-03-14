### Power Outage Analysis
A Data Science Project

# Introduction

Power outages can have widespread impacts, disrupting daily life, businesses, and emergency services. The ability to predict the number of customers affected during an outage is crucial for infrastructure planning, disaster preparedness, and improving grid resilience. By understanding the key factors that contribute to large-scale outages, utility companies and policymakers can better allocate resources, improve response times, and minimize disruptions.

In this project, I analyze a dataset of major power outages across different states in the continental U.S. from January 2000 to July 2016. This dataset not only includes information on major outages but also geographical locations, regional climatic conditions, land-use characteristics, electricity consumption patterns, and economic factors of the states affected.


Thus, thisis project is centered around the following question:
**How can we predict the number of customers affected by a power outage based on available outage characteristics?**

By modeling outage severity in terms of affected customers, we aim to identify the key factors that contribute to large-scale outages and develop a predictive framework that could assist with risk assessment and mitigation strategies.

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

**Selecting Relevant Columns**: The dataset contained 57 columns, but many were not needed for the analysis. Only variables relevant to the study were retained such as outage duration, causes, climation conditions, economic indicators, and population density metrics. These columns were corrected to appropriate data types.

**Handling Missing and Zero Values**: Certain columns, such as demand loss, customers affected, and outage duration, contained values recorded as zero.
Given the nature of power outages, it was unlikely that these values were genuinely zero; instead, they likely represented missing or unreported data.
These zero values were replaced with missing values to better reflect the underlying data collection process.

**Dropping Rows with Missing Key Information**: Entries where `OUTAGE.START/END.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, and `OUTAGE.RESTORATION.TIME` were missing were removed because an outage with missing time stamps and thus duration cannot be analyzed meaningfully.

**Combining Timestamps**: Since the dataset originally stored date and time as separate columns for both start and restoration times, I merged the two columns to create new timestamp colummns `OUTAGE.START` and `OUTAGE.RESTORATION`. This transformation provides a way to double check `OUTAGE.DURATION`

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

Exploratory Data Analysis provides insignts into the dataset's structure, distributions, and relationships between variables. 

### Univariate Analysis

- **Number of Outages Over Time**:

    Key Observations:
    - The number of outages generaly increased from 2000 - 2011 and spiked in 2011, reaching over 250 recorded outages
    - After 2011, there was a sharp decline, sugessting possible improvements to infastructure, policy changes, etc.

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
    - More than half of all outages were caused by severe weather, suggesting, that storm mitigation and climate durability should be a top priority for utility companies 
    - While intentional attacks are less frequent than weather events, it is still the second largest category and should be taken seriously
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
    - California and Texas experienced the highest number of recorded outages which could be due to population density, extremely dry weather, etc. 
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
    - Severe weather appears to be associated with some of the longest outages, with many instances lasting over 14,000 minutes (~10 days)
    - Intentional attakcs and equipment failures generally lead to shorter outages, indicating a quick resolution time with the exception of an outlier

<iframe
  src="assets/bivariate-1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
<br>
- **Demand Loss vs Outage Duration**

    *Note: Bubble size denotes number of customers affected*

    Key Observations:
    - The majority of outages are concentrated at shorter durations and lower demand losses, indicating that most power disruptions are short and affect a limited power supply
    - There is a cluster of high demand loss in the first 5,000 minutes, likely representing outages in urban areas with high energy consumption
    - 2008's Hurricane Ike stands out as an extreme event, representing the bubble lasting around 30,000 minutes (~21 days) and causing significant loss

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
    - Sorted by counts, California and Texas have the highest incidents while Michigan, Washington, and New York have few outages. However, CA and TX have lower average durations compared to MI, WA, NY.
    - This suggests that states like CA and TX might focus on reducing outage frequency, while the other states might prioritize improving recovery speed.

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
    - The South experiences the highest number of customers affected per outage but has a shorter outage duration implying that utilities in the South might have faster response times despite more large scale disruptions. 

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

# Hypothesis Testing



# Framing a Prediction Problem
# Baseline Model
# Final Model
# Fairness Analysis