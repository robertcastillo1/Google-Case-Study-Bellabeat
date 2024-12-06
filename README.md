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

## Analyze
Summary:
Obtain the summary for all columns. We can see that the average amount of time spent per day as sedentary is about 12 hours, lightly active is about 3 and a half hours, and both fairly active and very active less than about half an hour. The average amount of sleep per day is about 7 hours. The average calories burned is about 2400, the average steps is about 8500, and the average distance traveled is about 6 km. Finally the average weight is about 160 pounds. 

(Include code and output for the summaries of the 2 datasets)

Calories:
Based on the boxplot below, we can see that as expected those who were very active have on average more calories burned. Slightly more surprising is the fact that those who were lightly active have on average more calories burned than those in the fairly active or sedentary groups.

(Include code and plot for Calories burned by activity level)

When we look at the scatterplots of the different activity level minutes, we can see that all activity level minutes have a positive correlation with calories burned. Because they are all similar plots, there aren't too many strong conclusions we can make about the ideal activity level to have.

(Include code and plots for calories burned by activity type minutes)

Weekday:
As a result of lightly active individuals having more calories burned than fairly active or sedentary people, I took a closer look at Lightly Active Minutes by Weekday. As can be seen below, Saturday has the highest average lightly active minutes and Sunday has the lowest. During the week, minutes decrease as the week go along, and then increase once the weekend arrives.

(Include code and plot for Average Lightly Active Minutes by Weekday)

Sleep:
I wanted to get an idea of the relationship between the different activity level minutes and sleep. Based on the scatterplots below, most activity level minutes have a negative correlation with sleep minutes. Lightly active minutes have almost no correlation with sleep minutes

(Include code and scatterplots for all 4 activity level types and sleep minutes)

Weight:
When looking at activity level minutes and weight, we can see some interesting relationships. Both Fairly active minutes and lightly active minutes have a negative relationship with weight. Oddly enough very active minutes have a positive relationship with weight. When you look at the plot there are very clearly two major weights: around 180 and 130 pounds. So maybe the trend line is not the most indicative of a strong correlation.

(Include code and scatterplots for all 4 activity level types and weight)
