# Cyclistic Case Study Report

By Kevin Chin

Published v02a: 6 October, 2023

Last Edited: 6 October, 2023

 

TODO

- [x]  Introduction
- [x]  Data Collection
- [x]  Data Processing
    - [x]  Cleaning
    - [x]  Transformations
- [ ]  Analysis and Findings
    - [x]  Storytelling with data
        - [x]  Visualizations
- [x]  Recommendations (3 of them)
- [ ]  SQL queries equivalent to R code for:
    - [ ]  Appendix 1: Data cleaning
    - [ ]  Appendix 2: Data transformations
- [ ]  Works Cited
- [ ]  Executive Summary (i.e. an abstract)
- [ ]  Appendices
    - [x]  Appendix 3: R code for data analysis
    - [ ]  Appendix 4: Statistical Analysis

## Executive Summary

## Table of Contents

1. Introduction
2. Data Collection
3. Data Processing
4. Analysis and Findings
5. Recommendations
6. Appendix 1: R code and SQL queries for data cleaning
7. Appendix 2: R code and SQL queries for data transformations
8. Appendix 3: R code for data analysis
9. Appendix 4: Statistical Analysis
10. Works Cited

## Introduction

Cyclistic is a fictional bike ridesharing company. In this case study we’ll analyze real-world bike-sharing data from Divvy Bikes, a bike-sharing company in Chicago, and use it as a placeholder for Cyclistic’s data. 

The problem: at Cyclistic, we’ve determined that our members are much more profitable than our casual “pay-as-you-go” riders. The purpose of this study is to use data from bike trips to gain insight into how casuals and members use our bikes. These insights will be leveraged to provide our marketing team with recommendations to develop a marketing strategy to convert casuals into members. 

## Data Collection

The ridership data used in this analysis was sourced from [this database of rides](https://divvy-tripdata.s3.amazonaws.com/index.html) from Divvy Bikes, which is free to use.

Monthly ride data from July-2022 to June-2023 was gathered in CSV format. This consists of a years-worth of the most recent ridership data available from Divvy Bikes. By analyzing a full year of rides, we can observe how casuals and members use the bikes through the seasons.

The dataset contains the following categories for each observation: a unique ride id, the type of bike used, start and end timestamps, start and end bike stations, start and end coordinates, and whether the user was a member or casual. 

## Data Processing

First, the data was cleaned. The steps taken to clean the data are enumerated and explained below:

| Step | Process | Reasoning |
| --- | --- | --- |
| 1 | Consolidate 12 months of ride data into one data frame and check that it’s all present | To ensure the data is comprehensively cleaned and to avoid repeating the same steps for 12 months, we’ll combine the data together so all 12 months are cleaned simultaneously |
| 2 | Eliminate duplicate entries  | Duplicate entries were eliminated by searching for any repeated ride id’s. In this case, none were found so there are no duplicates  |
| 3  | Truncate the data so that it only contains rides between July-22 and June-23 | Our desired sample is exactly one year long, according to ride start date and time. |
| 4 | Remove rides with negative ride time | Ride time cannot be negative. This data is erroneous and is therefore removed. |
| 5 | Remove rides less than 45 seconds long if their start and end stations are the same. | If the ride is only 45 seconds long and the start and end stations are the same, it is assumed that the bike was checked out then checked back in without a ride occurring |
| 6 | Remove rides made by casuals that are greater than 24 hours.  | Casuals are limited to a day pass. Any rides that exceed 24 hours are no longer considered a single ride and are therefore removed. This consists of 4,303 observations, which have been stored in another data frame for later use in future studies. |
| 7 | Correct missing coordinate data | Cross-reference start and end stations to determine start and end coordinates. These coordinates will be used to determine the distance between start and end points and the minimum possible speed taken during the trip to further clean the data set. |
| 8  | Calculate minimum distance traveled and minimum possible speed, then remove all speed entries greater than 65 kmph | The straight distance between start and end coordinates represents the minimum possible distance traveled for each observation. This is used with ride time to calculate average riding speed over the minimum distance.

This calculation is used to determine if ride time or distance are erroneous or anomalous, since it is not reasonable for a human to sustain speeds greater than 65 kmph and beyond without special equipment.

Speeds greater than 65 kmph could be attributed to faulty GPS data or bikes being loaded into vehicles and driven somewhere.  |
| 9  | Count null start and end entries | This helps us get a grasp of the data’s validity. Too many null entries for start and end stations limits the usefulness of the data. These NA entries are interpreted as Cyclistic users who start or end their trip at a location other than a designated bike station.

1.31 million of 5.65 million remaining entries are missing start or end station data, or both. This leaves 4.34 million entries with valid start and end station locations. Observations with missing station data will be kept since the missing fields do not affect analysis related to ride time. For station-related analysis, our sample size is roughly three quarters the size of the population which is more than sufficient to make strong inferences. |

Next, the data needs to be transformed before analysis:

| Step | Process | Reasoning |
| --- | --- | --- |
| 1  | Calculate ride time in minutes | This makes analyzing ride time more intuitive than if it’s in seconds. |
| 2 | Add columns for month, day of the week, and weekend or weekday | Adding these three columns will allow us to easily analyze variances in rider behavior with respect to month, day of the week, and whether the ride occurred on a weekday or weekend.  |
| 3 | Split member and casual data to new data frames | The ride data set is large. To make it easier to explore the data, new data frames that only included data from members or casuals were created. |

## Analysis and Findings

To gain perspective on how casuals and members use Cyclistic’s services, we’ll start our analysis at a macro level then look at the data in increasing levels of detail. We’ll focus on medians for most of our analysis, since statistical testing (see Appendix 4) has revealed that the data does not follow a normal distribution. Therefore the median, which measures the “center” of the data, can give us a better picture of what’s going on than averages or totals.

****************************************************************Monthly ridership behavior****************************************************************

First, we’ll examine the median number of rides casuals and members made on a monthly basis.
<p align="center">
<img align="center" src="https://github.com/kevin-s-chin/Case_Studies/blob/main/Cyclistic_Case_Study/Cyclistic_Visualizations/median_month.png?raw=true" width="700" height="auto">
</p>

This graph illustrates the how the seasons affect ridership. Casuals and member ridership is highest during the summer months. As the weather gets colder throughout fall and winter, overall ridership falls significantly with an 88% drop-off in casual ridership during January relative to June. Member usage also decreases by 70% in December from its high in August. In the coldest Winter months, there are about three times as many member rides as there are casual rides. Next, we’ll zoom in one step further and look at the data with respect to the days of the week.

**************************************************Weekly ridership behavior**************************************************

<p align="center">
<img align="center" src="https://github.com/kevin-s-chin/Case-Studies/blob/main/Cyclistic_Case_Study/Cyclistic_Visualizations/median_week.png?raw=true" width="700" height="auto">
</p>

Here we can see the ebb and flow of usage by our members throughout the week. Median member usage peaks on Thursday, then diminishes before bottoming out on Sunday and ramping back up again on Monday. Median casual ridership peaks on Saturday and bottoms on Monday and Tuesday. Now we know the majority of our ride volume throughout the year comes from members and it’s particularly concentrated during weekdays, but how long do our users spend on each trip?

<p align="center">
<img align="center" src="https://github.com/kevin-s-chin/Case-Studies/blob/main/Cyclistic_Case_Study/Cyclistic_Visualizations/Median_duration_week.png?raw=true" width="700" height="auto">
</p>

This is an interesting development - the majority of median ridership volume comes from members, but members spend less time per ride than casuals. In other words, members on aggregate go on many short-duration trips while our casual userbase makes fewer longer-duration trips. Median member trip duration also remains consistent throughout the week. This is particularly apparent on Saturday and Sunday, where median casual ride duration increases substantially.  

To get a better sense of how each group uses our bikes, we’ll compare their overall median and average ride times:

<div align="center">
 
| Ride duration | Median | Average |
| --- | --- | --- |
| **Member** | 9 minutes | 12 minutes |
| **Casual** | 12 minutes | 21 minutes |

</div>

Member average and median rides are very close in duration, which supports our thesis that members take many short duration trips. If a large subset of members were going on lengthy trips, average and median would diverge further apart. This is exactly what we see with casuals; average trip duration is almost double that of median trip duration. Casuals make many short-duration trips and also take enough long-duration trips to skew the average up. Next, we’ll look at the intraday behavior of members and casuals.

************Intraday ridership behavior************

<p align="center">
<img align="center" src="https://github.com/kevin-s-chin/Case-Studies/blob/main/Cyclistic_Case_Study/Cyclistic_Visualizations/median_hour_combined.png?raw=true" width="900" height="auto">
</p>

On the left graph, it’s apparent that members are using their bikes to commute on weekdays since median ridership peaks during the morning and afternoon rush hour. Casual ridership steadily creeps up during the day and peaks during afternoon rush hour as well. Weekend median member ridership looks greater than casual ridership, but this difference is not necessarily statistically significant (see Appendix 4). The takeaway here is that on the weekdays, member and casual behavior deviate significantly, while they are similar on the weekends.

<aside>
📌 Recap: on aggregate, members…

- Persist through winter to a greater degree than casuals
- Ride more on the weekdays than casuals
- Have shorter-duration rides than casuals
- Ride most during rush hour on weekdays
- Have similar ride behavior to casuals on weekends
</aside>

Putting these findings together, we can infer that members have integrated our bike-sharing service into their daily routine. Cyclistic fulfills an essential piece of their transportation needs and they use Cyclistic bikes to commute and make short trips around Chicago. 

********************************************************************************************Station usage by members and casuals********************************************************************************************

To get a better sense of how casuals behave, we’ll look at the locations of the top 10 stations used by casuals and members. This was measured by summing the total number of rides that started and ended at each station and ranking them. 

<p align="center">
<img align="center" src="https://github.com/kevin-s-chin/Case-Studies/blob/main/Cyclistic_Case_Study/Cyclistic_Visualizations/station_freq_pie_combined.png?raw=true" width="700" height="auto">
</p>

The proportional ridership among top 10 stations, categorized by location, for casuals and members is shown below:

Among the top 10 stations used by casuals, slightly less than 80% of trips start or finish at a lakefront station and about 15% occur near parks. Conversely, among the top 10 stations used by members, slightly more than 80% of rides start or finish at an urban station. 

This lends further credence to the usage patterns we noticed before - members use Cyclistic bikes to navigate the city while casuals use them for leisure. 

1. Casuals often use our bikes for leisure. As the weather gets colder, casual ridership drops precipitously. Moreover, their median usage on weekdays is roughly one third to one half that of members. Casuals have not integrated our bike service into their daily routines to the extent that members have. 

> ***Key takeaway:** members treat Cyclistic like a necessity for everyday life while casuals rent Cyclistic bikes as a recreational outlet.*
> 

## Recommendations

Cyclistic’s services fulfill different needs in our members and casuals’ lives. Members rely on our bikes for transportation while casuals seem to use them primarily for fun. Therefore, our marketing strategy targeting casuals should focus on convincing them to use Cyclistic bikes for some of their everyday transportation needs in the city and promote the practical bike-sharing use cases.

We propose three recommendations for the marketing strategy:

1. Illustrate the convenience, cost savings, and other benefits from using Cyclistic bikes to commute and run errands around Chicago.
2. Offer a fall promotion to lessen the casual drop-off leading into winter, then offer a spring promotion to bring casuals back quickly after the winter lull.
3. Create advertisements that juxtapose the fun and practical aspects of Cyclistic bikes; teach casuals that Cyclistic bikes can and should be used for both purposes. Have casuals associate Cyclistic bikes with both leisure and utility.

## Appendix 1: R Code and SQL Queries for Data Cleaning

The following R packages were used to perform this case study

```r
library(conflicted)
library(tidyverse)
library(skimr)
library(janitor)
library(here)
library(geosphere)

conflict_prefer("filter", "dplyr")
conflict_prefer("lag", "dplyr")
```

Step 1. Combining the data

R: 

```r
# Navigate to the folder all 12 CSV files are in, then write them to a data frame
directory <- "202208-202307-divvy-tripdata"
bike_data_list <- list.files(directory, pattern = ".csv", full.names = TRUE)

bike_data_original <- do.call(rbind, lapply(bike_data_list, read_csv))
bike_data <- do.call(rbind, lapply(bike_data_list, read_csv))

# Save an original copy of the combined data frame
write_csv(bike_data_original, file = "total-bike-data-original.csv")

# Check that all the data is there
min(bike_data$started_at) # Output: 2022-07-01 00:00:01 UTC
max(bike_data$ended_at) # Output: 2023-06-30 23:59:56 UTC

# The ride data spans from the correct dates, so all the data is present
```

SQL: 

```sql

```

Step 2. Eliminate duplicate entries based on ride id

R: 

```r
# Eliminate duplicates
bike_data_test <- bike_data %>% 
  distinct(ride_id, .keep_all = TRUE)

bike_data_test_length <- bike_data_test %>% 
  summarize(total_count = n())

bike_data_length <- bike_data %>% 
  summarize(total_count = n())

print(bike_data_test_length == bike_data_length) # Output: TRUE

# No duplicate ride_id values since bike_data_test_length = bike_data_length
```

SQL:

```sql

```

Step 3. Truncate the data to the desired time period

R:

```r
min(bike_data$started_at) # Output: 2022-07-01 00:00:01 UTC
max(bike_data$started_at) # Output: 2023-06-30 23:59:56 UTC

# Since the min and max times fall within our desired timeframe,
# no truncating is necessary
```

SQL:

```sql

```

Step 4. Remove observations with negative ride time

R:

```r
# Add a column called ride_time, which shows how long each ride lasted in seconds
# then filter out all rides with ride_time < 0
bike_data <- bike_data %>% 
  mutate(ride_time = difftime(ended_at, started_at)) %>% 
  filter(ride_time >= 0)
```

SQL:

```sql

```

Step 5. Remove rides that are 45 seconds or less where the start and end locations are the same

R:

```r
bike_data <- bike_data %>% 
  filter(!(ride_time <= 45 & start_station_name == end_station_name))
```

SQL:

```sql

```

Step 6. Remove casual rides that are greater than 24 hours

R:

```r
# Write casual rides where ride time is greater than 24 hours (86400 seconds) 
# to a new data frame and remove them from the main data frame
bike_data_casual_long <- bike_data %>% 
  filter(ride_time > 86400 & member_casual == "casual")

bike_data <- bike_data %>% 
  filter(!(ride_time > 86400 & member_casual == "casual"))
```

SQL:

```sql

```

Step 7. Correct missing coordinates in observations

R:

```r
# Correct where lat and long = 0 by cross-referencing station start and stop data with
# complete observations that share the same start or end stations
bike_data <- bike_data %>% 
  mutate(end_lat = ifelse(end_lat == 0 & end_station_name == "Green St & Madison Ave*",
                          41.8818593280382, end_lat)) %>% 
  mutate(end_lng = ifelse(end_lng == 0 & end_station_name == "Green St & Madison Ave*",
                          -87.6492637395858, end_lng)) %>% 
  mutate(end_lat = ifelse(end_lat == 0 & end_station_name == "OH Charging Stx - Test",
                          41.86257, end_lat)) %>% 
  mutate(end_lng = ifelse(end_lng == 0 & end_station_name == "OH Charging Stx - Test",
                          -87.6799351, end_lng))
```

SQL:

```sql

```

Step 8. Calculate distance, speed, and filter for all speeds greater than 65 kmph

R:

```r
# Calculating minimum distance traveled
distance_coords <- function(lat1, lon1, lat2, lon2) {
  start_coords <- cbind(lon1, lat1)
  end_coords <- cbind(lon2, lat2)
  distance_meters <- distVincentySphere(start_coords, end_coords)
  return(distance_meters)
}

# Calculate average speed and remove all observations with speed greater than 65 kmph 
bike_data <- bike_data %>% 
  mutate(distance_m = distance_coords(
    start_lat, start_lng, end_lat, end_lng)) %>% 
  mutate(speed_kmph = (distance_m / ride_time) * (60 * 60) / 1000) %>% 
  filter(!(speed_kmph > 65))
```

SQL:

```sql
-- https://cloud.google.com/bigquery/docs/geospatial-data
```

Step 9. Count null start and end station entries

R:

```r
# Count null start and end locations
empty_count_start <- sum(is.na(bike_data$start_station_name))
empty_count_end <- sum(is.na(bike_data$end_station_name))
empty_count_both <- sum(is.na(bike_data$start_station_name) & 
                        is.na(bike_data$end_station_name))

cat("The number of empty start and end station entries is",
    empty_count_start + empty_count_end - empty_count_both)

# Output: The number of empty start and end station entries is 1313831

# Of the 5.65 million remaining bike_data entries, 1.31 million are missing
# information regarding their start station, end station, or both. 
# This constitutes 24% of all observations
```

SQL:

```sql

```

## Appendix 2: R Code and SQL Queries for Data Transformations

Step 1. Adding a ride time column expressed in minutes

R:

```r
bike_data <- bike_data %>% 
  mutate(ride_time_min = (difftime(ended_at, started_at) / 60))
```

SQL:

```sql

```

Step 2. Add columns for month, day of the week, and whether the day was a weekend or weekday

R:

```r
month_order <- c("Jul", "Aug", "Sep", "Oct", "Nov", "Dec", "Jan", "Feb", "Mar", 
                 "Apr", "May", "Jun")
day_order <- c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", 
               "Saturday")
day_abbr <- c("Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat")

bike_data <- bike_data %>% 
  mutate(month = factor(month(started_at, label = TRUE, abbr = TRUE),
    levels = month_order)) %>% 
  mutate(day_of_week = factor(weekdays(as.Date(bike_data$started_at)),
    levels = day_order, labels = day_abbr)) %>% 
  mutate(weekend_or_weekday = ifelse(day_of_week %in% 
    c("Sat", "Sun"), "Weekend", "Weekday"))
```

SQL:

```sql

```

Step 3. Make two new data frames consisting of rides from members or casuals exclusively

R:

```r
# Split members and casuals
member_data <- subset(bike_data, member_casual == "member")
casual_data <- subset(bike_data, member_casual == "casual")

# Save data frames
write_csv(bike_data, file = "total-bike-data.csv")
write_csv(member_data, file = "total-member-data.csv")
write_csv(casual_data, file = "total-casual-data.csv")
```

SQL:

```sql

```

## Appendix 3: R Code for Analysis

1.    Summarize the bike data by hour of the day

R:

```r
bike_summary_hr <- bike_data %>% 
  group_by(hour_of_day = hour(started_at), member_casual, month, day_of_week) %>% 
  summarize(avg_ride_time = mean(ride_time_min),
            electric_total = sum(rideable_type == "electric_bike"),
            classic_total = sum(rideable_type == "classic_bike"),
            docked_total = sum(rideable_type == "docked_bike"), 
            total_count = n()) %>% 
  mutate(weekend_or_weekday = ifelse(day_of_week %in% 
    c("Sat", "Sun"), "Weekend", "Weekday"))

write_csv(bike_summary_hr, file = "bike_summary_hr.csv")
```

SQL:

```sql

```

1. Break down ride frequency by month

R:

```r
rides_per_month <- bike_data %>% 
  group_by(month, member_casual) %>% 
  summarize(ride_count = n())

write_csv(rides_per_month, file = "rides_per_month.csv")
```

SQL:

```sql

```

1. Find the most frequent start and end stations by user type, then dividing the top 10 into urban, park, or lakefront categories by looking up their locations on Google Maps

R:

```r
# Sorting and ranking stations by the number of rides started and ended for each
freq_member <- subset(bike_data, member_casual == "member") %>% 
  count(start_station_name, name = "member_start") %>% 
  rename(station_name = start_station_name) %>% 
  inner_join(
    member_data %>% 
      count(end_station_name, name = "member_end") %>% 
      rename(station_name = end_station_name),
    by = "station_name"
  ) %>% 
  mutate(member_total = member_start + member_end) %>% 
  arrange(desc(member_total))

freq_casual <- subset(bike_data, member_casual == "casual") %>% 
  count(start_station_name, name = "casual_start") %>% 
  rename(station_name = start_station_name) %>% 
  inner_join(
    subset(bike_data, member_casual == "casual") %>% 
      count(end_station_name, name = "casual_end") %>% 
      rename(station_name = end_station_name),
    by = "station_name"
  ) %>% 
  mutate(casual_total = casual_start + casual_end) %>% 
  arrange(desc(casual_total))

# Filtering out the top 10 stations used by casuals and members based on
# casual_total and member_total, respectively
casual_stations <- freq_casual %>% 
  slice(2:11) %>% 
  select(station_name, casual_total) %>% 
  mutate(percent = casual_total / sum(casual_total)) %>% 
  mutate(station_type = c("lakefront", "lakefront", "lakefront", "park", 
                          "lakefront", "lakefront", "lakefront", "urban", 
                          "lakefront", "park"))

member_stations <- freq_member %>% 
  slice(2:11) %>% 
  select(station_name, member_total) %>% 
  mutate(percent = member_total / sum(member_total)) %>% 
  mutate(station_type = c("urban", "urban", "urban", "urban", "urban", "park", 
                          "park", "urban", "urban", "urban"))
```

SQL:

```sql

```

## Appendix 4: Statistical Analysis

## Works Cited

**************************************[Dataset goes here]**************************************

**[Anne Bastin interview goes here]**

************************************************************************************[Google data analytics project specs here]************************************************************************************
