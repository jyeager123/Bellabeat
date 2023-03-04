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
  
I then uploaded the daily_activity, hourly_steps_calories, and hourly_intensities tables to BigQuery. I also uploaded the heartrate_seconds_merged table to Google Cloud, as the file was to large to open in Excel. I then uploaded the table to BigQuery from Google Cloud and renamed it as heartrate_in_seconds by using the following SQL query.

```
---Creates table called heartrate_seconds---
CREATE TABLE `my-first-project-374001.Bellabeat.heartrate_in_seconds`
AS SELECT SAFE_CAST(id AS int64) AS id,
      Time AS Time,
      SAFE_CAST(Value AS int64) AS Value
      FROM `my-first-project-374001.Bellabeat.heartrate_seconds`
```

Schema of the tables:<br>

daily_activity<br>
  -Id: Integer<br>
  -ActivityDate: Date<br>
  -TotalSteps: Integer<br>
  -TotalSleepRecords: Integer<br>
  -TotalMinutesAsleep: Integer<br>
  -TotalMinutesInBed: Integer<br>
  -MinutesAwakeInBed: Integer<br>
  -TotalDistance: Float<br>
  -TrackerDistance: Float<br>
  -LoggedActivitiesDistance: Float<br>
  -VeryActiveDistance: Float<br>
  -ModerateActiveDistance: Float<br>
  -LightActiveDistance: Float<br>
  -SedentaryActiveDistance: Float<br>
  -VeryActiveMinutes: Integer<br>
  -ModerateActiveMinutes: Integer<br>
  -LightActiveMinutes: Integer<br>
  -SedentaryActiveMinutes: Integer<br>
  -Calories: Integer<br>
  
heartrate_in_seconds<br>
  -Id: Integer<br>
  -Time: String<br>
  -Value: Intger<br>
  
hourly_intensities<br>
  -Id: Integer<br>
  -ActivityHour: Timestamp<br>
  -TotalIntensity: Integer<br>
  -AverageIntensity: Float<br>
  
hourly_steps_calories<br>
  -Id: Integer<br>
  -ActivityHour: Timestamp<br>
  -StepTotal: Integer<br>
  -Calories: Integer<br>
  
#### The Analysis
My first order of business was to figure out how many woman use a smart device to track their workout, track their sleep, and track their heartrate. We will consider a workout as the sum of the VeryActiveMinutes and ModerateActiveMinutes that is greater than 10 minutes. I ran a few queries to find the results

```
---Finds the number of woman who use a smart product to workout and who don't use a smart product to workout---
WITH working_out AS(
      SELECT 'use product' AS to_workout, COUNT(id) as number_of_customers 
      FROM (SELECT id, ROUND(SUM(ModerateActiveMinutes)/31 + SUM(VeryActiveMinutes)/31,2) AS working_out_minutes
      FROM `my-first-project-374001.Bellabeat.daily_activity`
      GROUP BY id
      HAVING working_out_minutes>10)
),
  not_working_out AS(
      SELECT "don't use product" AS to_workout, COUNT(id) as number_of_customers
      FROM (SELECT id, ROUND(SUM(ModerateActiveMinutes)/31 + SUM(VeryActiveMinutes)/31,2) AS working_out_minutes
      FROM `my-first-project-374001.Bellabeat.daily_activity`
      GROUP BY id
      HAVING working_out_minutes<10)
)
SELECT * FROM working_out
UNION ALL 
SELECT * FROM not_working_out
```

```
---Finds the number of woman who use a smart product to track their sleep and who don't use a smart product to track their sleep---
WITH use_to_track_sleep AS (
      SELECT 'use product' AS to_track_sleep, COUNT(DISTINCT id) AS number_of_customers
      FROM `my-first-project-374001.Bellabeat.daily_activity` 
      WHERE TotalSleepRecords>0
),
  dont_use_to_track_sleep AS (
      SELECT "don't use product" AS to_track_sleep, COUNT(sleep_records) AS number_of_customers
      FROM (SELECT Id, AVG(TotalSleepRecords) AS sleep_records
      FROM `my-first-project-374001.Bellabeat.daily_activity`
      GROUP BY Id)
      WHERE sleep_records=0
)
SELECT * FROM use_to_track_sleep
UNION ALL
SELECT * FROM dont_use_to_track_sleep
```

```
---Finds the number of woman who use a smart product to track their heartrate and who don't use a smart product to track their heartrate---
SELECT 'use product' AS track_heartrate, COUNT(DISTINCT id) AS number_of_customers
FROM `my-first-project-374001.Bellabeat.heartrate_in_seconds`
UNION DISTINCT
SELECT "don't use product" AS track_heartrate, COUNT(DISTINCT not_heartrate.id) AS number_of_customers
FROM `my-first-project-374001.Bellabeat.daily_activity` not_heartrate
LEFT JOIN `my-first-project-374001.Bellabeat.heartrate_in_seconds` heartrate
ON heartrate.id=not_heartrate.id
WHERE heartrate.id IS NULL
ORDER BY track_heartrate DESC
```

![Product Use](https://user-images.githubusercontent.com/102268286/222925270-033e1b21-549e-483d-badb-657194d254a4.PNG)

Here we can see that customers much prefer to track their workouts and sleep with a smart prduct compared to tracking their heartrate.
  
Now that I have figured out how woman use a smart product, I wanted to analyze some of the trends in the data generated from the smart product. I first wanted to find out how someones workout impacted their minutes laying awake in bed. Again, we can consider a workout where the sum of the VeryActiveMinutes and ModerateActiveMinutes is greater than 10.

```
---Finds the minutes awake in bed and the workout distance in a day---
SELECT MinutesAwakeInBed,(VeryActiveDistance + ModerateActiveDistance) 
AS WorkoutDistance
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE MinutesAwakeInBed>0 AND (VeryActiveMinutes + ModerateActiveMinutes)>10
```

```
---Finds the minutes awake in bed and the workout minutes in a day---
SELECT MinutesAwakeInBed,(VeryActiveMinutes + ModerateActiveMinutes) AS WorkoutMinutes
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE MinutesAwakeInBed>0 AND (VeryActiveMinutes+ModerateActiveMinutes)>10
```

```
---Finds the minutes awake in bed and the total steps in a day
SELECT MinutesAwakeInBed, TotalSteps
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE MinutesAwakeInBed>0 AND TotalSteps>0
```

![Minutes Awake in Bed vs  Daily Activity](https://user-images.githubusercontent.com/102268286/222928289-9bdb5388-f09f-454f-83f8-242e5f0dfd8c.PNG)

As we can see a womans' workout routine and total steps taken in a day had close to no impact on their time laying awake in bed, as the correlation of all thhree is close to zero.

Next I wanted to find out how a womans workout routine and steps taken per day impacted her total calorie intake in a day.

```
---Finds the calorie intake and workout distance---
SELECT Calories, (VeryActiveDistance + ModerateActiveDistance) AS WorkoutDistance
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE Calories>0 AND (VeryActiveDistance + ModerateActiveDistance)>10
```

```
---Finds the calorie intake and workout minutes---
SELECT Calories, (VeryActiveMinutes + ModerateActiveMinutes) AS WorkoutMinutes
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE Calories>0 AND (VeryActiveMinutes + ModerateActiveMinutes)>10
```

```
---Find the calorie intake and total steps---
SELECT Calories, TotalSteps
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE TotalSteps>0 AND Calories>0
```

