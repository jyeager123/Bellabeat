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

Data Analysts tools used: Microsoft Excel, Google Cloud, BigQuery, Power BI

I first downloaded the CSV files and started cleaning them in Microsoft Excel with the following stps.

  1. Opened the"dailyActivity_merged" file, which already contained the data from the dailyCalories_merged, dailyIntensities_merged, and dailySteps_merged files.             I then renamed the dailyActivities_merged file to "daily_activity".
  2. Opened the sleepDay_merged file and added that data to the new daily_activity file. Several cells had no values in them after the data was transferred, so I             entered a 0 for those values.
  3. Renamed the FairlyActiveMinutes column to ModerateActiveMinutes, renamed the ModeratelyActiveDistance column to ModerateActiveDistance, renamed                         LightlyActiveMinutes column to LightActiveMinutes, and renamed SedentaryMinutes column to SedentaryActiveMinutes.
  4. Formatted the TotalDistance, TrackerDistance, LoggedActivitiesDistance, VeryActiveDistance, ModerateActiveDistance, LightActiveDistance, and                             SedentaryActiveDistance columns so that the values were no more than decimal places.
  5. Created a new column called MinutesAwakeInBed that calculated how long someone was in bed, but awake, by TotalMinutesInBed values by that of the                         TotalMinutesAsleep values.
  6. Opened the hourlyCalories_merged and hourlySteps_merged files and combined them into a new file called hourly_steps_calories.
  7. Opened the hourlyIntensities_merged file and saved it as a new file called hourly_intensities I then converted the ActivityHour column to a Date data type and           formnatted it as mm/dd/yy hh:mm.
  
I then uploaded the daily_activity, hourly_steps_calories, and hourly_intensities tables to BigQuery. I also uploaded the heartrate_seconds_merged table to Google Cloud, as the file was to large to open in Excel. I then uploaded the table to BigQuery from Google Cloud and renamed it as heartrate_in_seconds.

Schema of the tables:
daily_activity
  -Id: Integer
  -ActivityDate: Date
  TotalSteps: Integer
  TotalSleepRecords: Integer
  TotalMinutesAsleep: Integer
  TotalMinutesInBed: Integer
  MinutesAwakeInBed: Integer
  TotalDistance: Float
  TrackerDistance: Float
  LoggedActivitiesDistance: Float
  VeryActiveDistance: Float
  ModerateActiveDistance: Float
  LightActiveDistance: Float
  SedentaryActiveDistance: Float
  VeryActiveMinutes: Integer
  ModerateActiveMinutes: Integer
  LightActiveMinutes: Integer
  SedentaryActiveMinutes: Integer
  Calories: Integer
  
heartrate_in_seconds<br>
  Id: Integer<br>
  Time: String
  Value: Intger
  
hourly_intensities
  Id: Integer
  ActivityHour: Timestamp
  TotalIntensity: Integer
  AverageIntensity: Float
  
hourly_steps_calories
  Id: Integer
  ActivityHour: Timestamp
  StepTotal: Integer
  Calories: Integer
  
  
  

