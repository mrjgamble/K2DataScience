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


##### MTA Ridership Data

The MTA turnstile dataset contains turnstile data collected from stations across the nyc subway network.  We be specifically looking at data from August to October 2016.  

The original thought was to use data spanning multiple years, however, it was found that the structure of the turnstile data changed from 2014 to 2016 making it difficult to accurately align station data across multiple years.  For example - 
* In October 2014, the MTA changed the turnstile data schema, meaning data prior to October 2014 could not aligned with data recorded in or after October 2014.
* Between 2015 & 2016, the MTA changed how station names were recorded.  As a result, it would be difficult to accurately align turnstile data from 2015 with the appropriate station data in 2016.

Before any analysis of the MTA dataset could occur, a thorough cleaning was required.  The data itself is recorded at the turnstile level - meaning ridership numbers had to be summarized at the station level before being loaded into a Pandas DataFrame for analysis.  This cleaning process is detailed in the [nyc_mta_cleaning notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_mta_cleaning.ipynb).  

##### NOAA Weather Data
Data collected from the NOAA consisted of daily weather temperature readings (max, min, average), as well as precipitation, snowfall, and snow depth readings.  

Much like the MTA data, we will be observing weather data from the September to October time frame.  Unlike the MTA dataset, we do have suitable data from multiple years.  Thus, it was decieded to look at data from 2014 to 2016 - averaging the daily temperature & precipitation across multiple years.  

For the most part, the data retrieved from NOAA was very clean and required very little maniupation.  The cleaning process is detailed in the [nyc_weather_cleaning_and_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_weather_cleaning_and_analysis.ipynb)

## Exploring MTA Ridership

Upon successfully cleaning the data, we first looked at the total ridership across the time frame: 

![nyc_mta_ridership_per_day.png](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_mta_ridership_per_day.png "nyc_mta_ridership_per_day")

Based on the above figure, several trends were identified:

* Ridership fluctuates considerably throughout the week.  The city is primarily busy on weekdays due to the amount of commuters.  A considerable drop in ridership is visible on Saturday & Sundays. We can observe a considerable difference between weekdays versus weekends as noted in the plot below (5.6 mil to 3.1 mil riders on average). 

![nyc_mta_avg_ridership_per_day_of_week](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_mta_avg_ridership_per_day_of_week.png "nyc_mta_avg_ridership_per_day_of_week")

* Holidays negatively impact ridership.  Notice the dip in ridership on October 9th & 10th.  This is as a result of Columbus Day (October 10th).  We can also observe a dip on Labor Day (September 5th), though ridership is considerably lower leading into August.  We'll touch more on this in the next point.  

* Ridership increases as we move into the fall.  This could be as a result of cooler weather as well as the fact that summer vacation is over (students are back to school and workers are back at the office).

| Month | Total Ridership |
| ----- | :-----: |
| Aug 2016 | 4,700,032.58 |
| Sep 2016 | 5,020,936.07 |
| Oct 2016 | 5,021,505.93 |

In order to ensure our clothing retailer targers a *busy day in NYC*, we will say that a busy day consists of Tuesdays, Wednesdays, Thursdays, Fridays which are not hoidays. 

(see [nyc_mta_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_mta_analysis.ipynb) for more)

## Exploring MTA Ridership Per Station

Plotting ridership per station demonstrates that some stations are busier than others.  

![nyc_mta_ridership_per_station_per_day](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_mta_ridership_per_station_per_day.png "nyc_mta_ridership_per_station_per_day")

These busy stations repesent major traffic hubs in the MTA system that could represent opportunities to target a larger audience for clothing retailers.  To detail these areas, a heatmap was created.  Subway stations that represent hubs or are clustered closely together tend to have a much higher intensity on the heatmap.  Areas of specfic interest are the financial district & grand central station:

![nyc_mta_heatmap](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_mta_heatmap.png)

Plotting the ridership per station for the top 30 stations, we can see that the world trade centre and grand central are among these top stations:

![nyc_mta_top_30_stations](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_mta_top_30_stations.png "nyc_mta_top_30_stations")

(see [nyc_mta_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_mta_analysis.ipynb) for more)

## Exploring Dialy Average Temperature
As mentioned above, we will be using the average daily temperature for each year in this analysis.  Below we can see these temperatures for our time period: 

![nyc_daily_temp](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_daily_temp.png "nyc_daily_temp")

As one would imagine for a city located in the North East, the average temperatue begins to dip as we transition from summer into fall:

|  | Aug | Sept | Oct |
| --- | --- | --- | --- |
| 2014 | 74.51 | 69.73 | 59.62 |
| 2015 | 78.96 | 74.45 | 58.04 |
| 2016 | 79.17 | 71.80 | 58.75 |

![nyc_monthly_avg_temp](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_monthly_avg_temp.png "nyc_monthly_avg_temp")

Averaging the temperatures over 3 years, we were able to achieve a new daily average temperature that could be used for our analysis.  This average of 3 years will hopefully settle out any annomalies that we experience in an overly warm or cool year.  The average of 3 years of data looks as follows: 

![nyc_daily_avg_temp](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_daily_avg_temp.png "insert nyc_daily_avg_temp")

(see [nyc_weather_cleaning_and_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_weather_cleaning_and_analysis.ipynb) for more)

## Exploring Precipitation
Plotting 3 years of preciptiation data, we see that the daily precipitation begins to increase as we transition from summer into fall:

![nyc_daily_precipitation](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_daily_precipitation.png "nyc_daily_precipitation")

As we did with the temperature, we averaged the daily precipitation over 3 years to achieve the average daily precipitation:
![nyc_daily_avg_precipitation](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_daily_avg_precipitation.png "insert nyc_daily_avg_precipitation")

Using this average, we find that very few days have 0 precipiation.  As a result, it is realistic to assume that a day with (on average) less than 0.5mm precipitation could potentially be a pleasant day. 

(see [nyc_weather_cleaning_and_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_weather_cleaning_and_analysis.ipynb) for more)

## Exploring Snow
The time frame for which this analysis occured was late summer to fall.  As one might have expected, there was no snow recorded on any of our days in question, so we will not look any further into this variable. 

(see [nyc_weather_cleaning_and_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_weather_cleaning_and_analysis.ipynb) for more)

## Exploring Pleasant Days
In order to explore pleasant days further, we first needed to define **a pleasant day**.  For the purpose of this analysis, we have selected the following definition:  
* Average Daily Temperature >= 60F
* Average Daily Precipitation < 0.5mm

Using our definition of a pleasant day, we can apply this to our weather dataset to identify days which have been historically pleasant:

![nyc_weather_pleasant_days](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_weather_pleasant_days.png "nyc_weather_pleasant_days")

We've found that, historically, there are 48 days that are pleasant.  We can now use this data to insertsect with our ridership data to identify both busy & pleasant days.   

(see [nyc_weather_cleaning_and_analysis notebook](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/nyc_weather_cleaning_and_analysis.ipynb) for more)

## Exploring Busy & Pleasant Days
Based on our analysis above, we have outlined that a busy & pleasant day should be defined as:
* One of the following weekdays: Tuesday, Wednesday, Thrusday, Friday
* Have a daily average temperature >= 60F
* Have a daily average precipitation < 0.05mm

Knowing this, we can overlay our two datasets to find the following pleasant days: 

![nyc_weather_mta_pleasant_days](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_weather_mta_pleasant_days.png "nyc_weather_mta_pleasant_days")

We find that there are potentially 29 days over the course of August to October that were pleasant.

When observing the above dataset, we also asked if there was any correlation between weather & ridership.  A correlation matrix was created to test this theory:

![nyc_weather_corr](https://github.com/mrjgamble/K2DataScience/blob/master/Projects/Project%201%20-%20EDA/figures/nyc_weather_corr.png "nyc_weather_corr")

We noted:
* Ridership is slightly negatively correlated to temperature - meaning as temperature decreases, ridership increases.  This is consistent with our analysis thus far. 
* Ridership is also slightly negatively correlated to precipitation - meaning as precipitation descreases, ridership increases.  We shouldn't read into this too deeply.  I believe the large number of days with no precipitation impacts this correlation.  We also see that precipitation can happen on warm days, which also contributes to a decrease in ridership.

With all of the analysis complete, can we recommend specific days in 2017 to target?  To accomplish this, we performed the following for the period of September 1st to October 31st 2017:

1) Remove Saturdays, Sundays, and Mondays (which represent non-busy days)
2) Remove days which were historically unpleasant 

This apporach led us to find 30 potential busy & pleasant days in August to October 2017 that clothing retailers can target for sales & popup events: 

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

1. Busier days in the city tend to be Tuesdays, Wednesdays, Thursdays, and Fridays.
2. Expect lower foot traffic in the city on holidays. 
3. Historically, there are 48 suitable days over the course of August to Octber that are both busy & pleasant. 
4. Ridership is negatively correlated to average temperature.  As temperature decreases, we expect to see ridership increase. 
5. Several areas of the city are expected to be busier due to traffic hubs.  Heatmaps can help identify these busy areas for clothing retailers.  

## Next Steps
This project was never inteded to go beyond the initial EDA.  This initial EDA can be expanded upon to included predictive modelling to help clothing retails predict pleasant and busy days in NYC.  

This EDA can also be revisited to improve accurancy of the data based on the below findings:

##### MTA Data Improvements
* We only looked at a single year of ridership to determine historical ridership values.  We could incorporate additional years to normalize the data and achieve a better predictive model
* We found that turnstile data often had itegrity issues, as ridership counts often were out of sync from one day to the next.  We used the data as best we could, however the turnstile data issues can result in inaccurate ridership levels.

##### Weather Data Improvements
* Include additional variables for determining a 'pleasant day'.  Examples of these may be: cloud coverage, dew point humidity, etc.  

##### Additional Supporting Data
* Include additional market variables such as target demographics, competitor locations, and seasonality to better identify specific locations within the city to target. 

