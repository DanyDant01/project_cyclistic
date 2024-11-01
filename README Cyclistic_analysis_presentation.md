
<h1 style="font-size: 1.5em; font-weight: bold; text-align: center;">Cyclistic - Analysis Presentation</h1>

<div style="text-align: center;">
  <img src="./images/cyclistic_project_logo_v2.jpg" alt="" style="max-width:60%; height:auto;" />
</div>

<br>

# 1. Introduction

Cyclistic is a fictional bike-sharing company Cyclistic, operating in Chicago.
In the scenario, US. Director of Marketing believes that key to company success lies in maximizing the number of annual memberships. Data Analysis team and myself are tasked to determine how casual riders and annual members use Cyclistic bikes differently. The ultimate goal is to use these insights to create a marketing strategy that converts casual riders into annual members.

- Company as a fleet of slightly under 10,000 bicycles that are geotracked and operate within a network of stations across Chicago. The bikes can be unlocked from one station and returned to any other station.
- Cylistic Pricing Plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase <u>single-ride</u> or <u>full-day passes</u> are referred to as **casual** riders. Customers who purchase <u>annual memberships</u> are Cyclistic **members**.
- Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders.

# 2. Data Preparation and Validation process

Data for the project is obtained from [divvy-tripdata repository](https://divvy-tripdata.s3.amazonaws.com/index.html) and is based on real-company Lyft Bikes and Scooters and DivvyBikes data. Data was assessed and following is our conclusion in accordinace with ROCCC framework:

- Reliable ✅
- Original ✅
- Current ✅
- Credible ✅
- Comprehensive ⚠️

It is a 1st party data, collected by Cyclistic themselves. It covers the priod of 01.08.2023 through 01.07.2024. Thefore, it is concluded to be reliable, original, current and credible. The only criteria where we couldn't attest data to the highest standard is Comprehensiveness. That is because data contains limitations and had some errors. While data was cleaned of errors and outliers, and processed before analyzing to enable valid insights, limitations cannot be overcome.

# 3. Data Limitations

It is important to keep in mind limitations of the data to understand applicability limits of our findings. There are 4 main limitations.

1. Data is anonymized for privacy reasons. It does not carry any identification info (account numbers) about the users. Therefore, we are not able to tell how many unique individual members or casual users have made the number of trips we are analyzing. It is also due to the same reason we do not know valuable information such as location of residence, age, gender, which, if present, would allow for a much more insightful analysis.

2. In the scenario, trips are claimed to be geo-tracked. However, we only have available coordinates of start & end stations, which does not enable us to get an adequate idea about travelled distance, directions, speed, etc. Therefore, route analysis is not possible.

3. Information about the bike type is not possible to be matched against fleet count or bike availability at the station at the time of trip booking. Therefore, we cannot reliably conclude whether difference between members and casual riders in terms of the bike choice (which, jumping ahead, is very little) is attributed to their preferences or simply a factor of availability. Additionally, it is not pointed out in the scenario, whether price for the ride is different depending ont the type of the bike used. It would be reasonable to assume so, but, strictly speaking, it is unknown.

4. Costs of the business are unkown. It is simply given within the scenario that financial analysts concluded that members are more profitable. While it doesn't prohibit us from analyzing the data, it does limit our possibility to make insightful recommendations, because we do not know if any of our recommendations would undermine the underlying logic of member profitability. This point is separately addressed in Conclusion section.

# 4. Analysis and Findings

## 4.1. An Overview

Before delving into the distinctions between Members and Casual riders, let's establish a baseline understanding of the overall trip data.

<div style="text-align: center;">
  <img src="./images/overall_table.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

As depicted in the following chart, the distribution of trip counts across the week presents a clear pattern.

<div style="text-align: center;">
  <img src="./images/Trips_by_daytype_dashboard.png" alt="" style="max-width:80%; height:auto;" />
</div>

<br>

However, it's important to note that this raw count doesn't fully capture the picture. When normalized to trips per day, both weekdays and weekends exhibit similar levels of activity.

<div style="text-align: center;">
  <img src=".\images\trips_by_daytime_averaged_bars.png" alt="" style="max-width:80%; height:auto;" />
</div>

<br>

An examination of trip durations reveals that most trips are relatively short. The chart below provides a detailed breakdown.

<br>

<div style="text-align: center;">
  <img src="./images/Duration Dashboard_resized.png" alt="" style="max-width:80%; height:auto;" />
</div>

<br>

Upper chart presents number of trips within specific duration ranges in absolute numbers, while the lower one reflects the same data in percentages. The chosen duration ranges emphasize the dominance of short trips. Around 50% of trips are under 10 minutes, 80% under 20 minutes, and 90% under 30 minutes.

As expected, summer sees the highest demand for bikes, while winter experiences the lowest. This aligns with the intuitive notion that bike usage is heavily influenced by weather conditions.

<br>

<div style="text-align: center;">
  <img src="./images/Trips_per_month _%25.png" alt="" style="max-width:80%; height:auto;" />
</div>

<br>

<div style="text-align: center;">
  <img src="./images/Trips_by_Season_dashboard.png" alt="" style="max-width:80%; height:auto;" />
</div>

<br>

To visualize the geographical distribution of trips, please refer to the following map. Circles represent individual stations, and circle thickness indicates station popularity.

<br>

<div style="text-align: center;">
  <img src="./images/Station Map General.png" alt="" style="max-width:80%; height:auto;" />
</div>

<br>

The subsequent chart offers insights into the hourly distribution of trips throughout the day.

<br>

<div style="text-align: center;">
  <img src="./images/Start_Hour_per_day.png" alt="" style="max-width:80%; height:auto;" />
</div>

<br>

As you can observe, weekdays exhibit distinct peaks around 8 AM and 5-6 PM, corresponding to morning and evening commutes. These peaks gradually diminish on Fridays and are altogether absent on weekends.

## 4.2. Member & Casual user comparison

In light of the above data, let's explore how these trends manifest differently when considering user type. As will be seen, Members and Casual users exhibit distinct patterns across various dimensions.

### 4.2.1. Trip Count

Firstly, it's evident that Members undertake a significantly larger number of trips compared to Casual riders.

<div style="text-align: center;">
  <img src="./images/Trips_per_usertype_wide.png" alt="" style="max-width:80%; height:auto;" />
</div>

<br>

Members account for nearly 2/3 of all trips.

However, the distribution of trips across weekdays and weekends varies considerably between the two user types. Members exhibit a strong preference for weekday riding, while Casual users tend to ride more frequently on weekends. This disparity is visualized in the following chart.

<div style="text-align: center;">
  <img src="./images/Daytype_by_usertype_dashboard.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

The subsequent chart further accentuates this difference. It illustrates the average number of trips per day for both Casual and Member users, categorized by weekday and weekend.

<br>

<div style="text-align: center;">
  <img src="./images/TripCount_daytype_usertype_weightedavg.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

When viewed in this manner, Casual users, on average, undertake notably more trips per day on weekends compared to weekdays. In contrast, Members exhibit the opposite trend.

A more granular breakdown of trips per day of the week for Members and Casual users is presented below.

<div style="text-align: center;">
  <img src="./images/Trips_day_userype_dashboard.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

When examining trip counts per season, both user types undertake more trips in summer, followed by autumn, spring, and winter. However, Members exhibit a more consistent demand for bikes throughout the year, while Casual users demonstrate a steeper seasonal variation in demand.

<div style="text-align: center;">
  <img src="./images/Trips_per_season_usertype_dashboard.png" alt="" style="max-width:100%; height:auto;" />
</div>

### 4.2.2. Trip Durations

As established in the overview, the majority of trips are relatively short, with 80% under 20 minutes and 90% under 30 minutes. Nevertheless, Members and Casual users display distinct patterns in trip duration. Casual users, on average, ride longer trips. The following charts presents the mean and median trip duration for each user type.

<div style="text-align: center;">
  <img src="./images/mean_median_duration_usertype.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

A more detailed perspective can be seen from inspecting trip duration ranges for each user type.

<br>

<div style="text-align: center;">
  <img src="./images/duration_range_per_usertype.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

A similar overall pattern is observed when analyzing trip duration per season. However, there is a notable distinction. Namely, that member trip durations having much less of a spread throughout the year, while Casual users exhibit significant seasonal variation in demand, with winter ride durations dropping drastically compared to other seasons.

<br>

<div style="text-align: center;">
  <img src="./images/duration_season_usertype_mean.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

<div style="text-align: center;">
  <img src="./images/duration_season_usertype_median.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

Members similarly exhibit consistent trip duration patterns throughout the week, maintaining nearly identical durations on each day. In contrast, Casual users not only ride longer on average but also tend to ride significantly longer on weekends.

<br>

<div style="text-align: center;">
  <img src="./images/duration_median_mean_dayoftheweek.png" alt="" style="max-width:100%; height:auto;" />
</div>

### 4.2.3. Distribution by Station

One of the most significant distinctions between Member and Casual riders lies in the geographic patterns of their rides. The following visualization illustrates the overall station distribution for each user type, with circle size representing station usage.

<div style="text-align: center;">
  <img src="./images/Station Map - Casual Usertype.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

<div style="text-align: center;">
  <img src="./images/Station Map - Member Usertype.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

It's evident that the spread and diversity of stations used is noticeably higher for members rather than for casual users.

Furthermore, a drastic difference is observed in the localization of bike demand. This is most apparent when examining the top 10 stations for each user category, as depicted in the chart below.

<div style="text-align: center;">
  <img src="./images/TOP10_stations_usertype_both.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

Casual riders tend to ride along the coastline, with significant concentration at one specific station near the attraction spot. Members' demand for bikes, on the other hand, is concentrated in the downtown area and is more equally spread out.

### 4.2.4. Start time of the trips

Finally, another crucial dimension that differentiates Casual and Member riders is the timing of their trips.

As noted in the overview, general peak usage times occur around 8 AM and 5-6 PM. While this pattern holds true for both user types to some extent, Members adhere more closely to this "schedule", whereas Casual riders exhibit a less rigid pattern.

<div style="text-align: center;">
  <img src="./images/Start_Hour_by_usertype.png" alt="" style="max-width:100%; height:auto;" />
</div>

<br>

Casual user demand for bikes gradually increases throughout the day, peaking around 5 PM. Members, on the other hand, demonstrate a steeper increase and decrease in demand around peak times, with somewhat more stable usage during the day.

# 5. Conclusion

## 5.1. Recap of the Findings

Following is the summary of the findings.

- Members exhibit more consistent behavior. Their usage is more evenly distributed throughout the year and week. Slightly less stability is observed in their hourly usage. It follows prominent peak & fall pattern around 8AM in the morning and during Rush Hour.
- Casual riders demonstrate a significant preference for weekend riding and summer usage, with only 6% of their rides occurring in winter. Unlike Members, however, their trip start times follow a more gradual increase throughout the day, peaking around 5 PM.

- Members tend to ride shorter rides in general. Their trip durations remain are rather stable across seasons and weekdays.
- In contrast, Casual riders, on average, have longer rides, with notably longer durations on weekends and during summer compared to weekdays and other seasons, particularly winter.

- Geographically, members exhibit much wider spread spread throughout the city, with concentration in the downtown area.
- Casual users are noticeably less geographically widespread than Members. And their demand for bikes is concentrated along the coastline.

## 5.2. Data-driven insights and recommendations

Considering the characteristics of Member behavior, including weekday usage, morning and evening peak times, downtown concentration, and shorter trip durations, it is reasonable to assume that Members primarily utilize bike-sharing services for daily commuting, whether for work, school, or other routine, purpose-driven activities.
Conversely, Casual riders, with their weekend and summer preferences, less structured hourly usage, and longer trip durations, likely use bike-sharing for leisure purposes.

In order to convert casual users to members, generally, we explore one of 2 primary strategies.

1. Adapt to casual rider behaviour.  This involves modifying the membership offering to better align with the preferences of Casual riders.
2. Influence Casual Rider Behavior. This implies running existing membership promotions among Casual users to make them consider changing their behaviour and shifting to more business-oriented bike use.

A third option would lie somewhere in-between these two extremes.

While the first option implies directly addressesing the needs of Casual riders by adjusting membership offering.
The problem with that approach is the one mentioned in data limitations section. Namely, such approach implies changing the underlying financial structure of the membership offering
and, therefore, it is unknown whether it will remain as profitable as determined by financial analysts in its' current version.

The second option is limited in a sense that it's leveraging our data analysis only to effectively target marketing campaings for Casual riders.
However, it makes no use of the data to propose sensible adjustment to the membership offering as such.

Given the above cosniderations, we recommend a more conservative and gradual approach aligned with the second strategy.
Maintain current membership offering structure to preserve profitability as determined by financial analysts, utilize the findings to
execute promotions campaigns for Casual riders in a more targeted way. Additionally, we propose conducting personalized email
and phone campaigns in experimental manner, dividing Casual riders in control groups to identify the most effective strategies for increasing conversions.

Furthermore, we suggest gathering more insights from both Casual and Member users through survey comapaigns to understand their motivations and preferences better.
This could involve asking Casual riders about factors influencing their decision to become Members
and gathering feedback from Members about their satisfaction with the current membership offerings.

Ultimately, the decision on how to leverage these insights lies with the marketing team. However, from our side we will be happy to
collaborate with both marketing and finance teams to model potential scenarios, assess their impact, and support the implementation and evaluation of these strategies.
