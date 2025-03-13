# Power Outage Analysis
By Andrew Chen

# Introduction

Power outages can have widespread impacts, disrupting daily life, businesses, and emergency services. The ability to predict the number of customers affected during an outage is crucial for infrastructure planning, disaster preparedness, and improving grid resilience. By understanding the key factors that contribute to large-scale outages, utility companies and policymakers can better allocate resources, improve response times, and minimize disruptions.

In this project, I analyze a dataset of major power outages across different states in the continental U.S. from January 2000 to July 2016. This dataset not only includes information on major outages but also geographical locations, regional climatic conditions, land-use characteristics, electricity consumption patterns, and economic factors of the states affected.


Thus, thisis project is centered around the following question:
**How can we predict the number of customers affected by a power outage based on available outage characteristics?**

By modeling outage severity in terms of affected customers, we aim to identify the key factors that contribute to large-scale outages and develop a predictive framework that could assist with risk assessment and mitigation strategies.

### Data Overview 
The original data contains 1534 rows, each corresponding to an outage, and 57 column features. However, I am only considering a portion of them for my analysis
| Column                     | Description |
|----------------------------|-------------|
| `'YEAR'`                   | Year an outage occurred |
| `'MONTH'`                  | Month an outage occurred |
| `'U.S._STATE'`             | State the outage occurred in |
| `'NERC.REGION'`            | North American Electric Reliability Corporation (NERC) regions involved in the outage event |
| `'CLIMATE.REGION'`         | U.S. Climate regions as specified by National Centers for Environmental Information (9 Regions) |
| `'ANOMALY.LEVEL'`          | Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season |
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


# Data Cleaning and Exploratory Data Analysis
# Assessment of Missingness
# Hypothesis Testing
# Framing a Prediction Problem
# Baseline Model
# Final Model
# Fairness Analysis