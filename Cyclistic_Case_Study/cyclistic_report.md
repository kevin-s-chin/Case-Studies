# Cyclistic Report: Member and Casual User Behavior

By Kevin Chin

Published: 9 October 2023

Last edited: 9 October 2023

## Executive Summary

#### Overview

Cyclistic is a fictional Chicago-based bike-sharing company whose user base is split into two categories: members who pay an annual subscription and casuals who can buy a single-ride or full-day pass.

#### Problem

Thus far, Cyclistic has focussed on building its user base. Recent financial analysis has revealed that our members are much more profitable than our casual users, so we intend to pivot our marketing strategy towards converting casuals into members. This study was conducted using bike trip data and its insights will inform our marketing team about how casuals and members use our services.

#### Findings

Members use Cyclistic bikes to fulfill their everyday transportation needs; casuals often use them for leisure. 

Trends in member usage:

- Have a lower seasonal attrition rate than casuals.
- Use Cyclistic bikes as part of their commute to and from work.
- Make more frequent, shorter trips than casuals.
- Ride more often than casuals on the weekdays.
- Have similar ride behavior to casuals throughout the day on weekends.
- Often start and end their rides at urban stations

Trends in casual usage:

- Ride recreationally
- Don’t use Cyclistic to commute
- Ride after work on weekdays
- Often start and stop their rides at lakefront stations

#### Recommendations

Based on our findings, we recommend that the new marketing strategy focuses on:

- Demonstrating the convenience of commuting and making short (less than 10 min.) trips around the city using Cyclistic bikes.
- Offer a seasonal promotion in the fall to drive casual engagement and in spring to bring casuals back after the winter months.
- Combine fun and practical use cases in our marketing materials to show casuals that Cyclistic’s service is multi-purpose.

## Table of Contents

1. Introduction
2. Data Collection
3. Data Processing
4. Analysis and Findings
5. Summary Conclusion
6. Recommendations
7. Appendix 1: Statistical Analysis
8. Appendix 2: R Code and SQL Queries for Data Cleaning
9. Appendix 3: R Code and SQL Queries for Data Transformations
10. Appendix 4: R Code for Data Analysis

## Introduction

At Cyclistic, a fictional Chicago-based bike-sharing company, we’ve built a large user base and have determined that our members are much more profitable than our casual riders. Our customers with annual memberships can use the bikes whenever they like while casuals can buy a single-ride or full-day pass. The purpose of this study is to leverage the data collected from millions of Cyclistic’s bike trips to gain insight into how casuals and members use our bikes. We will then use these insights to provide our marketing team with recommendations to develop a marketing strategy that converts casuals into members. 

## Data Collection

The ridership data used in this analysis was sourced from Motivate International Inc.’s [Divvy bike-sharing database](https://divvy-tripdata.s3.amazonaws.com/index.html), which is free to use under this [license](https://divvybikes.com/data-license-agreement).

Monthly ride data from July 2022 to June 2023 was gathered in CSV format. This constitutes a year of the most recent ridership data available from Divvy Bikes. By analyzing a full year of rides, we can gain insight into how casuals and members use our bikes throughout the year.

The dataset contains the following categories for each observation: a unique ride id, the type of bike used, start and end timestamps, start and end bike stations, start and end coordinates, and whether the rider was a member or casual. 

## Data Processing

First, we need to clean the data. This process is described in the table below.
<div align="center">
  
| Step | Process | Reasoning |
| --- | --- | --- |
| 1 | Consolidate 12 months of ride data into one data frame and check that it’s all present | To ensure the data is comprehensively cleaned and to avoid repeating work, we’ll combine the data together to clean all 12 months simultaneously |
| 2 | Eliminate duplicate entries  | Duplicate entries were eliminated by searching for any repeated ride id’s. In this case, none were found so there are no duplicates  |
| 3  | Filter the data so that it only contains rides that started between July 2022 and June 2023 | Our desired sample is exactly one year long, according to the ride start and end times. |
| 4 | Remove rides with negative ride duration | Ride time cannot be negative. This data is erroneous and was removed. |
| 5 | Remove rides made by casuals that are greater than 24 hours.  | Casuals are limited to a day pass. Any rides that exceed 24 hours are no longer considered a single ride and were removed.  |
| 6 | Correct missing coordinate data | Cross-reference start and end stations to determine start and end coordinates.  |
| 7 | Calculate minimum distance traveled and estimated average speed, then remove speed entries that are NA or greater than 65 kmph  | The straight distance between start and end coordinates represents the minimum possible distance traveled for each observation. This is used with ride time to calculate a rough estimate of average speed.
</div>

Sustained speeds beyond 65 kmph are not realistic. These entries could be attributed to faulty GPS data or bikes being loaded into vehicles and driven somewhere. 

Observations with “NA” speed entries are missing several fields, so their integrity is suspect. |
| 8 | Remove rides that started and stopped in the same location and had a duration of less than 30 seconds | If the ride is only 30 seconds long and the distance traveled is 0, it is assumed that the bike was checked out and then checked back in without a ride occurring. |
| 9 | Count null start and end station entries | This helps us get a grasp of the data’s validity. Too many null entries for start and end stations limit the usefulness of the data. These NA entries are interpreted as rides that started or ended at a location other than a designated bike station.

Of the 5.7 million remaining entries after cleaning, about three-quarters have valid start and end station entries. Observations with missing station data will be kept since their time stamp data is still valuable. The remaining sample is more than sufficient to make strong inferences about station usage. |

Next, the data needs to be transformed before analysis:
<div align="center">
  
| Step | Process | Reasoning |
| --- | --- | --- |
| 1 | Add columns for month, day of the week, and weekday or weekend | Adding these three columns will allow us to analyze variances in rider behavior with respect to month, the day of the week, and whether the ride occurred on a weekday or weekend.  |
| 2 | Split member and casual data into new data frames | For convenience during exploratory data analysis, new data frames that only included data from members or casuals were created. |

</div>
## Analysis and Findings

To gain perspective on how casuals and members use Cyclistic’s services, we’ll start our analysis at a macro level and then look at the data in increasing levels of detail. Medians will be compared for most of the analysis because statistical tests (see Appendix 1) revealed that the data does not follow a normal distribution. Therefore, the median provides the least biased estimate with which to compare the data.

#### Monthly ridership behavior

First, we’ll examine the median number of rides casuals and members made monthly. 

<p align="center">
<img src="https://github.com/kevin-s-chin/Case_Studies/blob/main/Cyclistic_Case_Study/Cyclistic_Visualizations/median_month.png?raw=true" width="700">
</p>

This graph illustrates how the seasons affect ridership. Casuals and member ridership are highest during the pleasant summer months, but ridership falls significantly as the weather gets colder. There’s an 88% drop in casual ridership from June to January. Member usage decreased by 70% from its high in August to its low in December. In the coldest Winter months, there are about three times as many member rides as there are casual rides.

Next, we’ll zoom in further and look at the frequency and duration of rides with respect to the day of the week.

#### Weekly ridership behavior

<p align="center">
<img src="https://github.com/kevin-s-chin/Case_Studies/blob/main/Cyclistic_Case_Study/Cyclistic_Visualizations/median_duration_week.png?raw=true" width="700">
</p>

Here we can see the ebb and flow of bike usage throughout the week. Median member ridership peaks on Thursday, then decreases until Monday when it ramps up again. Median casual ridership peaks on Saturday and bottoms on Monday and Tuesday. The majority of ride volume throughout the year comes from members and it’s particularly concentrated during weekdays, but how long do members and casuals spend on each trip? The difference between median rides per day for members and casuals on Saturday and Sunday was not statistically significant. 

<p align="center">
<img src="https://github.com/kevin-s-chin/Case_Studies/blob/main/Cyclistic_Case_Study/Cyclistic_Visualizations/median_week.png?raw=true" width="700">
</p>

Median member ride duration is less than median casual ride duration every day of the week and, from the previous graph, most median ridership volume comes from members. In other words, members tend to make many short-duration trips. Median member ride duration also remains consistent throughout the week whereas casual ride duration increases on the weekends.

To get a better sense of how each group uses our bikes, let’s compare their overall median and average ride times:

<div align="center">

| Ride duration | Median | Average |
| --- | --- | --- |
| Member | 9 minutes | 12 minutes |
| Casual | 12 minutes | 21 minutes |

</div>  

Member average and median ride duration are similar, which supports our earlier observation that members take many short-duration trips. Casuals, on the other hand, are split; the average trip duration is almost double the median trip duration. The gap between the median and average duration implies casuals make many short-duration trips, which drags the median down while making enough long-duration trips to skew the average up.

Next, we’ll examine the intraday ride frequency of members and casuals.

#### Intraday ridership behavior

<p align="center">
<img src="https://github.com/kevin-s-chin/Case_Studies/blob/main/Cyclistic_Case_Study/Cyclistic_Visualizations/median_hour_combined.png?raw=true" width="900">
</p>

Observe the spikes in median member ridership at 8:00 and 17:00 on the left-hand graph. Those spikes illustrate that members are using their bikes to commute during rush hour. Casual ridership steadily creeps up during the day and peaks during afternoon rush hour as well. 

The casual peak at 17:00 could be them commuting home; however, if they didn’t bike to work then they likely drove. Therefore, this peak may be casuals biking for fun or exercise after work before driving home in the evening.

Weekend median member ridership looks greater than casual ridership, but this difference is only statistically significant for a few hours in the morning (see Appendix 1). 

It can be inferred from this that members have integrated our bike-sharing service into their daily routine to a greater extent than casuals. Cyclistic fulfills an essential piece of its members’ everyday transportation needs.

#### Station usage by members and casuals

To get a better sense of how casuals behave, we collated the locations of the top 10 stations used by casuals and members. The top-10 lists were determined by ranking the stations using the sum of rides that started and ended at each one.

<p align="center">
<img src="https://github.com/kevin-s-chin/Case_Studies/blob/main/Cyclistic_Case_Study/Cyclistic_Visualizations/station_freq_pie_combined.png?raw=true" width="700">
</p>

Among the top 10 stations used by casuals, seven are on the lakefront and two are next to parks. Only one is situated in an urban area. Conversely, eight top member stations are urban. This implies casuals often use our bikes for leisure and have not integrated our bike service into their daily  routine to the extent that members have. Their high winter attrition rate makes more sense in this context.
  
## Summary Conclusion
This data analysis study has identified the following trends among members and casuals:
<div align="center">
  
| Members | Casuals |
| --- | --- |
| Persist through winter to a greater degree than casuals | Ride in a recreational capacity |
| Have a larger volume of short-duration rides than casuals | Don’t often bike to work in the morning |
| Ride more on the weekdays than casuals | Frequently rent a bike after work, possibly to relax or exercise |
| Ride most during rush hour on weekdays | Majority of most popular stations are on the lakefront |
| Have a similar intraday ridership profile to casuals on weekends |  |
| Majority of most popular stations are urban |  |

</div>

***Key takeaway:** **Members use Cyclistic bikes to fulfill their everyday transportation needs while casuals tend to use them recreationally.***  

## Recommendations

Cyclistic fulfills different needs for our customers. Members rely on Cyclistic bikes for transportation while many casuals use them for fun. Our marketing strategy should focus on convincing casuals to use our bikes for some of their everyday transportation needs in the city.

We propose three recommendations:

1. Illustrate the convenience and lifestyle benefits of using Cyclistic bikes to commute and get around in Chicago. For example, with Cyclistic you don’t have to worry about finding parking.
2. Offer a fall promotion to slow the casual drop-off before winter, then offer a spring promotion to bring casuals back quickly after the winter lull.
3. Create advertisements that juxtapose the fun and utilitarian aspects of Cyclistic bikes. Have casuals associate Cyclistic bikes with both leisure and practicality. Consider focusing on the casuals who use our bikes immediately after work. 

## Appendix 1: Statistical Analysis

The data was statistically compared from five perspectives:
<div align="center">
  
|  | Description | Conclusion |
| --- | --- | --- |
| 1.  | Compare daily ridership for each month by user type | The difference in median daily ridership for members and casuals was statistically significant for all months except July.  |
| 2.  | Compare ridership by day of the week and user type | The difference in median ridership for members and casuals was statistically significant on weekdays, but we cannot conclude that they differ on weekends. |
| 3.  | Compare ride duration for each day of the week by user type | The difference in median ride duration for members and casuals was statistically significant for every day of the week. |
| 4. | Compare ridership for each hour on weekdays by user type | The difference in median ridership for members and casuals was statistically significant from 5 a.m. to 12 a.m., but we cannot conclude that they differ outside of those hours. |
| 5.  | Compare ridership for each hour on weekends by user type | The difference in median ridership for members and casuals was statistically significant between 6 a.m. and 10 a.m., but we cannot conclude that they differ outside of this four-hour period. |
</div>

Each test followed the same procedure:

Step 1: Determine if the data from the perspective being tested is normally distributed using the Shapiro-Wilk test.

Step 2: Determine if the difference between ridership metrics for members and casuals is statistically significant. 

The tests were performed at a 95% confidence level. All five perspectives compared failed the test for normality. This limits the tests we can perform in Step 2 since many statistical tests assume normality. In Step 2, a Wilcoxon Rank Sum multiple comparisons test was performed. This test compares medians and does not require normally distributed data.

Sample R code for daily ridership by month:

```r
# Data frame for daily ridership grouped by month
bike_month <- bike_data %>% 
  mutate(day = day(date)) %>% 
  group_by(month, day, member_casual) %>% 
  summarize(total_count = n()) %>% 
  pivot_wider(names_from = member_casual, values_from = total_count)

# Testing the data frame for normality
shapiro_results_month <- bike_month %>% 
  group_by(month) %>% 
  summarize(
    member_p = shapiro.test(member)$p.value,
    casual_p = shapiro.test(casual)$p.value
  )

View(shapiro_results_month)

# Performing the Wilcoxon Rank Sum multiple comparisons test
wilcoxon_results_month <- bike_month %>% 
  group_by(month) %>% 
  summarize(
    wilcoxon_stat = wilcox.test(member, casual, exact = FALSE)$statistic,
    wilcoxon_p_value = wilcox.test(member, casual, exact = FALSE)$p.value
  ) %>% 
  mutate(holm_p_value = p.adjust(wilcoxon_p_value, method = "holm"))

View(wilcoxon_results_month)
```

## Appendix 2: R Code and SQL Queries for Data Cleaning

The following R packages were used to perform this case study

```r
library(conflicted)
library(tidyverse)
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
bike_data <- bike_data_original

# Save an original copy of the combined data frame
write_csv(bike_data_original, file = "total-bike-data-original.csv")
```

SQL: 

```sql
-- Query 1: "Stacking" all the tables together (partial code)
CREATE TABLE `cyclistic_data.bike_data` AS
SELECT * FROM `cyclistic_data.cyclist_data_202207`
UNION ALL
SELECT * FROM `cyclistic_data.cyclist_data_202208`
...
UNION ALL
SELECT * FROM `cyclistic_data.cyclist_data_202306`

-- Query 2 and 3: Check that the data is all present
SELECT
  started_at
FROM
  `cyclistic_data.bike_data`
ORDER BY
  started_at -- Earliest is 2022-07-01 00:00:01 UTC

SELECT
  started_at
FROM
  `cyclistic_data.bike_data`
ORDER BY
  started_at DESC -- Latest is 2023-06-30 23:59:56 UTC

-- Query 4: Back-up an original copy of the combined data before cleaning starts
CREATE TABLE `cyclistic_data.bike_data_original` AS
SELECT *
FROM `cyclistic_data.bike_data`
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
-- Check for duplicate ride_id's
SELECT
  COUNT(ride_id) AS count_id,
  COUNT(DISTINCT ride_id) AS count_unique_id
FROM `cyclistic_data.bike_data`

-- count_id = 5779444 = count_unique_id => no duplicates
```

Step 3. Filter the data to the desired time period

R:

```r
min(bike_data$started_at) # Output: 2022-07-01 00:00:01 UTC
max(bike_data$started_at) # Output: 2023-06-30 23:59:56 UTC

# Since the min and max times fall within our desired timeframe,
# we do not need to trim the data
```

SQL:

```sql
SELECT
  started_at
FROM
  `cyclistic_data.bike_data`
ORDER BY
  started_at -- Earliest is 2022-07-01 00:00:01 UTC

SELECT
  started_at
FROM
  `cyclistic_data.bike_data`
ORDER BY
  started_at DESC -- Latest is 2023-06-30 23:59:56 UTC

-- Therefore all data is within the desired timeframe
```

Step 4. Remove observations with a negative ride time

R:

```r
# Add a column called ride_time, which shows how long each ride lasted in seconds
# then filter out all rides with ride_time <= 0 and count the number of rows removed
bike_data <- bike_data %>% 
  mutate(ride_time = difftime(ended_at, started_at)) %>%
  mutate(ride_time_min = as.numeric(ride_time) / 60) %>% 
  filter(ride_time > 0)

# Removed 574 rows
```

SQL:

```sql
-- Add the column ride_time = started_at - ended_at, which is ride time in seconds
-- Then filter for ride_time > 0

-- Query 1
ALTER TABLE `cyclistic_data.bike_data`
ADD COLUMN ride_time INT,
ADD COLUMN ride_time_min FLOAT64

-- Query 2
UPDATE `cyclistic_data.bike_data`
SET 
  ride_time = TIMESTAMP_DIFF(ended_at, started_at, SECOND),
  ride_time_min = TIMESTAMP_DIFF(ended_at, started_at, SECOND) / 60
WHERE 
  ride_time IS NULL
  AND ride_time_min IS NULL

-- Query 3
DELETE FROM `cyclistic_data.bike_data`
WHERE ride_time <= 0

-- Removed 574 rows
```

Step 5. Remove casual rides that are greater than 24 hours

R:

```r
# Remove casual rides where ride time is greater than 24 hours (86400 seconds) 
bike_data <- bike_data %>% 
  filter(!(ride_time > 86400 & member_casual == "casual"))

# Removed 4300 rows
```

SQL:

```sql
DELETE FROM `cyclistic_data.bike_data`
WHERE 
  ride_time > 86400 
  AND member_casual = "casual"

-- Removed 4300 rows
```

Step 6. Correct missing coordinates in observations

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

# Changed 10 rows
```

SQL:

```sql
-- 1. Check for any errors in latitude or longitude errors
SELECT
  ride_id,
  start_station_name,
  end_station_name
FROM `cyclistic_data.bike_data`
ORDER BY start_lat
-- Repeat with start_lng, end_lat, end_lng and look at ascending order as well

-- 2. Update erroneous coordinates with correct values
-- Query 2.1
UPDATE `cyclistic_data.bike_data`
SET end_lat =
  CASE 
    WHEN end_lat = 0 AND end_station_name = 'Green St & Madison Ave*'
      THEN 41.8818593280382
    WHEN end_lat = 0 AND end_station_name = 'OH Charging Stx - Test'
      THEN 41.86257
  END
WHERE end_lat = 0

-- Query 2.2
UPDATE `cyclistic_data.bike_data`
SET end_lng =
  CASE
    WHEN end_lng = 0 AND end_station_name = "Green St & Madison Ave*"
      THEN -87.6492637395858
    WHEN end_lng = 0 AND end_station_name = "OH Charging Stx - Test"
      THEN -87.6799351
  END
WHERE end_lng = 0

# Changed 10 rows
```

Step 7. Calculate distance and speed. Filter out speeds greater than or equal to 65 kmph and NA speed values.

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
# This filter also removes all speed_kmph values that are NA
bike_data <- bike_data %>% 
  mutate(distance_m = distance_coords(
    start_lat, start_lng, end_lat, end_lng)) %>% 
  mutate(speed_kmph = (distance_m / ride_time) * (60 * 60) / 1000) %>% 
  filter(speed_kmph < 65)

# Removed 13061 rows
```

SQL:

```sql
-- Query 1: Add a distance (meters) column 
ALTER TABLE `cyclistic_data.bike_data`
ADD COLUMN distance_m FLOAT64,
ADD COLUMN speed_kmph FLOAT64

-- Query 2: Calculate distance and insert it into the distance_m column
UPDATE `cyclistic_data.bike_data`
SET 
  distance_m = ST_DISTANCE(
    ST_GEOGPOINT(start_lng, start_lat),
    ST_GEOGPOINT(end_lng, end_lat)
  ),
  speed_kmph = (ST_DISTANCE(
    ST_GEOGPOINT(start_lng, start_lat),
    ST_GEOGPOINT(end_lng, end_lat)
  ) / ride_time_min) * (60 / 1000)
WHERE 
  distance_m IS NULL
  AND speed_kmph IS NULL

-- Query 3: Remove any rides with speeds of 65 kmph or more
DELETE FROM `cyclistic_data.bike_data`
WHERE 
  speed_kmph >= 65

-- Query 4: Remove NULL distance values since these entries are missing a significant
-- amount of fields and their integrity is suspect
DELETE FROM `cyclistic_data.bike_data`
WHERE
  distance_m IS NULL

-- Removed 13055 rows. Query 3 removed 11453 and Query 4 removed 1602
```

Step 8. Filter out rides that started and stopped in the same location and had a duration of less than 30 seconds

R:

```r
bike_data <- bike_data %>% 
  filter(distance_m > 0 & ride_time >= 30))

# Removed 53319 rows
```

SQL:

```sql
DELETE FROM `cyclistic_data.bike_data`
WHERE 
  distance_m = 0 AND ride_time < 30

-- Removed 53319 rows
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

# Output: The number of empty start and end station entries is 1329213

# Of the 5.65 million remaining bike_data entries, 1.31 million are missing
# information regarding their start station, end station, or both. 
# This constitutes 24% of all observations
```

SQL:

```sql
SELECT COUNT(ride_id)
FROM `cyclistic_data.bike_data`
WHERE
  end_station_name IS NULL OR start_station_name IS NULL

-- Result: 1329219. There is a final difference of six entries between R and SQL.
-- This discrepancy occurred in Step 7 and is likely due to the way R and BigQuery
-- handle NULL and NA entries.
```

## Appendix 3: R Code and SQL Queries for Data Transformations

Step 1. Add columns for month, day of the week, and whether the day was a weekend or weekday

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
-- Query 1: Add date columns
ALTER TABLE `cyclistic_data.bike_data`
ADD COLUMN month STRING,
ADD COLUMN day_of_week STRING,
ADD COLUMN weekday_weekend STRING

-- Query 2: Populate the date columns
UPDATE `cyclistic_data.bike_data`
SET
  month = FORMAT_DATE('%b', DATE(started_at)),
  day_of_week = FORMAT_DATE('%a', DATE(started_at)),
  weekday_weekend = CASE
    WHEN EXTRACT(DAYOFWEEK FROM started_at) IN (2,3,4,5,6) 
      THEN 'Weekday'
    ELSE 'Weekend'
  END
WHERE started_at IS NOT NULL
```

Step 2. Make two new data frames consisting of rides from members or casuals exclusively

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
-- Query 1: Create member table
CREATE TABLE `cyclistic_data.member_data` AS
SELECT *
FROM `cyclistic_data.bike_data`
WHERE 
  member_casual = 'member'

-- Query 2: Create casual table
CREATE TABLE `cyclistic_data.casual_data` AS
SELECT *
FROM `cyclistic_data.bike_data`
WHERE 
  member_casual = 'casual'
```

## Appendix 4: R Code and SQL Queries for Analysis

Step 1: Summarize the bike data by hour of the day for exploratory data analysis

R:

```r
bike_summary_hr <- bike_data %>% 
  group_by(hour_of_day = hour(started_at), member_casual, month, day_of_week) %>% 
  summarize(
    avg_ride_time = mean(ride_time_min),
    electric_total = sum(rideable_type == "electric_bike"),
    classic_total = sum(rideable_type == "classic_bike"),
    docked_total = sum(rideable_type == "docked_bike"), 
    total_count = n()
  )

write_csv(bike_summary_hr, file = "bike_summary_hr.csv")
```

SQL:

```sql
SELECT
  EXTRACT(HOUR FROM started_at) hour_of_day,
  member_casual,
  month,
  day_of_week,
  AVG(ride_time_min) avg_ride_time,
  COUNT(*) AS total_count
FROM 
  `cyclistic_data.bike_data`
GROUP BY
  EXTRACT(HOUR FROM started_at),
  member_casual,
  month,
  day_of_week
```

Step 2: Group ride frequency data by month

R:

```r
rides_per_month <- bike_data %>% 
  group_by(month, member_casual) %>% 
  summarize(ride_count = n())

write_csv(rides_per_month, file = "rides_per_month.csv")
```

SQL:

```sql
SELECT
  month,
  member_casual,
  count(*) ride_count
FROM `cyclistic_data.bike_data`
GROUP BY
  month,
  member_casual
```

Step 3: Find the most frequent start and end stations by user type, then divide the top 10 into urban, park, or lakefront categories by looking up their locations on Google Maps.

R:

```r
# Sorting and ranking stations by the number of rides started and ended for each.
# Note: This was performed with casuals by swapping "member" with "casual"
freq_member <- member_data %>% 
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

# Filtering out the top 10 stations used by casuals and members based on
# casual_total and member_total, respectively
member_stations <- freq_member %>% 
  slice(2:11) %>% 
  select(station_name, member_total) %>% 
  mutate(percent = member_total / sum(member_total)) %>% 
  mutate(station_location = c("urban", "urban", "urban", "urban", "urban", "park", 
                          "park", "urban", "urban", "urban"))
```

SQL:

```sql
-- This code can be executed for casuals by swapping "member" with "casual"
CREATE TABLE `cyclistic_data.freq_member` AS

WITH start_count AS (
  SELECT
    start_station_name station_name,
    COUNT(*) member_start
  FROM `cyclistic_data.member_data`
  GROUP BY
    station_name
),

end_count AS (
  SELECT
    end_station_name station_name,
    COUNT(*) member_end
  FROM `cyclistic_data.member_data`
  GROUP BY
    station_name
)

SELECT
  start_count.station_name,
  start_count.member_start,
  end_count.member_end,
  (start_count.member_start + end_count.member_end) member_total
FROM start_count 
INNER JOIN end_count ON
  start_count.station_name = end_count.station_name
ORDER BY
  (start_count.member_start + end_count.member_end) DESC
LIMIT 10
```
