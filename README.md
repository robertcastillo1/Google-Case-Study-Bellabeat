# Google Case Study: Bellabeat

## Scenario
Bellabeat is a high-tech company that manufactures health-focused smart products. Since it was founded in 2013, Bellabeat has grown quickly and is primarily positioned as a tech-driven wellness company for women. By 2016 the company created 5 primary products: the Bellabeat app, Leaf, Time, Spring, and Bellabeat membership. While Bellabeat is a successful company, analysis of consumer data would allow for more opportunities to grow. As part of the marketing analytics team we have been tasked with analyzing smart device data to gain insight into how customers are already using their smart devices. Using this information, the team can provide recommendations to guide marketing strategy for Bellabeat.

## Ask
Business Task: Analyze smart device data to gain insight on consumers and provide marketing strategy recommendations to help grow Bellabeat.

## Prepare
The dataset we will be using is the [Fitbit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit) that is made available through [Mobius](https://www.kaggle.com/arashnic). The data consists of 30 Fitbit users who consented to the submission of their personal tracker data, which includes information about minute-level output for physical activity, heart rate, and sleep monitoring. The data is publicly available through Kaggle, and analysis of the 18 available CSVs of smart device data will provide us and Bellabeat with insight into user trends.

There are some concerns with the data, primarily that there is data from only 30 users. While this is acceptable, more data would be preferred for a more comprehensive analysis. In addition, the data is from 2016 so it might be a bit outdated and more recent data would help provide insights into more modern trends.

## Process
To process the data we first clean the data in the three primary tables: daily_activity, sleep_day, and weight. We start with cleaning the column names and removing unused columns:
(Include code for column cleaning and removal)

Then we need to convert the date columns into a date format using as.Date, parse_date_time, and as.POSIXct:
(Include code for converting date columns)

Once the conversions are complete, merge the dataframes into two main dataframes to maintain the number of records and remove NA values:
(Include code for merge dataframes (activity_sleep and activity_weight) and removing NAs)

Finally create a Weekday column and a factor determining a users' Activity Level:

(Include the code for creating Weekday and Activity_Level)
