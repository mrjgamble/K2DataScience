# NYC MTA Subway Ridership & Weather Analysis

## Introduction

*Note: This project was completed as part of the [K2 Data Science program](http://www.k2datascience.com/).  It is designed to focus on exploratory data analysis (data cleaning, basic statistical analysis, and data visulaization).  The exploratory data analysis will then identify next steps that could be used for further analysis.*

In this project, our client is a clothing retailer who is looking to plan flash sales and pop-up events around busy and pleasant weather days in NYC.  In particular the question we are looking to answer is as follows:

> **What days are historically busy and pleasant in NYC?**

We want to help the clothing retailer pick several days over the next few months that will maximize their exposure and minimize the chance of poor weather.
* Using turnstile data from the MTA will be our way to indicate the busyness of a day. 
* Weather data supplied from the NOAA will be used to identify pleasant weather days. 

This analysis will only outline an EDA, laying the groudwork for futher analysis, including predictive analysis if desired. 

## Approach
Prior to conducting any data analysis, it was necessary to perform data cleansing of both data sets.  

**MTA ridership data**

473 mta stations were analyzed, with turnstile data collected over the months of September to October 2016.  I originally inteded to utillize data from 2014 & 2015 in order to average the ridership numbers per day across several years; however, after investigation of the data, I found that data from 2014 & 2015 were not usable for this analysis as the MTA has changed the way stations were classified (the data from past years did not align with 2016).

(See ridership cleansing notebook for further details)

**NOAA weather data**

The weather data consisted of daily weather temperature (max, min, average), as well as precipitation, snowfall, and snow depth readings.  

(see weather cleansing ntoebook for further details)

With the data cleaned, analysis of the data could be peformd utilizing Pandas, Matplotlib, and Seaborn packages. 

The details of the analysis is summarized below.

## Exploring Ridership Data
(See ridership analysis notebook for details)

"In 2016, average weekday subway ridership was 5.7 million" [source](http://web.mta.info/nyct/facts/ffsubway.htm)

With an average of 5.7 million riders per day, NYC's subway is a great way to determine how busy the city is on any given day.  

Summarizing the total ridership per day for all station

The MTA has recorded ridership for 

## Exploring Weather Data
(See weather analysis notebook for details)

## Summary & Conclusions
In summary of the above analysis, the following recommendations have been reached:

1. 
2. 
3.
4.
5.


## Next Steps
In order to carry this analysis to the next steps, we can begin building a predictive model that allows us to see if a suggested day will be pleasant and busy.  

As mentioned throughout the analysis, several data concerns can be investigated further to improve the accuracy of the model:
* We only looked at a single year of ridership to determine historical ridership values.  We could incorporate additional years to normalize the data and achieve a better predictive model

In addition to this, additional variables can be used to improve a model.  For example
* Include additional variables for determining a 'pleasant day'.  Examples of these may be: cloud coverage, dew point humidity, etc.  
* Include additional market variables such as target demographics, competitor locations, seasonality to better identify specific locations within the city to target. 

