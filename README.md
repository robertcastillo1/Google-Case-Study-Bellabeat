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

```
# Clean column names
daily_activity <- clean_names(daily_activity)
sleep_day <- clean_names(sleep_day)
weight_info <- clean_names(weight_info)
```

```
# Remove unused columns
daily_activity_keep <- c('id','activity_date','total_steps','total_distance',
                         'very_active_minutes','fairly_active_minutes', 'lightly_active_minutes',
                         'sedentary_minutes', 'calories')
sleep_day_keep <- c('id', 'sleep_day', 'total_minutes_asleep')
weight_info_keep <- c('id', 'date', 'weight_pounds')

daily_activity <- daily_activity %>% select(all_of(daily_activity_keep))
sleep_day <- sleep_day %>% select(all_of(sleep_day_keep))
weight_info <- weight_info %>% select(all_of(weight_info_keep))
```

Then we need to convert the date columns into a date format using as.Date, parse_date_time, and as.POSIXct:

```
# Change date columns to date format
daily_activity$activity_date <- as.Date(daily_activity$activity_date, format('%m/%d/%Y'))

sleep_day$sleep_day <- parse_date_time(sleep_day$sleep_day, '%m/%d/%Y %I:%M:%S %p')
sleep_day$sleep_day <- as.Date(as.character(sleep_day$sleep_day), format('%Y-%m-%d'))

# weight log requires a little more work
weight_info$date <- parse_date_time(weight_info$date, '%m/%d/%Y %I:%M:%S %p')
weight_info$date <- format(as.POSIXct(weight_info$date, format = '%Y%m%d %H:%M:%S'),
                           format = '%Y-%m-%d')
weight_info$date <- as.Date(weight_info$date, format('%Y-%m-%d'))
```

```
# Change column names for simplicity
colnames(daily_activity)[colnames(daily_activity) == 'activity_date'] <- 'date'
colnames(sleep_day)[colnames(sleep_day) == 'sleep_day'] <- 'date'
```

Once the conversions are complete, merge the dataframes into two main dataframes to maintain the number of records and remove NA values:

```
# Merge dataframes
activity_sleep_merge <- merge(daily_activity, sleep_day, by = c('id', 'date'), all = TRUE)
activity_weight_merge <- merge(daily_activity, weight_info, by = c('id', 'date'), all = TRUE)
sleep_weight_merge <- merge(sleep_day, weight_info, by = c('id', 'date'), all = TRUE)
```

```
# Remove NAs
activity_sleep_merge <- activity_sleep_merge %>% drop_na()
activity_weight_merge <- activity_weight_merge %>% drop_na()
sleep_weight_merge <- sleep_weight_merge %>% drop_na()
```

Finally create a Weekday column and a factor determining a users' Activity Level:

```
# Add Weekday to activity and sleep merged data and turn it into a factor
activity_sleep_merge <- activity_sleep_merge %>% mutate(weekday = weekdays(date))

activity_sleep_merge$weekday <- factor(activity_sleep_merge$weekday,
                                       levels = c('Monday','Tuesday','Wednesday', 'Thursday',
                                                  'Friday', 'Saturday', 'Sunday'))
```

## Analyze
Summary:
Obtain the summary for all columns. We can see that the average amount of time spent per day as sedentary is about 12 hours, lightly active is about 3 and a half hours, and both fairly active and very active less than about half an hour. The average amount of sleep per day is about 7 hours. The average calories burned is about 2400, the average steps is about 8500, and the average distance traveled is about 6 km. Finally the average weight is about 160 pounds. 

```
summary(activity_sleep_merge)
```
![Activity_Sleep_Summary](https://github.com/user-attachments/assets/5b539e74-42f6-4b00-8dc0-86fd03c84241)

```
summary(activity_weight_merge)
```
![Activity_Weight_Summary](https://github.com/user-attachments/assets/3482b64c-e9ea-4a91-bf5a-b6ef530337f8)

Calories:
Based on the boxplot below, we can see that as expected those who were very active have on average more calories burned. Slightly more surprising is the fact that those who were lightly active have on average more calories burned than those in the fairly active or sedentary groups.

```
# Create boxplots of calories burned for each activity level
ggplot(data = activity_sleep_merge, aes(x = activity_level, y = calories,
                                        fill = activity_level)) +
  geom_boxplot() +
  theme(legend.position = 'none') +
  labs(title = 'Calories Burned by Activity Level') +
  ylab('Calories') +
  xlab('Activity Level')
```
![Calories_Activity_Level](https://github.com/user-attachments/assets/3bf7ff8e-b9dd-4041-9055-c28730661cac)

When we look at the scatterplots of the different activity level minutes, we can see that all activity level minutes have a positive correlation with calories burned. Because they are all similar plots, there aren't too many strong conclusions we can make about the ideal activity level to have.

```
# Create scatterplot for very active minutes and calories
ggplot(data = activity_sleep_merge, aes(x = very_active_minutes, y = calories)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Very Active Minutes and Calories')+
  ylab('Calories') +
  xlab('Very Active Minutes')
```
![Very_Calories](https://github.com/user-attachments/assets/119e0b6b-9adc-4c74-a560-f253d4ff8b2c)

```
# Create scatterplot for fairly active minutes and calories
ggplot(data = activity_sleep_merge, aes(x = fairly_active_minutes, y = calories)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Fairly Active Minutes and Calories')+
  ylab('Calories') +
  xlab('Fairly Active Minutes')
```
![Fairly_Calories](https://github.com/user-attachments/assets/94f716e3-0bb5-4404-adad-a0e8b5aac362)

```
# Create scatterplot for lightly active minutes and calories
ggplot(data = activity_sleep_merge, aes(x = lightly_active_minutes, y = calories)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Lightly Active Minutes and Calories')+
  ylab('Calories') +
  xlab('Lightly Active Minutes')
```
![Lightly_Calories](https://github.com/user-attachments/assets/09e00b16-1cac-43e0-b823-5fd3e5fdf8e3)

```
# Create scatterplot for sedentary minutes and calories
ggplot(data = activity_sleep_merge, aes(x = sedentary_minutes, y = calories)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Sedentary Minutes and Calories')+
  ylab('Calories') +
  xlab('Sedentary Minutes')
```
![Sedentary_Calories](https://github.com/user-attachments/assets/e06cb53a-93ba-482e-87f6-77094d7a2b87)

Weekday:
As a result of lightly active individuals having more calories burned than fairly active or sedentary people, I took a closer look at Lightly Active Minutes by Weekday. As can be seen below, Saturday has the highest average lightly active minutes and Sunday has the lowest. During the week, minutes decrease as the week go along, and then increase once the weekend arrives.

```
# Create plot of avg lightly active minutes per Weekday
ggplot(data = activity_sleep_pivot, aes(x = weekday, y = mean_la_minutes)) + 
  geom_bar(stat = 'identity', fill = 'blue') +
  labs(title = 'Average Lightly Active Minutes by Weekday')+
  ylab('Avg. Lightly Active Minutes') +
  xlab('Weekday')
```
![Lightly_Weekday](https://github.com/user-attachments/assets/f3744fb7-4322-4fca-b8ca-651140e473e1)

Sleep:
I wanted to get an idea of the relationship between the different activity level minutes and sleep. Based on the scatterplots below, most activity level minutes have a negative correlation with sleep minutes. Lightly active minutes have almost no correlation with sleep minutes

```
# Create scatterplot for very active minutes and sleep minutes
ggplot(data = activity_sleep_merge, aes(x = very_active_minutes, y = total_minutes_asleep)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Very Active Minutes and Total Minutes Asleep')+
  ylab('Total Minutes Asleep') +
  xlab('Very Active Minutes')
```
![Very_Sleep](https://github.com/user-attachments/assets/3246c3d7-fd2a-461f-8b64-4e837ef104cb)

```
# Create scatterplot for fairly active minutes and sleep minutes
ggplot(data = activity_sleep_merge, aes(x = fairly_active_minutes, y = total_minutes_asleep)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Fairly Active Minutes and Total Minutes Asleep')+
  ylab('Total Minutes Asleep') +
  xlab('Fairly Active Minutes')
```
![Fairly_Sleep](https://github.com/user-attachments/assets/4a2e3865-3c2f-4500-b713-5c978adf1bcb)

```
# Create scatterplot for lightly active minutes and sleep minutes
ggplot(data = activity_sleep_merge, aes(x = lightly_active_minutes, y = total_minutes_asleep)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Lightly Active Minutes and Total Minutes Asleep')+
  ylab('Total Minutes Asleep') +
  xlab('Lightly Active Minutes')
```
![Lightly_Sleep](https://github.com/user-attachments/assets/298dd48b-41bc-4ac5-ab39-36ef15442fac)

```
# Create scatterplot for sedentary minutes and sleep minutes
ggplot(data = activity_sleep_merge, aes(x = sedentary_minutes, y = total_minutes_asleep)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Sedentary Minutes and Total Minutes Asleep')+
  ylab('Total Minutes Asleep') +
  xlab('Sedentary Minutes')
```
![Sedentary_Sleep](https://github.com/user-attachments/assets/3a19c890-07d0-4fb2-9128-ee30bc1a5a95)

Weight:
When looking at activity level minutes and weight, we can see some interesting relationships. Both Fairly active minutes and lightly active minutes have a negative relationship with weight. Oddly enough very active minutes have a positive relationship with weight. When you look at the plot there are very clearly two major weights: around 180 and 130 pounds. So maybe the trend line is not the most indicative of a strong correlation.

```
# Create scatterplot for very active minutes and weight (pounds)
ggplot(data = activity_weight_merge, aes(x = very_active_minutes, y = weight_pounds)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Very Active Minutes and Weight')+
  ylab('Weight (lbs)') +
  xlab('Very Active Minutes')
```
![Very_Weight](https://github.com/user-attachments/assets/72242de9-9604-47e7-990d-6ea3388e7a2e)

```
# Create scatterplot for fairly active minutes and weight (pounds)
ggplot(data = activity_weight_merge, aes(x = fairly_active_minutes, y = weight_pounds)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Fairly Active Minutes and Weight')+
  ylab('Weight (lbs)') +
  xlab('Fairly Active Minutes')
```
![Fairly_Weight](https://github.com/user-attachments/assets/73591cb2-41d8-45f6-95c2-0f86ab8f2ba5)

```
# Create scatterplot for lightly active minutes and weight (pounds)
ggplot(data = activity_weight_merge,
       aes(x = lightly_active_minutes, y = weight_pounds)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Lightly Active Minutes and Weight')+
  ylab('Weight (lbs)') +
  xlab('Lightly Active Minutes')
```
![Lightly_Weight](https://github.com/user-attachments/assets/69eec402-cf62-4ddb-add7-a54224d5daad)

```
# Create scatterplot for sedentary minutes and weight (pounds)
ggplot(data = activity_weight_merge, aes(x = sedentary_minutes, y = weight_pounds)) +
  geom_point(color = 'blue') +
  geom_smooth(method = lm) +
  labs(title = 'Relationship between Sedentary Minutes and Weight')+
  ylab('Weight (lbs)') +
  xlab('Sedentary Minutes')
```
![Sedentary_Weight](https://github.com/user-attachments/assets/6319e24d-233b-4fe4-b081-2b57aadea7ce)

## Share

[Tableau Dashboard](https://public.tableau.com/app/profile/robert.castillo/viz/BellabeatCaseStudy_17332534156220/ActiveMinutesDashboard)

(Include link/file for the PowerPoint presentation)

## Act
Conclusions based on the Analysis:
* Most minutes are spent as sedentary followed by lightly active. Users spend about 12 hours a day in sedentary and about 3 and a half hours in lightly active.
* Those in the very active level burn on average the most calories at about 2900 calories, but lightly active users burn the next most calories at about 2300; more than fairly active at about 2100 calories.
* Individuals tend to be lightly active the most on Saturday and the least on Sunday. As the week goes on, lightly active minutes decrease.
* Most activity levels have a negative correlation with sleep, except for lightly active.
* Fairly active minutes and lightly active minutes have a negative correlation with weight.

Recommendations:
* Obtain more data, especially surrounding weight information, so there can be more comprehensive analysis.
* Create a campaign that encourages members to be lightly active throughout the week, primarily on Sundays and Thursdays. This can be paired with incentives for those who are active throughout the whole week.
* Partner with weight loss companies to help promote maintaining fairly or lightly active lifestyles.
* Bellabeat could use products such as Leaf to remind users to be more active when they are sedentary for prolonged periods of time. 
