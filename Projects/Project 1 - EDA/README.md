# NYC MTA Subway Ridership & Weather Analysis

## Introduction

*Note: This project was completed as part of the [K2 Data Science program](http://www.k2datascience.com/).  It is designed to focus on exploratory data analysis (data cleaning, basic statistical analysis, and data visualization).  The exploratory data analysis will then identify next steps that could be used for further analysis.*

In this project, our client is a clothing retailer who is looking to plan flash sales and pop-up events around busy and pleasant weather days in NYC.  In particular the question we are looking to answer is as follows:

> **What days are historically busy and pleasant in NYC?**

We want to help the clothing retailer pick several days over the next few months that will maximize their exposure and minimize the chance of poor weather.

## Approach
*Prior to beginning this EDA, an EDA mvp was completed to provide initial insights into the datasets.  Details of this mvp can be found in the [nyc_mta_weather notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_mta_weather_eda_mvp.ipynb).*

Two datasets were used to answer the question of which days are historically busy and pleasant in NYC:
* Using turnstile data from the MTA will be our way to indicate the busyness of a day. 
* Weather data supplied from the NOAA will be used to identify pleasant weather days. 


##### MTA Ridership Data

The MTA turnstile dataset contains turnstile data collected from stations across the nyc subway network.  We be specifically looking at data from August to October 2016.  

The original thought was to use data spanning multiple years; However, it was found that the structure of the turnstile data changed from 2014 to 2016 making it difficult to accurately align station data across multiple years.  For example - 
* In October 2014, the MTA changed the turnstile data schema, meaning data prior to October 2014 could not aligned with data recorded in or after October 2014.
* Between 2015 & 2016, the MTA changed how station names were recorded.  As a result, it would be difficult to accurately align turnstile data from 2015 with the appropriate station data in 2016.

Before any analysis of the MTA dataset could occur, a thorough cleaning was required.  The data itself is recorded at the turnstile level - meaning ridership numbers had to be summarized at the station level before being loaded into a Pandas DataFrame for analysis.  This cleaning process is detailed in the [nyc_mta_cleaning notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_mta_cleaning.ipynb).  

##### NOAA Weather Data
Data collected from the NOAA consisted of daily weather temperature readings (max, min, average), as well as precipitation, snowfall, and snow depth readings.  

Much like the MTA data, we will be observing weather data from the September to October time frame.  Unlike the MTA dataset, we do have suitable data from multiple years.  Thus, it was decided to look at data from 2014 to 2016 - averaging the daily temperature & precipitation across multiple years.  

For the most part, the data retrieved from NOAA was very clean and required very little manipulation.  The cleaning process is detailed in the [nyc_weather_cleaning_and_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_weather_cleaning_and_analysis.ipynb)

## Exploring MTA Ridership

Upon successfully cleaning the data, we first looked at the total ridership across the time frame: 

![nyc_mta_ridership_per_day.png](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_mta_ridership_per_day.png "nyc_mta_ridership_per_day")

Based on the above figure, several trends were identified:

* Holidays negatively impact ridership.  Notice the dip in ridership on October 9th & 10th.  This is as a result of Columbus Day (October 10th).  We can also observe a dip on Labor Day (September 5th), though ridership is considerably lower leading into August.  We'll touch more on this in the next point.  

* Ridership increases as we move into the fall.  This could be as a result of cooler weather as well as the fact that summer vacation is over (students are back to school and workers are back at the office).

| Month | Total Ridership |
| ----- | :-----: |
| Aug 2016 | 4,700,032.58 |
| Sep 2016 | 5,020,936.07 |
| Oct 2016 | 5,021,505.93 |

* Ridership fluctuates considerably throughout the week.  The city is primarily busy on weekdays due to the amount of commuters.  A considerable drop in ridership is visible on Saturday & Sundays. We can observe a considerable difference between weekdays versus weekends as noted in the plot below (5.6 mil to 3.1 mil riders on average). 

![nyc_mta_avg_ridership_per_day_of_week](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_mta_avg_ridership_per_day_of_week.png "nyc_mta_avg_ridership_per_day_of_week")

Based on these averages - it would be safe to assume that weekdays are busier than weekends.  However, is it safe to assume that Tuesdays, Wednesdays, Thursdays, and Fridays are busier than Mondays?  In order to prove this, we can perform a ttest on our data.  Our null hypothesis will state that *Monday ridership levels are the same as every other weekday*.

The p-values obtained from the ttest are as follows:

|  | Tuesday | Wednesday | Thursday | Friday |
| --- | ---- | --------- | -------- | ------ |
| Monday | 0.000000023653 | 0.0000000000397 | 0.000000000000215 | 0.000000000438999 |

In each case above, we reject the null hypothesis and conclude that Monday's lower ridership levels are not by chance.  Monday's ridership levels are in fact significantly lower than every other weekday.  We received a very small p-value (less than 0.05) in each of our tests, and therefore can say that our results are statistically significant. 

Based on the above findings, we will say that a *busy day in NYC* consists of any Tuesday, Wednesday, Thursday, or Friday that do not fall on a holiday. 

(see [nyc_mta_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_mta_analysis.ipynb) for more)

## Exploring MTA Ridership Per Hour

After acknowledging that busy days are Tuesdays, Wednesdays, Thursdays, and Fridays, are there hours in which a retailers can target to maximize exposure?

The MTA data is summarized on a 4-hour interval (4am, 8am, 12pm etc).  However, upon inspecting the data further it was found that there are turnstiles that do not follow this reporting period.  It was found that several stations (particularly on the PATH line) report on a random hour/minute/second.  In order to align the data, it was resampled to 4 hour intervals using a 'sum' aggregator.  The results were then graphically displayed on a line plot (see below). 

![nyc_mta_ridership_per_hour](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_mta_ridership_per_hour.png "nyc_mta_ridership_per_hour")


* It can be noted that there are only 2 reporting periods near the end of a day - 8:00pm and 12:00am (also known as 0:00hr).  Ridership from 8:01pm to 12:00am are summarized into the 0:00hr reporting period.  This is why we see such significant traffic at this early morning time (think Friday nights, or nights which have a sporting event ending after 8:00pm). 

* Looking at the data, we do see busy periods throughout the day.  Focusing on ideal times for popup events and flash sales, retailers should target activations between the 10:00am to 8:00pm time frame.
 
## Exploring MTA Ridership Per Station

Plotting ridership per station demonstrates that some stations are busier than others.  

![nyc_mta_ridership_per_station_per_day](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_mta_ridership_per_station_per_day.png "nyc_mta_ridership_per_station_per_day")

These busy stations represent major traffic hubs in the MTA system that could represent opportunities to target a larger audience for clothing retailers.  To detail these areas, a heatmap was created.  Subway stations that represent hubs or are clustered closely together tend to have a much higher intensity on the heatmap.  Areas of specific interest are the financial district & grand central station:

![nyc_mta_heatmap](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_mta_heatmap.png)

*note: heatmap will not be interactive in markdown.  Please see nyc_mta_analysis notebook linked below* 
 
Plotting the ridership per station for the top 30 stations, we can see that the world trade centre and grand central are among these top stations:

![nyc_mta_top_30_stations](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_mta_top_30_stations.png "nyc_mta_top_30_stations")

(see [nyc_mta_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_mta_analysis.ipynb) for more)

## Exploring Daily Average Temperature
As mentioned above, we will be using the average daily temperature for each year in this analysis.  Below we can see these temperatures for our time period: 

![nyc_daily_temp](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_daily_temp.png "nyc_daily_temp")

As one would imagine for a city located in the North East, the average temperature begins to dip as we transition from summer into fall:

|  | Aug | Sept | Oct |
| --- | --- | --- | --- |
| 2014 | 74.51 | 69.73 | 59.62 |
| 2015 | 78.96 | 74.45 | 58.04 |
| 2016 | 79.17 | 71.80 | 58.75 |

![nyc_monthly_avg_temp](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_monthly_avg_temp.png "nyc_monthly_avg_temp")

Averaging the temperatures over 3 years, we were able to achieve a new daily average temperature that could be used for our analysis.  This average of 3 years will hopefully settle out any anomalies that we experience in an overly warm or cool year.  The average of 3 years of data looks as follows: 

![nyc_daily_avg_temp](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_daily_avg_temp.png "insert nyc_daily_avg_temp")

(see [nyc_weather_cleaning_and_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_weather_cleaning_and_analysis.ipynb) for more)

## Exploring Precipitation
Plotting 3 years of precipitation data, we see that the daily precipitation begins to increase as we transition from summer into fall:

![nyc_daily_precipitation](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_daily_precipitation.png "nyc_daily_precipitation")

As we did with the temperature, we averaged the daily precipitation over 3 years to achieve the average daily precipitation:

![nyc_daily_avg_precipitation](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_daily_avg_precipitation.png "insert nyc_daily_avg_precipitation")

Using this average, we find that very few days have 0 precipitation.  As a result, it is realistic to assume that a day with (on average) less than 0.5mm precipitation could potentially be a pleasant day. 

(see [nyc_weather_cleaning_and_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_weather_cleaning_and_analysis.ipynb) for more)

## Exploring Snow
The time frame for which this analysis occurred was late summer to fall.  As one might have expected, there was no snow recorded on any of our days in question, so we will not look any further into this variable. 

(see [nyc_weather_cleaning_and_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_weather_cleaning_and_analysis.ipynb) for more)

## Exploring Pleasant Days

In order to explore pleasant days further, we first needed to define **a pleasant day**.  For the purpose of this analysis, we have selected the following definition:  
* Average Daily Temperature >= 60F
* Average Daily Precipitation < 0.5mm

Using our definition of a pleasant day, we can apply this to our weather dataset to identify days which have been historically pleasant:

![nyc_weather_pleasant_days](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_weather_pleasant_days.png "nyc_weather_pleasant_days")

We've found that, historically, there are 48 days that are pleasant.  We can now use this data to intersect with our ridership data to identify both busy & pleasant days.   

(see [nyc_weather_cleaning_and_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_weather_cleaning_and_analysis.ipynb) for more)

## Exploring Busy & Pleasant Days
Based on our analysis above, we have outlined that a busy & pleasant day should be defined as:
* One of the following weekdays: Tuesday, Wednesday, Thursday, Friday
* Have a daily average temperature >= 60F
* Have a daily average precipitation < 0.05mm

Knowing this, we can overlay our two datasets to find the following pleasant days: 

![nyc_weather_mta_pleasant_days](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_weather_mta_pleasant_days.png "nyc_weather_mta_pleasant_days")

We find that there are potentially 29 days over the course of August to October that were pleasant.

When observing the above dataset, we also asked if there was any correlation between weather and ridership.  A correlation matrix was created to test this theory:

![nyc_weather_corr](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_weather_corr.png "nyc_weather_corr")

We noted:
* Ridership is slightly negatively correlated to temperature - meaning as temperature decreases, ridership increases.  This is consistent with our analysis thus far. 
* Ridership is also slightly negatively correlated to precipitation - meaning as precipitation decreases, ridership increases.  We shouldn't read into this too deeply.  I believe the large number of days with no precipitation impacts this correlation.  We also see that precipitation can happen on warm days, which also contributes to a decrease in ridership.

With all of the analysis complete, can we recommend specific days in 2017 to target?  To accomplish this, we performed the following for the period of September 1st to October 31st 2017:

1) Remove Saturdays, Sundays, and Mondays (which represent non-busy days)
2) Remove days which were historically unpleasant 

This approach led us to find 30 potential busy & pleasant days in August to October 2017 that clothing retailers can target for sales & popup events: 

| Aug | Sep | Oct |
| --- | --- | --- |
| Tue Aug 01 2017 | Tue Sep 05 2017 | Fri Oct 06 2017 |
| Thu Aug 03 2017 | Wed Sep 06 2017 | Thu Oct 12 2017 |
| Tue Aug 08 2017 | Thu Sep 07 2017 | Fri Oct 13 2017 |
| Wed Aug 09 2017 | Fri Sep 08 2017 | Tue Oct 17 2017 |
| Thu Aug 10 2017 | Fri Sep 15 2017 | Wed Oct 18 2017 |
| Tue Aug 15 2017 | Wed Sep 20 2017 | |
| Wed Aug 16 2017 | Thu Sep 21 2017 | |
| Thu Aug 17 2017 | Fri Sep 22 2017 | |
| Tue Aug 22 2017 | Tue Sep 26 2017 | |
| Wed Aug 23 2017 | Thu Sep 28 2017 | |
| Thu Aug 24 2017 | Fri Sep 29 2017 | |
| Fri Aug 25 2017 | | |
| Tue Aug 29 2017 | | |
| Wed Aug 30 2017 | | |

(see [nyc_mta_weather_eda notebooke](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_mta_weather_eda.ipynb) for more)

## Summary & Conclusion

The following recommendations have been made to ensure clothing retailers are targeting busy & pleasant days in NYC:

1. Busier days in the city tend to be Tuesdays, Wednesdays, Thursdays, and Fridays.  To maximize busy periods, target hours between 10am & 8pm. 
2. Expect lower foot traffic in the city on holidays. 
3. Historically, there are 48 suitable days over the course of August to October that are both busy & pleasant. 
4. Ridership is negatively correlated to average temperature.  As temperature decreases, we expect to see ridership increase. 
5. Several areas of the city are expected to be busier due to traffic hubs.  Heatmaps can help identify these busy areas for clothing retailers.  

Although this project servered as a great introduction into data exploration and graphing, it was flawed due to the inconsistencies found in the MTA dataset.  Particularly: 
* Multiple years of MTA ridership data should be used in order to accuractely depict a historical analysis of 'busy days'.  This was not possible due to the ever changing naming conventions for stations that the MTA has used across the years.  
* Inaccurate turnstile readings from day to day - this introduced inaccurate ridership levels across all stations.
* Inaccurate reporting periods from station to station - majority of stations reported at 4 hour intervals.  Others reported at different hour/minute/second intervals.  This made it hard to accurately match reporting periods across all stations.  

## Next Steps
This project was never intended to go beyond the initial EDA.  This initial EDA can be expanded upon to included predictive modelling to help clothing retailers predict pleasant and busy days in NYC.  

This EDA can also be revisited to improve accuracy of the data based on the below findings:

##### MTA Data Improvements
As mentioned above 
* Incorporate additional years to normalize the data and achieve a better predictive model.  Looking at only a single year of ridership data does not allow us to paint the picture of historically busy days. 
* Improve turnstile data integrity issues.  It was found that ridership counts often were out of sync and reporting periods were not consistent across the data.  These issues can result in inaccurate ridership levels.

##### Weather Data Improvements
* Include additional variables for determining a 'pleasant day'.  Examples of these may be: cloud coverage, dew point humidity, etc.  

##### Additional Supporting Data
* Include additional market variables such as target demographics, competitor locations, and seasonality to better identify specific locations within the city to target. 


