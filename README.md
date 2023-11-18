# Power Outage Research Project

Project 3 for DSC 80

Names: Dhruv Kanetkar and Justin Chou

## Introduction

### Research Question: How has major power outage incident response improved over time?
The electrical grid is a very important infrastructual technology in the world. It provides power to pretty much everything that we use on a day to day basis. However, due to factors such as weather and load on the grid, outages are a common occurance. In this project, we dive deeper into a comprehensive dataset on power outages and critical information related to them. Our goal is to understand whether or not restoration time for these major power outages is decreasing over the years, this will provide as a basis to figure out what protocols and systems may need to be improved for faster restoration times.

The dataset was pulled from Purdue University's LASCI Research Data and provides data on major power outage events in the continental U.S from January 2000 to July 2016. One important piece to note is that outages reported in this dataset only affected a single U.S. state at the time the outage occured. Included in the dataset is information such as year, month, climate region, anomaly level, outage start date, outage restoration date, categories of the causes, detailed descriptions of the event categories, outage duration, monthly electricity price, and electricity consumption. The dataset has 1540 rows, meaning 1540 different cases of major power outages. The dataframe also has 57 columns, for the purposes of our analysis, we will primarily be looking at the following columns: OBS which is the ID of each observed outage, YEAR which is the year the outage event occured randing from 2000-2016, and OUTAGE.DURATION which is the total duration in minutes of the outage. We will also be combining columns on outage start time and date as well as restoration time and date for easier readibility.

In the project we will first be cleaning the dataset and conduct an exploratory data analysis to get a fundamental understanding of the data, including some interesting aggregation trends. This will be used to determine some key information on the data and assess the missingness of values within the dataset itself. The missingness will be assessed through NMAR analysis and looking at the missingness dependency. Finally, we will focus on the larger question of how the major power outage incident reponse time has changed over time. We will run a hypothesis test to indicate if there is a clear sign of the duration of outages decreasing as the years progress. In order to run this test we will classify power outages as older if they occured from the year 2000 to 2008, and recent if they occurred from 2009 to 2016. 

## Data Cleaning and EDA
> Remove extra beginning rows and prepare into dataframe format

We first remove all unnecessary columns and rows from the dataset, this includes the Units, and the first few rows explaining the spreadsheet in order to get it into a usable format for a pandas dataframe.

> Check the datatypes

Next we check if the datatypes and consider what they need to be in order to run the proper analysis

```
OBS                        object
YEAR                       object
MONTH                      object
U.S._STATE                 object
POSTAL.CODE                object
NERC.REGION                object
CLIMATE.REGION             object
ANOMALY.LEVEL              object
CLIMATE.CATEGORY           object
OUTAGE.START.DATE          object
OUTAGE.START.TIME          object
OUTAGE.RESTORATION.DATE    object
OUTAGE.RESTORATION.TIME    object
CAUSE.CATEGORY             object
CAUSE.CATEGORY.DETAIL      object
HURRICANE.NAMES            object
OUTAGE.DURATION            object
DEMAND.LOSS.MW             object
CUSTOMERS.AFFECTED         object
RES.PRICE                  object
COM.PRICE                  object
IND.PRICE                  object
TOTAL.PRICE                object
RES.SALES                  object
COM.SALES                  object
...
AREAPCT_UC                 object
PCT_LAND                   object
PCT_WATER_TOT              object
PCT_WATER_INLAND           object
```
> Converting Object Values

Since all columns are of type object, they need to be converted to one of either `str`, `int64`, or `float64`. This is determined by looking at the dataframe values and converting them accordingly

> Combining DATE and TIME columns

The columns `OUTAGE.START.TIME` and `OUTAGE.START.DATE` need to be combined into a single column `OUTAGE.START`. Similarly the columns `OUTAGE.RESTORATION.TIME` and `OUTAGE.RESTORATION.DATE` need to be combined into a single column `OUTAGE.RESTORATION`

> Cleaning Result

Now we get a cleaned version of the dataframe

  | OBS | YEAR | MONTH | U.S._STATE | POSTAL.CODE | NERC.REGION | CLIMATE.REGION | ANOMALY.LEVEL | CLIMATE.CATEGORY | CAUSE.CATEGORY | ... | POPDEN_URBAN | POPDEN_UC | POPDEN_RURAL | AREAPCT_URBAN | AREAPCT_UC | PCT_LAND | PCT_WATER_TOT | PCT_WATER_INLAND | OUTAGE.START           | OUTAGE.RESTORATION      |
  |-----|------|-------|-------------|-------------|-------------|-----------------|---------------|-------------------|----------------|-----|--------------|-----------|--------------|---------------|------------|----------|---------------|-------------------|------------------------|-------------------------|
  | 1   | 2011 | 7     | Minnesota   | MN          | MRO         | East North Central | -0.3          | normal            | severe weather  | ... | 2279         | 1700.5    | 18.2         | 2.14          | 0.6        | 91.592666 | 8.407334      | 5.478743          | 2011-07-01 17:00:00   | 2011-07-03 20:00:00    |
  | 2   | 2014 | 5     | Minnesota   | MN          | MRO         | East North Central | -0.1          | normal            | intentional attack | ... | 2279         | 1700.5    | 18.2         | 2.14          | 0.6        | 91.592666 | 8.407334      | 5.478743          | 2014-05-11 18:38:00   | 2014-05-11 18:39:00    |
  | 3   | 2010 | 10    | Minnesota   | MN          | MRO         | East North Central | -1.5          | cold              | severe weather  | ... | 2279         | 1700.5    | 18.2         | 2.14          | 0.6        | 91.592666 | 8.407334      | 5.478743          | 2010-10-26 20:00:00   | 2010-10-28 22:00:00    |
  | 4   | 2012 | 6     | Minnesota   | MN          | MRO         | East North Central | -0.1          | normal            | severe weather  | ... | 2279         | 1700.5    | 18.2         | 2.14          | 0.6        | 91.592666 | 8.407334      | 5.478743          | 2012-06-19 04:30:00   | 2012-06-20 23:00:00    |
  | 5   | 2015 | 7     | Minnesota   | MN          | MRO         | East North Central | 1.2           | warm              | severe weather  | ... | 2279         | 1700.5    | 18.2         | 2.14          | 0.6        | 91.592666 | 8.407334      | 5.478743          | 2015-07-18 02:00:00   | 2015-07-19 07:00:00    |

### Univariate Analysis
We started with a grouped frequency distribution of the outages based on their dates. We noticed a large uptick in power outages from 2010-2011, followed by a steady decline. We hypothesize that the low number of outages in the earlier years is due to a less widespread infrastructure.
<iframe src="yearly_distribution.html" width=600 height=450 frameBorder=0></iframe>

We also looked at some other factors

Severe Weather: we found that the uptick in 2011 was less great than that of all power outages, indicating that severe weather, which partially responsible, likely was not the sole reason why this happened. Generally, we thought this looked like the most evenly distributed distribution, with a left skew.
<iframe src="yearly_distribution_severe weather.html" width=600 height=450 frameBorder=0></iframe>

Intentional Attack: we noticed that there were little-to-no outages caused by intentional attack leading up to 2011, when there was a significant peak and steady decline. We find it unlikely there were no intentional attacks leading up to that year, rather, increased awareness, detection, and reporting led to that conclusion.
<iframe src="yearly_distribution_intentional attack.html" width=600 height=450 frameBorder=0></iframe>

Equipment Failure: We noticed that after dipping from 2010 to 2012 and reupping in 2013, the number of outages due to equipment failure dropped to 0. We believe this makes sense given that equipment failure is the most easily controllable/solvable for those in charge of power structures.
<iframe src="yearly_distribution_equipment failure.html" width=600 height=450 frameBorder=0></iframe>

### Bivariate Analysis
This plot is "zoomed in" to a max of 25000 minute duration in order to get a better view of the spread. While there are noticeably long outages in early 2014, by the "eye-test" and by our linear regression line we noticed that there was a slight down trend in the average duration of outages over time.
<iframe src="outage_duration.html" width=600 height=450 frameBorder=0></iframe>

Similar to the last scatter plot, we noticed that demand loss decreased over time as a general trend, but are looking to prove that in our hypothesis tests. Here we can see for example with the outage type being sever weather
<iframe src="outage_duration_severe weather.html" width=600 height=450 frameBorder=0></iframe>

### Interesting Aggregates
We noticed that from the eye test, the most recent years generally had lower average outage durations across the board, which was an encouraging sign. Aside from that, however, there weren't too many strong trends.


  | CLIMATE.REGION | Central | East North Central | Northeast | Northwest | South | Southeast | Southwest | West | West North Central |
  | -------------- | ------- | ------------------ | --------- | --------- | ----- | --------- | --------- | ---- | ------------------- |
  | YEAR           |         |                    |           |           |       |           |           |      |                     |
  | 2000           | 1200.0  | 0.0                | 681.0     | 0.0       | 903.0 | 5384.0    | 33.0      | 0.0  | 0.0                 |
  | 2001           | 0.0     | 0.0                | 298.5     | 0.0       | 3915.7| 241.0     | 0.0       | 653.0| 0.0                 |
  | 2002           | 15420.0 | 3600.0             | 3130.0    | 0.0       | 10020.0| 1460.5    | 0.0       | 3028.6| 0.0                 |
  | 2003           | 1842.8  | 7378.5             | 5127.1    | 4014.0    | 3476.3| 998.8     | 67.5      | 8612.0| 0.0                 |
  | 2004           | 3588.0  | 5452.0             | 2406.1    | 3240.0    | 3686.2| 3380.7    | 33019.3   | 1091.3| 4.0                 |
  | 2005           | 8427.8  | 5612.9             | 3269.3    | 0.0       | 5348.4| 7902.1    | 0.0       | 1406.2| 0.0                 |
  | 2006           | 1895.8  | 6750.0             | 5062.1    | 6599.4    | 326.5 | 502.5     | 1289.5    | 2238.1| 9600.0              |
  | 2007           | 2425.2  | 3600.7             | 2134.1    | 4158.0    | 4795.1| 600.5     | 283.0     | 871.0 | 0.0                 |
  | 2008           | 3697.1  | 5401.2             | 3425.3    | 224.0     | 13058.5| 1341.9    | 871.0     | 1873.4| 60.0                |
  | 2009           | 5135.2  | 12294.9            | 3112.8    | 1204.0    | 1631.1| 1611.2    | 2308.3    | 451.4 | 115.3               |
  | 2010           | 4750.4  | 2995.2             | 5422.8    | 2580.0    | 1896.7| 598.4     | 1333.0    | 1448.4| 77.7                |
  | 2011           | 1575.5  | 3354.9             | 2721.6    | 407.3     | 1396.5| 1110.6    | 537.3     | 2516.2| 240.0               |
  | 2012           | 2673.5  | 2709.5             | 2796.2    | 335.7     | 1003.5| 1205.2    | 124.8     | 1115.0| 0.0                 |
  | 2013           | 1952.1  | 3632.9             | 634.0     | 674.2     | 4412.8| 584.2     | 429.5     | 592.0 | 42.0                |
  | 2014           | 996.3   | 10037.4            | 5536.2    | 385.0     | 543.5 | 1780.7    | 82.8      | 308.8| 56.0                |
  | 2015           | 168.5   | 1961.3             | 1692.4    | 1075.9    | 1452.9| 1796.8    | 51.3      | 257.2| 0.0                 |
  | 2016           | 1199.0  | 1139.3             | 1771.3    | 678.8     | 1947.6| 535.0     | 658.0     | 12433.8| 0.0                |
  
There was a general trend across all regions that the demand loss was shrinking, which is another encouraging sign that we are starting to minimize the impact of the outages

## Assessment of Missingness
As we can see there are columns that have many null values, such as DEMAND.LOSS.MW, which need to be filled in.

  | Column                | Non-Null Count | Dtype              |
  | -------------------- | -------------- | ------------------ |
  | OBS                  | 1534           | int64              |
  | YEAR                 | 1534           | int64              |
  | MONTH                | 1525           | object             |
  | U.S._STATE           | 1534           | object             |
  | POSTAL.CODE          | 1534           | object             |
  | NERC.REGION          | 1534           | object             |
  | CLIMATE.REGION       | 1534           | object             |
  | ANOMALY.LEVEL        | 1525           | float64            |
  | CLIMATE.CATEGORY     | 1534           | object             |
  | CAUSE.CATEGORY       | 1534           | object             |
  | CAUSE.CATEGORY.DETAIL| 1534           | object             |
  | HURRICANE.NAMES      | 1534           | object             |
  | OUTAGE.DURATION      | 1476           | object             |
  | DEMAND.LOSS.MW       | 829            | object             |
  | CUSTOMERS.AFFECTED   | 1091           | object             |
  | RES.PRICE            | 1512           | float64            |
  | COM.PRICE            | 1512           | float64            |
  | IND.PRICE            | 1512           | float64            |
  | TOTAL.PRICE          | 1512           | float64            |
  | RES.SALES            | 1512           | object             |
  | ...                  | ...            | ...                |
  | OUTAGE.START         | 1525           | datetime64[ns]     |
  | OUTAGE.RESTORATION   | 1476           | datetime64[ns]     |

### NMAR Analysis
For a column to not be missing at random we need to think about whether there could be column values that affect whether or not other column values could be missing or not. Based on the data for major power outages. The research paper providing this data states that all data not found was filled in as null. Looking at the columns there could be column values that are NMAR, one clear example would be OUTAGE.DURATION, which is the time between OUTAGE.START and OUTAGE.RESTORATION, so if one of the values was not provided, then the duration value would also be missing.

### Missingness Dependency

We're going to start with the CAUSE.CATEGORY, as we think that the cause of the outage could also cause issues in areas such as tracking, resulting in an inability to predict the loss in demand.

Our alpha value is 0.05, and we're using the TVD as a test statistic because we're looking at differences in distributions between categories.

Null Hypothesis: There is no difference in distributions of missingness between categories.
Alternative Hypothesis: There is a difference in distributions of missingness between categories.

Our observed test statistic is: `0.17869773888047633`

Now, let's simulate the test statistic 10000 times under the assumption of the null by shuffling the values of the DEMAND.LOSS.MW.

We get a P-Value of `0.6822`. Because p > 0.05, we fail to reject the null hypothesis that OUTAGE.DURATION affects the missingness of DEMAND.LOSS.MW.

## Hypothesis Testing

To reiterate, the question that we are concerned with is: How has major power outage incident response improved over time?

For this section, we will define incident response time as the duration of the major power outage, with lower time intervals indicating faster incident response. We will also define older major outages as occuring from the years 2000 to 2008, and recent major outages occuring from the years 2009 to 2016. We will conduct a permutation test.

### Setting Up the Test

Null Hypothesis H0: Older major outages have the same mean incident response time as the mean incident response time of recent major power outages.

Alternative Hypothesis H1: Older major outages have a greater mean incident reponse time than mean incident response time of recent major power outages.

We only want to use the columns that are useful for our analysis, these include OBS, YEAR, and OUTAGE.DURATION. We will create a new column `recency` which converts the `YEAR` column from numerical values to either 'older' if the values are less than or equal to 2008 and 'recent' otherwise.

|     OBS |   YEAR |   OUTAGE.DURATION | recency   |
|-------:|----------:|-------------:|:----------|
| 1 |        2011 |            3060 | recent     |
| 2 |        2014 |            1 | recent      |
| 3 |         2010 |            3000 | recent     |
| 4 |         2012 |            2550 | recent     |
| 5 |        2015 |            1740 | recent      |

The reason for choosing a one-sided test is that we want to see if there has been a clear trend of improvement over time for incident response time of a major power outage. Having a decrease in the change would mean that the duration was worse over time, which is not helpful.

| recency   |   OUTAGE.DURATION | 
|:----------|----------:|
| older     |   3930.436652 | 
| recent      |  2067.538685  |

We can see that OUTAGE.DURATION is numerical so it is appropriate to use the difference of means. As part of our research, the significance level we will use is 0.05. 

The observed difference in mean duration is `1862.8979668`

### Permutation Test
<iframe src="outage_duration_permutation_test.html" width=600 height=450 frameBorder=0></iframe>

We ran this permutation test 10000 times and plotted the distribution, the red line marked the observed statistic for the difference of mean duration time.

### Hypothesis Testing Conclusion
The testing yielded a P-value of `0.000`, which means that at a significance level of 0.05,  we are able to reject the null hypothesis. 

We can also see from the graph that there are no permutations that have a greater difference of means than the observed statistic. This is reasonble as we can expect for the duration to decrease as protocols and newer technologies are adopted over time. Additionally, infrastucture may be adopted to be more resistant and automatically fix itself for common issues, which could be another reason why there is a decrease in the mean duration time. This is a good sign as this could indicate a trend of better response to restoring power outages. 
