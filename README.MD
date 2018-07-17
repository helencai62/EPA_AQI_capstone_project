# Predicting Air Quality Index in San Francisco
Helen Cai
July 17, 2018

1. Summary:

The Environmental Protection Agency (EPA) has collected and stored air quality index (AQI) data at the www.airnow.gov site for the past 10+ years for reporting areas across the US. Reporting areas are various locales served by local weather stations. In California, there are 165 reporting areas served by 31 weather stations.

AQI values range between 0-500, with outliers ranging into the tens of thousands, and are categorized into 6 groups by value: 
Good 0-50
Moderate 51-100
Unhealthy for Sensitive Groups 101-150
Unhealthy 151-200
Very Unhealthy 201-300
Hazardous 301-500

This is commonly reported by newspapers and local weather sources to help people stay aware of their environment and manage pre-existing health conditions.

For prediction, I am using time-series and spatial modeling to predict AQI value and logistic regression to predict AQI category in San Francisco (compared to this past year's worth of data from June 2017-2018). The metric used for AQI values is mean absolute error and the metric used for AQI categories is accuracy score.
    
For inference, my goal is to use Bayesian inference principles to estimate the true distribution of AQI categories in the city from the sample collected here. The metric used here is the percentage of Good vs Poor AQI days.

2. Statistical methods:

For prediction of AQI value, the metric used here is mean absolute error of predicted AQI values. The MAE of auto-regressive models tuned for parameter p for lag, number of days included in features, is compared against the MAE of geo-spatial models tuned for parameter n for number of closest reporting areas to desired area (SF) ranked by relative distance.
For prediction of AQI category, the metric used is the accuracy of correctly predicted AQI categories for each date over the period 6/2017 - 6/2018. The EPA also forecasts for AQI value and category for up to 5 days in advance, which I use as a baseline. The baseline accuracy of these forecasts hover between 60-70% for San Francisco (high-variability in AQI occurs in the summertime due to fires and other natural disasters). I train the logistic regression and spatial models over the San Francisco reporting area data as well as n number of closest reporting areas weighted by distance. The logistic regression performs at or above 71% accuracy with various n areas. I also use the time-series and spatial models to predict AQI value within each category. In these cases, I can define success as +/- x number above or below the actual observed AQI value.

For inference, the metric used here is the percentage of Good vs Poor AQI days. I have furthered grouped the AQI categories: Good AQI day includes Good and Moderate, while Poor AQI day includes Unhealthy for Sensitive Groups, Unhealthy, Very Unhealthy, and Hazardous. I look at the data on a monthly period and update the prior Bayesian belief with each new month of incoming data. I started with an initial prior belief of 50% Good and 50% Poor AQI days and found the updated Bayesian belief to exceed this: over the latter six months of 2017, the percentage of Good vs Poor AQI day is over 80% Good. (Note: This is reporting area specific.)

3. Modeling + Model preparation:

I chose the logistic regression for modeling multi-class classification with AQI category, and the time-series and spatial models for forecasting AQI values. 

In preparation, I cleaned the dataset to return one AQI value and category for each date -- in the initial dataset, there are multiple observations per day at various times throughout the day and there are many missing values for either specific dates or reporting areas. I approached the missing data values by sampling from the distribution of AQI values in that reporting area by month and returning a single, first observation per date per reporting area. Alternative methods excluding missing date values from the overall accuracy metric and averaging observations per date.

For additional features, I created a Season feature as summertime shows the highest variability in AQI values and created a filter for reporting area location in Northern CA vs Southern CA. Next steps are to parse discussion text for alarm words such as "fire" or "thunderstorm".

To tune for hyperparameters in my models, I used various n closest reporting areas, such as 3 or 5 reporting area locations closest to San Francisco and regularized. I also evaluated the models comparing various classification metrics such as accuracy, mean absolute error, and Hamming Loss.
