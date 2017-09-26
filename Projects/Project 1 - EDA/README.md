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

