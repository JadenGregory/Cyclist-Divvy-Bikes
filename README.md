# Case Study : Cyclist Divvy Bikes

### About The Company
In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that
are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and
returned to any other station in the system anytime.

Its pricing plans are :
Single-ride passes (referred to as CASUAL riders)
Full-day passes (referred to as CASUAL riders)
Annual memberships (referred to as MEMBERS)

### Introduction
For this case study, I am assuming the role of a junior data analyst. I am a part of Cyclistic’s marketing analytics team, a team of data analysts who are responsible for collecting, analysing, and reporting data that helps guide the company’s marketing strategy

I will be using Google Data Analytics 6 step or phase process : Ask, Prepare, Process, Analyze, Share, Act.

# 1. Objective (Ask)

### Business Task
My business task is to understand how casual riders and annual members use bikes differently, then use that insight to design a new marketing strategy to convert casual riders into annual members.

### Questions
* How do annual members and casual riders use Cyclistic bikes differently?
* Why would casual riders buy Cyclistic annual memberships?
* How can Cyclistic use digital media to influence casual riders to become members?

# 2. Prepare
For this case we were assigned the question, How do annual members and casual riders use Cyclistic bikes
differently

All the data was downloaded from [Divvy Data](https://divvy-tripdata.s3.amazonaws.com/index.html)

The datasets are sorted by month and loaded using RStudio for the fact that each dataset contains over 100,000 rows and when merged will become too large for excel.

### Loading Packages
````
install.packages('tidyverse')
install.packages('lubridate')
library(tidyverse)
library(lubridate)
````


![Image](https://user-images.githubusercontent.com/123005774/220429318-bd95e6ea-2aac-4495-94ba-fc35b2446dfe.png)


### Loading The Datasets
````
 april <- read.csv('202204-divvy-tripdata.csv')
 
 may <- read.csv('202205-divvy-tripdata.csv')
 
 june <- read.csv('202206-divvy-tripdata.csv')
 
 july <- read.csv('202207-divvy-tripdata.csv')
 
 august <- read.csv('202208-divvy-tripdata.csv')
 
 september <- read.csv('202209-divvy-publictripdata.csv')
 
 october <- read.csv('202210-divvy-tripdata.csv')
 
 november <- read.csv('202211-divvy-tripdata.csv')
 
 december <- read.csv('202212-divvy-tripdata.csv')
 
 january <- read.csv('202201-divvy-tripdata.csv')
 
 february <- read.csv('202202-divvy-tripdata.csv') 

 march <- read.csv('202203-divvy-tripdata.csv')
````

After each files has been checked for consistent and uniform field names and data types, I will merge them to create a data frame that contains data for a whole year

````
divvybikes <- bind_rows(may, june, july, august, september, october, november, december, january, february, march, april)
````

Once the datasets are merged its important to check for any error or faults in the data.

This will be a good time to check if columns were properly named and in their appropriate data types, know the exact number of records that we are dealing with and get initial summary statistics.

````
# List of Column Names
colnames(divvybikes)
````


![Image](https://user-images.githubusercontent.com/123005774/220660419-f976eeca-17bb-4f6b-a199-49252317e6d1.PNG)



````
# Checking Number of Rows
nrow(divvybikes)
````
Number of rows : 5667717

````
# Checking Columns and Data Types ( Numerical, Character , etc)
str(divvybikes)
````




![Image](https://user-images.githubusercontent.com/123005774/220662215-4167c440-7b9f-4261-8213-a1048b214ce8.PNG)

# 3. Process

Now that the datasets are merged, cleaned, and checked over, its time to make the data more useful for analysis. Since the main topic is how the use of bikes differ from casual or members, for this I need to create additional fields to the data.

For this I subtracted the started_at column from ended_at column and added this as a new column named ride_length.

````
divvybikes$ride_length <- difftime(divvybikes$ended_at,divvybikes$started_at)
````
Next I converted the ride_length from character to numeric for calculations later.

````
divvybikes$ride_length <- as.numeric(as.character(divvybikes$ride_length))
````

Afterwards I made new fields for the date, month, day, year, and day_of_week. This will be used to better understand the use of bikes for each cyclist member (Casual, Member) for the 12 months of data.

````
divvybikes$date <- as.Date(divvybikes$started_at)
divvybikes$month <- format(as.Date(divvybikes$date), "%m")
divvybikes$day <- format(as.Date(divvybikes$date), "%d")
divvybikes$year <- format(as.Date(divvybikes$date), "%Y")
divvybikes$day_of_week <- format(as.Date(divvybikes$date), "%A")
````

Now we can get a look at the newly added fields.

````
divvybikes %>%
select(ride_length, date, month, day, year, day_of_week)
````



![Image](https://user-images.githubusercontent.com/123005774/223278327-94178d25-3ef3-4033-9529-f5dd49331fcb.PNG)


After checking the new fields I decided before starting the analysis, to create a new data frame with no negative values. This is done so there is no misleading results after the analysis is conducted.

````
divvybikes2 <- divvybikes [!(divvybikes$ride_length<0),]
````

# 4. Analyze
Now that the data was cleaned successfully, I can start to analyze the data. I can start by using some basic statistic knowledge to calculate the average, median, maximum, and minimun trip durations.

````
divvybikes2 %>%
summarize(mean_usage = mean(ride_length), median_usage = median(ride_length), max_usage = max(ride_length), min_usage = min(ride_length)
````

![Capture 6](https://user-images.githubusercontent.com/123005774/223314745-1cde6735-05a0-4fce-9c5f-7d459677b872.PNG)


Next I will show the difference between casual riders and members.

````
![Capture 7](https://user-images.githubusercontent.com/123005774/223314863-08f53ea4-6a36-4db9-945d-e2965f71c3f8.PNG)

````
Next to go into more details by days of week. I First need to display the days of the week in order by inputing the following code.

````
divvybikes2$day_of_week <- ordered(divvybikes2$day_of_week, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))
````

After that I will get check on the differences in both riders for their average trip duration by days of the week.

````
divvybikes2 %>% 
+     group_by(member_casual, day_of_week) %>% 
+     summarize(mean_usage = mean(ride_length)/60,
+               median_usage = median(ride_length)/60,
+               max_usage = max(ride_length)/60,
+               min_usage = min(ride_length)/60)
````

![Capture 8](https://user-images.githubusercontent.com/123005774/223317223-2f55d0bc-9a7d-4a50-ae4b-d3e5565208b1.PNG)

Now I can also count the number of rides taken by each rider (Casual, Member) and group them into days of the week.

````
divvybikes2 %>% 
+     mutate(weekday = wday(started_at)) %>%  
+     group_by(member_casual, weekday) %>%  
+     summarise(number_of_rides = n()) %>% 
+     arrange(member_casual, weekday)
````
![Capture 9](https://user-images.githubusercontent.com/123005774/223318342-aacbb823-b256-41fd-891d-7c2df4492ee3.PNG)

We can also look at the number of rides per day with its corresponding average trip duration.

````
divvybikes2 %>% 
+     mutate(weekday = wday(started_at, label = TRUE)) %>%  
+     group_by(member_casual, weekday) %>%  
+     summarise(number_of_rides = n(),average_duration = mean(ride_length)/60) %>%
+     arrange(member_casual, weekday)
````
![Capture 10](https://user-images.githubusercontent.com/123005774/226686454-3323e65e-c627-4b2a-9e9c-244ce62f5398.PNG)

After checking the week I would like to see the number of rides per month for each member.

````
bikeshare2 %>% 
  group_by(member_casual, month) %>%  
  summarise(number_of_rides = n()) %>%
  arrange(member_casual, month)
````
![Capture 11](https://user-images.githubusercontent.com/123005774/226695864-fe78b432-8363-4c86-8b4d-4622b690b09c.PNG)

After this is finally the average trip duration for each month.

````
bikeshare2 %>% 
  group_by(member_casual, month) %>%  
  summarise(average_duration = mean(ride_length)/60) %>%
  arrange(member_casual, month)
````

![Capture 12](https://user-images.githubusercontent.com/123005774/226696459-b7ead7df-0d1f-49af-bcaa-9ea58ee8f5e8.PNG)

With the calculations above, we can tell there is a significant difference in casual riders and members.

# 5. Share

Now we are ready to create visualizations to get a clearer insight about the case study.

First we will get a look at which days rider used the cyclist service the most. I also noticed that the casual riders were less consistent and the members had a more consistent pattern.



