# Power Outage Research Project

Project 3 for DSC 80
Names: Dhruv Kanetkar and Justin Chou

## Introduction

### Research Question: How has major power outage incident response improved over time?
The electrical grid is a very important infrastructual technology in the world. It provides power to pretty much everything that we use on a day to day basis. However, due to factors such as weather and load on the grid, outages are a common occurance. In this project, we dive deeper into a comprehensive dataset on power outages and critical information related to them. Our goal is to understand whether or not restoration time for these major power outages is decreasing over the years, this will provide as a basis to figure out what protocols and systems may need to be improved for faster restoration times.

The dataset was pulled from Purdue University's LASCI Research Data and provides data on major power outage events in the continental U.S from January 2000 to July 2016. One important piece to note is that outages reported in this dataset only affected a single U.S. state at the time the outage occured. Included in the dataset is information such as year, month, climate region, anomaly level, outage start date, outage restoration date, categories of the causes, detailed descriptions of the event categories, outage duration, monthly electricity price, and electricity consumption. The dataset has 1540 rows, meaning 1540 different cases of major power outages. The dataframe also has 57 columns, for the purposes of our analysis, we will primarily be looking at the following columns: OBS which is the ID of each observed outage, YEAR which is the year the outage event occured randing from 2000-2016, and OUTAGE.DURATION which is the total duration in minutes of the outage. We will also be combining columns on outage start time and date as well as restoration time and date for easier readibility.

In the project we will first be cleaning the dataset and conduct an exploratory data analysis to get a fundamental understanding of the data, including some interesting aggregation trends. This will be used to determine some key information on the data and assess the missingness of values within the dataset itself. The missingness will be assessed through NMAR analysis and looking at the missingness dependency. Finally, we will focus on the larger question of how the major power outage incident reponse time has changed over time. We will run a hypothesis test to indicate if there is a clear sign of the duration of outages decreasing as the years progress. In order to run this test we will classify power outages as older if they occured from the year 2000 to 2008, and recent if they occurred from 2009 to 2016. 

## Data Cleaning and EDA

We first remove all unnecessary columns and rows from the dataset, this includes the Units, and the first few rows explaining the spreadsheet


## Assessment of Missingness


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
