# Bellabeat Case Study
This project is a part of the Google Data Analytics Certification. In it I work as a data analyst for a company Called Bellabeat, which is a tech company that creates smart products designed for woman to track their health data. The goal of this project is to analyze smart device data, that is not related to Bellabeat, in order to get a better understanding of how consumers use these devices. 
# Bellabeat, the company
Bellabeat is a high tech company that focuses on the overall health of woman by developing different smart products. The company has four different products: the Bellabeat app, Leaf, Time, and Spring. 

The Bellabeat app generates the users health data, which includes things such as sleep, steps, distance traveled, intake of calories, and intensity. Leaf is a fashionable product that can be worn as a necklace, bracelet, or clip. This products create data that is then sent to the Bellabeat app. The Time product does the same thing as that of the Leaf, but is in the form of a watch that can be worn around the wrist. Spring is a water bottle that tracks how much water someone drinks in a day.

Bellabeat also has a membership available for customers. This membership provides customized advice on sleep, daily activity, nutrition, and more.

# Purpose of the Case Study
As a data analyst, I am tasked with reviewing smart device data that is not related to Bellabeat. My goal is to track trends in the smart device usage and see how these trends apply to Bellabeat customers. I have also been asked to recommend different marketing strategies to increase awareness and profitability for the company.

# About the Data
The data I will be analyzing is from FitBit. It is a public domain dataset that has been provided Mobius in Kaggle. The link to the open source dataset can be found here. [FitBit Tracker Dataset](https://www.kaggle.com/datasets/arashnic/fitbit)

The data provided is from a survey of woman who wear a smart device to track their health and daily activity. The data has a few limitations as only 33 woman were surveyed across a one month period from 4/12/16 to 5/12/16.

# The Case Study
#### Cleaning and Transforming the Data

Data Analysts tools used: Microsoft Excel, Google Cloud, BigQuery, Power BI

I first downloaded the CSV files and started cleaning them in Microsoft Excel with the following steps.

  1. Opened the dailyActivity_merged file, which already contained the data from the dailyCalories_merged, dailyIntensities_merged, and dailySteps_merged files.             I then renamed the dailyActivity_merged file to daily_activity.
  2. Opened sleepDay_merged and added that data to the new daily_activity file. Several cells had no values in them after the data was transferred, so I                     entered a 0 for those values.
  3. Renamed the FairlyActiveMinutes column to ModerateActiveMinutes, renamed the ModeratelyActiveDistance column to ModerateActiveDistance, renamed                         LightlyActiveMinutes column to LightActiveMinutes, and renamed SedentaryMinutes column to SedentaryActiveMinutes.
  4. Formatted the TotalDistance, TrackerDistance, LoggedActivitiesDistance, VeryActiveDistance, ModerateActiveDistance, LightActiveDistance, and                             SedentaryActiveDistance columns so that the values were no more than 2 decimal places.
  5. Created a new column called MinutesAwakeInBed that calculated how long someone was in bed, but awake, by subtracting the TotalMinutesInBed values from the               TotalMinutesAsleep values.
  6. Opened the hourlyCalories_merged and hourlySteps_merged files and combined them into a new file called hourly_steps_calories.
  7. Opened hourlyIntensities_merged and saved it as a new file called hourly_intensities. I then converted the ActivityHour column to a Date data type and                   formatted it as mm/dd/yy hh:mm.
  
After I cleaned and formatted the files, I uploaded the daily_activity, hourly_steps_calories, and hourly_intensities tables straight to BigQuery. I also uploaded the heartrate_seconds_merged table to Google Cloud, as the file was too large to open in Excel. I then uploaded that table to BigQuery from Google Cloud and renamed it as heartrate_in_seconds by using the following SQL query.

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
  -Value: Integer<br>
  
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
My first order of business was to figure out how many woman use a smart device to track their workout, track their sleep, and track their heartrate. We will consider a workout as the sum of the VeryActiveMinutes and ModerateActiveMinutes that is greater than 10 minutes. I ran a few queries to find the results.

```
---Finds the number of woman who use a smart product to track their workout and who don't use a smart product to track their workout---
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

Here we can see that customers much prefer to track their workouts and sleep with a smart product compared to tracking their heartrate.

Since tracking sleep and tracking a workout are some popular ways to use a smart device, I then wanted to figure out how many women use it to track both their sleep and workouts and how many use is to track one or the other.

```
---Counts the number of customers who use a smart device to track their sleep and their workouts---
SELECT 'use product' AS to_track_sleep_and_workout, COUNT(*) AS number_of_customers
FROM (SELECT COUNT(DISTINCT Id)
FROM `my-first-project-374001.Bellabeat.daily_activity`
GROUP BY id
HAVING AVG(VeryActiveMinutes) + AVG(ModerateActiveMinutes) > 10 AND AVG(TotalSleepRecords)>0)
```

|Track Sleep and Workout|	Number of Customers|
|--------|--------|
|Use Product|	17|

We can see that roughly half (17 of the 33 woman surveyed) use a smart product to track their sleep as well as their workout routine.

Now that I have figured out how woman use a smart product, I wanted to analyze some of the trends in the data generated from the smart product. I first wanted to find out how someone's workout impacted their minutes lying awake in bed. Again, we can consider a workout where the sum of the VeryActiveMinutes and ModerateActiveMinutes is greater than ten minutes.

```
---Finds the minutes awake in bed and the workout distance---
SELECT MinutesAwakeInBed, (VeryActiveDistance + ModerateActiveDistance) 
AS WorkoutDistance
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE MinutesAwakeInBed>0 AND (VeryActiveMinutes + ModerateActiveMinutes)>10
```

```
---Finds the minutes awake in bed and the workout minutes---
SELECT MinutesAwakeInBed, (VeryActiveMinutes + ModerateActiveMinutes) AS WorkoutMinutes
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE MinutesAwakeInBed>0 AND (VeryActiveMinutes + ModerateActiveMinutes)>10
```

```
---Finds the minutes awake in bed and the total steps---
SELECT MinutesAwakeInBed, TotalSteps
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE MinutesAwakeInBed>0 AND TotalSteps>0
```

![Minutes Awake in Bed vs  Daily Activity](https://user-images.githubusercontent.com/102268286/222931464-23e4a4b0-74c6-42c0-a9ec-05d6c631135c.PNG)

As we can see a woman's workout routine and total steps taken in a day had close to no impact on their time lying awake in bed, as the correlation of all three is close to zero.

Next, I wanted to find out how a woman's workout routine and steps taken per day impacted her total calorie intake in a day.

```
---Finds the calorie intake and workout distance---
SELECT Calories, (VeryActiveDistance + ModerateActiveDistance) AS WorkoutDistance
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE Calories>0 AND (VeryActiveMinutes + ModerateActiveMinutes)>10
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
![Calories vs  Daily Activity](https://user-images.githubusercontent.com/102268286/222931376-c9c22c40-b057-4020-9d20-f0140b81ab38.PNG)

Here we see a much stronger correlation compared to that of the previous scatter plots. The intake of calories to workout minutes and total steps is over 0.50, while the correlation to calories to workout distance is closer to 0.40.

Now I wanted to find out which days were the most popular for working out.

```
---Finds the number of workouts done per day---
SELECT CASE EXTRACT(DAYOFWEEK FROM ActivityDate)
            WHEN 1 THEN 'Sunday'
            WHEN 2 THEN 'Monday'
            WHEN 3 THEN 'Tuesday'
            WHEN 4 THEN 'Wednesday'
            WHEN 5 THEN 'Thursday'
                  WHEN 6 THEN 'Friday'
      WHEN 7 THEN 'Saturday'
      END AS day_of_week,
      COUNT(*) AS number_of_workouts
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE (VeryActiveMinutes + ModerateActiveMinutes)>10
GROUP BY day_of_week
ORDER BY number_of_workouts DESC
```
|Day of Week | Number of Workouts	|
|----------|----------|
|Wednesday |	87 |	
|Tuesday | 87	|
|Thursday | 81	|
|Monday |	74	|
|Saturday |	68	|
|Friday |	64	|
|Sunday	| 57	|

As we can see, the woman surveyed prefer to work out during the weekdays compared to that of the weekend.

Now that we have the most popular days to exercise, I wanted to find what time of day they like to work out.
```
---Finds the most popular time of day to workout---
SELECT EXTRACT(HOUR FROM ActivityHour) AS hour_of_day,
      ROUND(AVG(TotalIntensity), 2) AS AverageIntensity
FROM `my-first-project-374001.Bellabeat.hourly_intensities`
GROUP BY hour_of_day
ORDER BY hour_of_day
```

![Intensity by Hour of Day](https://user-images.githubusercontent.com/102268286/222930684-7a736a44-a9cb-47da-b45f-a8a70e5131bb.PNG)

The most popular time of day for woman to workout tends to be in the evening between 5pm and 7pm.

I also wanted to get a breakdown of the distance traveled per day by the woman surveyed, as well as how their minutes are spent in a day. 

```
---Finds the type of distance traveled per day---
SELECT ActivityDate,
      LightActiveDistance, 
      ModerateActiveDistance, 
      VeryActiveDistance,
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE TotalDistance>0
```

```
---Finds how minutes are spent during the day---
SELECT ActivityDate,
      SedentaryActiveMinutes,
      LightActiveMinutes,
      ModerateActiveMinutes,
      VeryActiveMinutes
FROM `my-first-project-374001.Bellabeat.daily_activity`
WHERE (SedentaryActiveMinutes + LightActiveMinutes + ModerateActiveMinutes + VeryActiveMinutes)>0
```

![Distance per Day](https://user-images.githubusercontent.com/102268286/222930992-4a3c3616-5b8e-4087-97dd-85aa6a8a74d7.PNG)

![Minutes Spent per Day](https://user-images.githubusercontent.com/102268286/222931026-fc58bc78-7ae7-4320-a505-ef39dc4e1776.PNG)

We see that the woman surveyed typically go about 5 to 6 miles a day, with a little less than half of that being working out. On the other hand, their time working out per day makes up very little of their daily schedule.

# Conclusion
My analysis has determined that the woman surveyed using the FitbBit data are inclined to use a smart device to track their workout routines as well as their sleep. However, only half of them use it to track both their workouts and sleep, while the other half track either one or the other. 

In regard to the workout routine itself, it does not have much of an impact on how fast a woman falls asleep. On the other hand, their workout routine, as well as their total steps taken in a day, has an impact on their calorie intake as those who have worked out longer, traveled farther while working out, and had more steps in a day, ingest more calories.

Another aspect I found through my analysis is the preference of when to workout. The woman surveyed like to work out more during the weekdays, with an evening workout being the most popular. Also, a significant portion of their distance traveled in a day, tends to be when they are working out.

Bellabeat can certainly glean a lot from these findings. My first recommendation to the marketing team would be to offer customized advertisements on the Bellabeat App. For those customers who work out and take more steps in a day, Bellabeat can offer advertising space on their app to restaurants and grocery stores that promote a healthy diet. This will increase overall revenue and create a positive relationship with companies that offer healthy food products. Another possible advertising route is for sports apparel companies to advertise on the app during specific hours. Since the woman surveyed prefer to work out during the evenings of a weekday, Bellabeat can offer advertising space to those apparel companies at that time.

Another recommendation would be to offer different types of products, as only half of the woman surveyed use a smart device to track both their workout and sleep together. An example of this would be to create a new type of product, like a ring, that tracks only details about sleep. This type of technology can have the ability to provide more in depth details about ones sleep patterns, such as the different stages of sleep.
