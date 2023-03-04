# Bellabeat Case Study
This project is apart of the Google Data Analytics Certification. In it I work as a data analyst for a company Called Bellabeat, which is a tech company that creates smart products designed for woman that track their health data. The goal of this project is to analyze smart device data, that is not related to Bellabeat, in order to get a better understanding of how consumers use these devices. 
# Bellabeat, the company
Bellabeat is a high tech company that focuses on the overall health of woman by developing different smart products. The company has four different products: the Bellabeat app, Leaf, Time, and Spring. 

The Bellabeat app generates the users health data, which includes things such as sleep, steps, distance traveled, intake of calories, and intensity. Leaf is a fashionble product that can be worn as a necklace, bracelet, or clip. This products create data that is then sent to the Bellabeat app. The Time product does the same thing as that of the Leaf, but is in the form of watch thay can be worn around the wrist. Spring is a water bottle that tracks how much water someone drinks in a day.

Bellabeat also has a membership available for customers. This membership provides customized advice on sleep, daily activity, nutrition, and more.

# Purpose of the Case Study
As a data analyst, I am tasked with reviewing smart device data that is not related to Bellabeat. My goal is to track trends in the smart device usage and see how these trends apply to Bellabeat customers. I have also been asked to recommend different marketing strategies to increase awareness and profitability for the company.

# About the Data
The data I will be analyzing is from FitBit. It is a public domain dataset that has been Mobius in Kaggle. The link to the open source dataset can be found here. [FitBit Tracker Dataset](https://www.kaggle.com/datasets/arashnic/fitbit)

The data provided is from a survey of woman who wear a smart device to track their health and daily activity. The data has a few limitations as only 33 woman were surveyed across a one month period from 4/12/16 to 5/12/16.

# The Case Study
#### Cleaning and Transforming the Data

Data Analysts tools used: Microsoft Excel, BigQuery, Power BI

I first downloaded the CSV files and started cleaning them in Microsoft Excel with the following stps.

  1. Opened the"dailyActivity_merged" file, which already contained the data from the "dailyCalories_merged", "dailyIntensities_merged", and "dailySteps_merged" files.       I then renamed the "dailyActivities_merged" file to "daily_activity".
  2. Opened the "sleepDay_merged" file and added that data to the new "daily_activity" file.
  3. Renamed the FairlyActiveMinutes column to ModerateActiveMinutes, renamed the ModeratelyActiveDistance column to ModerateActiveDistance, renamed                         LightlyActiveMinutes column to LightActiveMinutes, and renamed SedentaryMinutes column to SedentaryActiveMinutes.
  4. Formatted the TotalDistance, TrackerDistance, LoggedActivitiesDistance, VeryActiveDistance, ModerateActiveDistance, LightActiveDistance, and                             SedentaryActiveDistance columns so that the values were no more than decimal places.

