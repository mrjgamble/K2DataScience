# NYC MTA Subway Ridership & Weather Analysis

## Introduction

*Note: This project was completed as part of the [K2 Data Science program](http://www.k2datascience.com/).  It is designed to focus on exploratory data analysis (data cleaning, basic statistical analysis, and data visulaization).  The exploratory data analysis will then identify next steps that could be used for further analysis.*

In this project, our client is a clothing retailer who is looking to plan flash sales and pop-up events around busy and pleasant weather days in NYC.  In particular the question we are looking to answer is as follows:

> **What days are historically busy and pleasant in NYC?**

We want to help the clothing retailer pick several days over the next few months that will maximize their exposure and minimize the chance of poor weather.

This analysis will only outline an EDA, laying the groudwork for futher analysis, including predictive analysis if desired. 

## Approach
We will use two datasets to answer the question of which days are historically busy and pleasant in NYC:
* Using turnstile data from the MTA will be our way to indicate the busyness of a day. 
* Weather data supplied from the NOAA will be used to identify pleasant weather days. 


### MTA Ridership Data
The MTA turnstile dataset contains turnstile data collected from all stations.  We are particularly looking at data from September to October 2016.

The original thought was to use data spanning multiple years, averaging daily ridership across the years in order to have a better sense of which days are historically busy.  However, it was found that the structure of the turnstile data changed from 2014 2016 making it difficult to accurately align station data across multiple years.  For example - 
* In October 2014, the MTA changed the turnstile data schema, meaning data prior to October 2014 could not aligned with data recorded in or after October 2014.
* Between 2015 & 2016, the MTA changed how station names were recorded.  As a result, it would be difficult to accurately align turnstile data from 2015 with the appropriate station data in 2016.

Before any analysis of the MTA dataset could occur, a thorough cleaning was required.  The data itself is recorded at the turnstile level - meaning it needed to be summarized at the station level and loaded into a Pandas DataFrame before analysis could begin.  This process is detailed in the ridership cleansing notebook. **INSERT LINK**  

**Exploring Ridership**
The first thing to be looked at was the total ridership across September to October:

**nyc_mta_ridership_per_day**

Reviewing the plot, several notable trends were investigated further:

A) Ridership fluctuates considerably throughout the week.  The city is primarily busy on weekdays due to the amount of commuters found within NYC.  A considerable drop can be seen on Saturday & Sundays, which is as a result of the workforce presence in the city. 

We can observe a considerable difference between weekdays versus weekends as noted in the plot below (5.6 mil to 3.1 mil riders on average)

**nyc_mta_avg_ridership_per_day_of_week**

B) Ridership is affected by holidays.  Notice the dip in ridership on October 9th & 10th.  This is as a result of Columbus Day on October 10th.  We can observe a dip on Labor Day weekend (September 5th), though ridership is considerably lower in August.  We'll touch more on this in the next point.  

C) Ridership increases as we move into the fall.  This could be as a result of cooler weather as well as the fact that summer vacation is over (students are back to school and workers are back at the office).

'2016-Aug': 4700032.58
'2016-Sep': 5020936.07
'2016-Oct': 5021505.93

D) When looking at the ridership per station, we can see there are a handful of stations that are busier than others. These repesent major traffic hubs in the MTA system that could represent opportunities to target a larger audience for clothing retailers.

**nyc_mta_ridership_per_station_per_day**

Plotting the total ridership per station onto a heatmap, we can clearly see hotspots throughout the city that can be used to maximize exposure. 

**Insert heatmap**

### NOAA weather data
Data collected from the NOAA consisted of daily weather temperature readings (max, min, average), as well as precipitation, snowfall, and snow depth readings.  

Much like the MTA data, we will be observing weather data from the September to October time frame.  Unlike the MTA dataset, we do have suitable data from multiple years.  Thus, it was decieded to look at data from 2014 to 2016 - averaging the daily temperature & precipitation across multiple years.  

For the most part, the data retrieved from NOAA was very clean and required very little maniupation.  Details of the data analysis and cleansing is found in a single ipython notebook **insertlinke**

**Defining a pleasant day**
A pleasant day can be defined by many variables.  We have two variables within this particular dataset: average daily temperature and precipitaiton.  For the purpose of this analysis, we have selected the following to define a pleasant day: 
* Average Daily Temperature >= 60F
* Average Daily Precipitation < 0.5mm

**exploring Dialy Average Temperature**
As mentioned above, we will be using the average daily temperature for each year in this analysis.  See below: 
**nyc_daily_temp**

As one would imagine for a Northern American city, the average temperatue begins to dip as we transition from summer into fall:

Aug Sept Oct
2014 74.516129 69.733333 59.629032
2015 78.967742 74.450000 58.048387
2016 79.177419 71.800000 58.758065

**nyc_monthly_avg_temp**

Averaging the daily temperature over 3 years, we were able to achieve a new daily average temperature that could be used for our analysis.  This average of 3 years will hopefully settle out any annomalies that we experience in an overly warm or cool year.  The average of 3 years of data looks as follows: 

**insert nyc_daily_avg_temp**


**Exploring Precipitation**
Plotting the 3 years worth of preciptiation, we can also see that the daily precipitation begins to increase as we transition from summer into fall:
**insert nyc_daily_precipitation**

As we did with the temperature, we averaged the daily precipitation over 3 years to achieve the average daily precipitation:
**insert nyc_daily_avg_precipitation**

**Exploring Snow**
The time frame for which this analysis occured was late summer & early fall.  As one might have expected, there was no snow recorded on any of our days in question, so we will not look any further into this variable. 

**Exploring Pleasant Days**
Using our definition of a pleasant day, we can apply this to our weather dataset to identify historically which days have been pleasant:
**insert nyc_weather_pleasant_days**

We've found that, historically, there are 48 days that are pleasant.  We can now use this data to insertsect with our ridership data to identify both busy & pleasant days.   

**Exploring busy & pleasant days**
See notebook
Based on our analysis above, we have outlined that a busy & pleasant day should be defined as:
* One of the following weekdays: Tuesday, Wednesday, Thrusday, Friday
* have an average temperature >= 60F
* Have less than an average of 0.05mm precipitaiton

Knowing this, we can overlay our two datasets to find the following pleasant days: 

**Insert nyc_weather_mta_pleasant_days**

This represents a total of 29 days over the course of 2016.

When observing the above dataset, we also asked if there was any correlation between weather & ridership.  A correlation matrix was created to test this theory:

**Insert nyc_weather_corr**

* Ridership is slightly negatively correlated to temperature (meaning as temperature decreases, ridership increases)
* Ridership is also slightly negatively correlated to precipitation (meaning as precipitation descreases, ridership increases - we shouldn't read into this too deeply - I believe the large number of days with no precipitation impacts this correlation.  We also see that precipitation can happen on warm days as well - which drives down ridership.)

With all of the analysis complete, can we recommend specific days in 2017 to target?  The accuracy of this is not near perfect, but we've doen the following steps:
For the period of September 1st to October 31st 2017
1) Filter out Saturday, Sunday, Monday (our non-busy days)
2) Of the remaining days, identify which days are historically pleasant in NYC (based on the initital 48 pleasant days we previously identified)

Taking the above appraoch, we see that there are 30 potential busy & pleasant days in 2017: 

Tue Aug 01 2017
Thu Aug 03 2017
Tue Aug 08 2017
Wed Aug 09 2017
Thu Aug 10 2017
Tue Aug 15 2017
Wed Aug 16 2017
Thu Aug 17 2017
Tue Aug 22 2017
Wed Aug 23 2017
Thu Aug 24 2017
Fri Aug 25 2017
Tue Aug 29 2017
Wed Aug 30 2017
Tue Sep 05 2017
Wed Sep 06 2017
Thu Sep 07 2017
Fri Sep 08 2017
Fri Sep 15 2017
Wed Sep 20 2017
Thu Sep 21 2017
Fri Sep 22 2017
Tue Sep 26 2017
Thu Sep 28 2017
Fri Sep 29 2017
Fri Oct 06 2017
Thu Oct 12 2017
Fri Oct 13 2017
Tue Oct 17 2017
Wed Oct 18 2017

Summary & Conclusion

In summary of the above analysis, the following recommendations have been reached:

1. The busiest days in the city tend to be Tuesdays, Wednesdays, Thursdays, and Fridays.
2. Expect lower foot traffic in the city on holidays, as we've seen a dramatic impact on ridership during holidays. 
3. Using our target parameters of pleasant weather, we have found there are 48 suitable days over the course of August to Octber. 
4. Ridership is negatively correlated to average temperature.  As temperature decreases, we expect to see ridership increase.  

## Next Steps
Based on our initial analsyis, we can begin to build predictive modelling to predict pleasant and busy days.  

It may also be desired to increase the accurancy of our predictive analysis by adding additional variables for analysis

### MTA data improvements
* We only looked at a single year of ridership to determine historical ridership values.  We could incorporate additional years to normalize the data and achieve a better predictive model
* We found that turnstile data often had itegrity issues, as ridership counts often were out of sync.  This can result in inaccurate ridership levels.

### Weather data improvements
* Include additional variables for determining a 'pleasant day'.  Examples of these may be: cloud coverage, dew point humidity, etc.  

### Additional data
* Include additional market variables such as target demographics, competitor locations, seasonality to better identify specific locations within the city to target. 

