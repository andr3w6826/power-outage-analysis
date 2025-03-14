# Power Outage Analysis
A Data Science Project

# Introduction

Power outages can have widespread impacts, disrupting daily life, businesses, and emergency services. The ability to predict the number of customers affected during an outage is crucial for infrastructure planning, disaster preparedness, and improving grid resilience. By understanding the key factors that contribute to large-scale outages, utility companies and policymakers can better allocate resources, improve response times, and minimize disruptions.

In this project, I analyze a dataset of major power outages across different states in the continental U.S. from January 2000 to July 2016. This dataset not only includes information on major outages but also geographical locations, regional climatic conditions, land-use characteristics, electricity consumption patterns, and economic factors of the states affected.


Thus, thisis project is centered around the following question:
**How can we predict the number of customers affected by a power outage based on available outage characteristics?**

By modeling outage severity in terms of affected customers, we aim to identify the key factors that contribute to large-scale outages and develop a predictive framework that could assist with risk assessment and mitigation strategies.

#### Data Overview 
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

#### Data Cleaning

**Selecting Relevant Columns**: The dataset contained 57 columns, but many were not needed for the analysis. Only variables relevant to the study were retained such as outage duration, causes, climation conditions, economic indicators, and population density metrics. These columns were corrected to appropriate data types.

**Handling Missing and Zero Values**: Certain columns, such as demand loss, customers affected, and outage duration, contained values recorded as zero.
Given the nature of power outages, it was unlikely that these values were genuinely zero; instead, they likely represented missing or unreported data.
These zero values were replaced with missing values to better reflect the underlying data collection process.

**Dropping Rows with Missing Key Information**: Entries where `OUTAGE.START/END.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, and `OUTAGE.RESTORATION.TIME` were missing were removed because an outage with missing time stamps and thus duration cannot be analyzed meaningfully.

**Combining Timestamps**: Since the dataset originally stored date and time as separate columns for both start and restoration times, I merged the two columns to create new timestamp colummns `OUTAGE.START` and `OUTAGE.RESTORATION`. This transformation provides a way to double check `OUTAGE.DURATION`

The first few rows of the cleaned DataFrame are shown below

|   YEAR |   MONTH | U.S._STATE   | NERC.REGION   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | CAUSE.CATEGORY     |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   TOTAL.PRICE |   TOTAL.SALES |   PC.REALGSP.STATE |   POPDEN_URBAN |   AREAPCT_URBAN | OUTAGE.START        | OUTAGE.RESTORATION   |
|-------:|--------:|:-------------|:--------------|:-------------------|----------------:|:-------------------|:-------------------|------------------:|-----------------:|---------------------:|--------------:|--------------:|-------------------:|---------------:|----------------:|:--------------------|:---------------------|
|   2011 |       7 | Minnesota    | MRO           | East North Central |            -0.3 | normal             | severe weather     |              3060 |              nan |                70000 |          9.28 |   6.56252e+06 |              51268 |           2279 |            2.14 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |
|   2014 |       5 | Minnesota    | MRO           | East North Central |            -0.1 | normal             | intentional attack |                 1 |              nan |                  nan |          9.28 |   5.28423e+06 |              53499 |           2279 |            2.14 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |
|   2010 |      10 | Minnesota    | MRO           | East North Central |            -1.5 | cold               | severe weather     |              3000 |              nan |                70000 |          8.15 |   5.22212e+06 |              50447 |           2279 |            2.14 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |
|   2012 |       6 | Minnesota    | MRO           | East North Central |            -0.1 | normal             | severe weather     |              2550 |              nan |                68200 |          9.19 |   5.78706e+06 |              51598 |           2279 |            2.14 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |
|   2015 |       7 | Minnesota    | MRO           | East North Central |             1.2 | warm               | severe weather     |              1740 |              250 |               250000 |         10.43 |   5.97034e+06 |              54431 |           2279 |            2.14 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |

#### Exploratory Data Analysis

<iframe
  src="assets/univariate-1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Assessment of Missingness
# Hypothesis Testing
# Framing a Prediction Problem
# Baseline Model
# Final Model
# Fairness Analysis