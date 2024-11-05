
<h1 style="font-size: 1.5em; font-weight: bold; text-align: center;">Cyclistic Case Study</h1>

<div style="text-align: center;">
  <img src="./images/cyclistic_project_logo_v2.jpg" alt="" style="max-width:60%; height:auto;" />
</div>

<br>

**Table of contents:**

- [1. About the Project](#1-about-the-project)
  - [1.1. Introduction](#11-introduction)
  - [1.2. Tools Used](#12-tools-used)
- [2. Project Phases - Methodology](#2-project-phases---methodology)
- [3. Ask Phase](#3-ask-phase)
- [4. Prepare Phase](#4-prepare-phase)
  - [4.1. Data Source](#41-data-source)
  - [4.2. ROCCC evaluation](#42-roccc-evaluation)
  - [4.3. Data Storage](#43-data-storage)
- [5. Process Phase](#5-process-phase)
  - [5.1. Preliminary understanding of the data](#51-preliminary-understanding-of-the-data)
  - [5.2. Assessing data relevancy and limitations for analysis](#52-assessing-data-relevancy-and-limitations-for-analysis)
  - [5.3. Data Cleaning](#53-data-cleaning)
    - [5.3.1. Checking and removing duplicates](#531-checking-and-removing-duplicates)
    - [5.3.2. Check NULLs and Blanks](#532-check-nulls-and-blanks)
    - [5.3.3. DATETIME columns - NULL/Blank/Default check](#533-datetime-columns---nullblankdefault-check)
    - [5.3.4. Trip start \& end time validation](#534-trip-start--end-time-validation)
    - [5.3.5. Fixing integrity of station IDs and station names](#535-fixing-integrity-of-station-ids-and-station-names)
    - [5.3.6. Data Cleaning: Station unification 2nd iteration cycle](#536-data-cleaning-station-unification-2nd-iteration-cycle)
    - [5.3.7. Proceed to update start stations](#537-proceed-to-update-start-stations)
- [6. Process Phase: Data Transformation](#6-process-phase-data-transformation)
  - [6.1. Calculating \& Populating Trip Duration](#61-calculating--populating-trip-duration)
  - [6.2. Getting day, month, season, day\_type](#62-getting-day-month-season-day_type)
    - [6.2.1. Extract \& Populate Day \& Month](#621-extract--populate-day--month)
    - [6.2.2. Populate season from Month column](#622-populate-season-from-month-column)
    - [6.2.3. Populate day\_type from day column (weekend/weekday)](#623-populate-day_type-from-day-column-weekendweekday)
    - [6.2.4. Extracting hour of the day when trip started](#624-extracting-hour-of-the-day-when-trip-started)
- [7. Analyze Phase](#7-analyze-phase)
  - [7.1. Trip duration stats - high level Preview](#71-trip-duration-stats---high-level-preview)
  - [7.2. Trips per Usertype](#72-trips-per-usertype)
    - [7.2.1. Trips less than 60 seconds](#721-trips-less-than-60-seconds)
  - [7.3. Group trips by duration ranges](#73-group-trips-by-duration-ranges)
    - [7.3.1. Splitting further into Sub-Ranges to determine sensible outlier cut-off point](#731-splitting-further-into-sub-ranges-to-determine-sensible-outlier-cut-off-point)
    - [7.3.2. Quartiles per usertype](#732-quartiles-per-usertype)
  - [7.4. Trip duration stats revisited](#74-trip-duration-stats-revisited)
    - [7.4.1. Core overall trip duration stats](#741-core-overall-trip-duration-stats)
    - [7.4.2. Calculating Median trip\_duration](#742-calculating-median-trip_duration)
    - [7.4.3. Mean \& Total Trips](#743-mean--total-trips)
  - [7.5. Combined Result of trip duration stats queries](#75-combined-result-of-trip-duration-stats-queries)
  - [7.6. Trip count per at each trip start hout](#76-trip-count-per-at-each-trip-start-hout)
  - [7.7. Trips per stations analysis](#77-trips-per-stations-analysis)
  - [7.8. Most popular routes analysis (station pairs)](#78-most-popular-routes-analysis-station-pairs)
    - [7.8.1. Calculate stats for popular route trips](#781-calculate-stats-for-popular-route-trips)



# 1. About the Project

## 1.1. Introduction

In this scenario I act a junior data analyst working on the marketing team for a fictional bike-sharing company Cyclistic, operating in Chicago, US. Director of Marketing believes that key to company success lies in maximizing the number of annual memberships. I am tasked to analyze how casual riders and annual members use Cyclistic bikes differently. The ultimate goal is to use these insights to create a marketing strategy that converts casual riders into annual members. In the end, I need to present findings to Cyclistic's executives, supported with strong data and compelling visualizations.

- Company as a fleet of slightly under 10,000 bicycles that are geotracked and locked into a network of stations across Chicago. The bikes can be unlocked from one station and returned to any other station.
- Cylistic Pricing Plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase <u>single-ride</u> or <u>full-day passes</u> are referred to as **casual** riders. Customers who purchase <u>annual memberships</u> are Cyclistic **members**.
- Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders.

Beyond presenting the project's findings, in order to demonstrate the practical application of SQL skills and the thought process behind data analysis, I've included detailed step-by-step descriptions of the SQL code used in each stage of the project and reasoning behind it.

## 1.2. Tools Used

- SQL (MySQL Workbench 8.0) <img src="./images/sql.png" alt="SQL Logo" width="20"/>
- Tableau <img src="./images/tableau.png" alt="Tableau Logo" width="20"/>
- Visual Studio Code (VS Code) <img src="./images/visual_studio.png" alt="VSCode Logo" width="20"/>
- GitHub <img src="./images/github.png" alt="GitHub Logo" width="20"/>

# 2. Project Phases - Methodology

I've approached this project following 6 Data Analysis Phases model - Ask, Prepare, Process, Analyze, Share and Act.

<img src="./images/six_data_analysis_steps.png" alt="" style="max-width:60%; height:auto;" />

<br>

# 3. Ask Phase

This phase implies asking questions to understand business problem. It is largely covered within the scenario by Marketing Department director who set the question rather straightforward: "How do Casual and Member riders use bikes differently?". As well as asking further questions like "Why would casual riders buy Cyclistic annual memberships?" and "How can Cyclistic use digital media to influence casual riders to become members?" The latter two are not addressed to data analyst team directly and are rather supposed to be answered on the basis of the findings when answering the first question which is in the core of this project. 

# 4. Prepare Phase

In this stage, the data is obtained and prepared for storage and made ready for processing. It is also in this stage data is assessed on ROCCC scale (Reliable, Original, Comprehensive, Current and Cited/Credible) and approaches to data handling are adopted.

## 4.1. Data Source

Data for the project is obtained from [divvy-tripdata repository](https://divvy-tripdata.s3.amazonaws.com/index.html) in the form of CSV format. Within this scenario data is collected by the 1st party - Cyclistic themselves. It is based on real-company Lyft Bikes and Scooters and DivvyBikes data. Data is anonymized and shared under this [license](https://divvybikes.com/data-license-agreement).

## 4.2. ROCCC evaluation

- Reliable ✅ and Original ✅. Since it is 1st party and real-company data, it is concluded to be Reliable and Original.
- Current ✅. I downloaded CSVs for the period from 2023-08-01 to 2024-07-01, which is the most up-to-date.
- Comprehensive ⚠️. Data is anonymized, so we don't have some important info about the users, that would otherwise be beneficial for analysis. Therefore, the data is not fully Comprehensive, but also not insufficient.
- Credible ✅. Basically, it stems from the the fact that it's a 1st party data and is checked for credibility. However, as it will be demonstrated further, it does not mean that data is error-less.

## 4.3. Data Storage

Obtained CSVs are imported to MySQL Workbench 8.0 where pre-created table structure matches one of the CSV.

<details>
<summary>Code: Creating tables</summary>

```sql
CREATE TABLE divvy_tripdata_202308
(ride_id VARCHAR(50),
rideable_type VARCHAR(50),
start_at DATETIME,
ended_at DATETIME,
start_station_name VARCHAR(100),
start_station_id VARCHAR(50),
end_station_name VARCHAR(100),
end_station_id VARCHAR(50),
start_lat DOUBLE,
start_lng DOUBLE,
end_lat DOUBLE,
end_lng DOUBLE,
member_casual VARCHAR(50)
);
```

</details>

<br>

<details>
<summary>Code: Loading data from CSVs</summary>

```sql
LOAD DATA LOCAL INFILE '../divvy_tripdata_202308.csv' 
INTO TABLE divvy_tripdata_202308
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 LINES
--reapeat for all other tables
```

</details>

<br>

Then combined data from the raw tables into a single table, which matches the structure. Backup (copy) combined table.

<details>
<summary>Code: Combining Tables</summary>

```sql
INSERT INTO combined_yearly SELECT * FROM divvy_tripdata_202308; 
--reapeat for all other tables
```

</details>

<br>

# 5. Process Phase

In this stage I gain understanding of the data, brainstorm what can be used for analysis. Data is also checked for errors, nulls, duplicates, outliers, inconsistencies and other type of invalidities at this stage. Those are then cleaned: deleted, restored or merged where possible. Any data manipulation at this stage is assessed for impact and relevancy to the analysis prior to execution.

## 5.1. Preliminary understanding of the data

I start with visual inspection. I then perform total row count to make sure combining data from individual tables was successful, as well as to anchor overall dataset size. 

<details>
<summary>Code: Visual Inspection & Row Count</summary>

```sql
-- visual inspection
SELECT *
FROM combined_yearly
LIMIT 1000;

-- total row count 
SELECT COUNT(*) AS total_rows
FROM combined_yearly
```

Total Rows: **5715693**.

</details>

<br>

From visual inspecion, I quickly understood that dataset lacks easily usable PRIMARY KEY column which can be used as a machine number / helper column
for data manipulation convenience. Existing 'ride_id' column which is supposed to be unique from business-logic, lacks a machine-level counterpart.

<details>
<summary>Code: Add Machine Number column</summary>

```sql
ALTER TABLE combined_yearly
ADD COLUMN mrowid BIGINT AUTO_INCREMENT PRIMARY KEY;
```

</details>

## 5.2. Assessing data relevancy and limitations for analysis

Here is the given table schema (all given parameters):

| Field              | Data Type    | 
|--------------------|--------------|
| ride_id            | varchar(50)  |
| rideable_type      | varchar(50)  |
| started_at         | datetime     |
| ended_at           | datetime     |
| start_station_name | varchar(100) |
| start_station_id   | varchar(50)  |
| end_station_name   | varchar(100) |
| end_station_id     | varchar(50)  |
| start_lat          | double       |
| start_lng          | double       |
| end_lat            | double       |
| end_lng            | double       |
| member_casual      | varchar(50)  |

1. The goal is to identify difference between Members and Casual users. So the main target is to see how variable 'member_casual' interconnects with other variables. It will be central to our analysis.

2. Looking at the given dataset, there are 4 main parameter types that could be used to understand differences between Member & Casual riders:
   1. Geographical - stations and their coordinates.
   2. Date & Time - trip start and end date & time.
   3. Categorical - type of the bike used.
   4. Quantitative - number of trips as such (expressed by row count or count of 'ride_id' being a unique trip identifier)

3. What additional parameters can be derived from existing data to assist in analysis?

   1. 'started_at' and 'ended_at' variables can be used to extract day, month, season, trip start hour and trip duration. Combined with quantitative data, it allows, for example, to see who is using bikes more on Weekends vs. Weekdays, or in Summer vs. Autumn, who rides them longer, etc. 
   2. Other parameters do not contribute to any meaningful derived parameter creation. However, on their own, combined with quantiative data and statistical calculation, it can provide valuable insights. For example, it is possible to assess which station is used the most by users or map busy areas using coordiantes.
   3. Column 'mrowid' is generated as convenience helper column to identify rows. A machine number ID as opposed to 'ride_id' being business ID. 
   
Following, would be new generated fields: 

| Field         | Type        | Comment         |
|---------------|-------------|-----------------|
| mrowid        | bigint      | generated_field |
| trip_duration | int         | generated_field |
| trip_day      | varchar(20) | generated_field |
| trip_month    | varchar(20) | generated_field |
| trip_season   | varchar(20) | generated_field |
| start_hour    | int         | generated_field |
| day_type      | varchar(25) | generated_field |

4. What are the limitations of the data?

   1. Data is anonymized for privacy reasons. It does not carry valuable information about the users. For example, location of residence, account numbers, age, gender etc. It would allow a much more insightful analysis.
   2. In the scenario, trips are claimed to be geo-tracked. However, we only have available coordinates of start & end stations, which does not enable us to have an adequate idea about travelled distance. Therefore, route analysis, speed and things like that are not possible.

## 5.3. Data Cleaning

### 5.3.1. Checking and removing duplicates

Business logic dictates that ride_id column must be unique. Therefore, check if there are records where 'ride_id' is duplicate.

<details>
<summary>Code: Check Duplicate ride_id</summary>

```sql
WITH duplicates AS (
    SELECT
    ride_id,
    mrowid,
    started_at,
    ended_at,
    start_station_id,
    start_station_name,
    end_station_id,
    end_station_name,
    ROW_NUMBER() OVER(PARTITION BY ride_id) AS row_num
    FROM combined_yearly
)
SELECT *
FROM duplicates
WHERE row_num > 1
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> The above query returned 211 rows. Amount is insignificant for the analysis purposes, therefore additional cross-checking whether all other data in those rows is also duplicate is not needed and these rows can be deleted.
</div>

<br>

<details>
<summary>Code: Delete Duplicate</summary>

```sql
WITH duplicates AS (
    SELECT
    ride_id,
    mrowid,
    started_at,
    ended_at,
    start_station_id,
    start_station_name,
    end_station_id,
    end_station_name,
    ROW_NUMBER() OVER(PARTITION BY ride_id) AS row_num
    FROM combined_yearly
), only_duplicate_rows AS (
    SELECT mrowid
    FROM duplicates
    WHERE row_num > 1
)
DELETE FROM combined_yearly
WHERE mrowid IN (SELECT mrowid FROM only_duplicate_rows);
```

</details>

<br>

<div style="background-color: #3385ff; border-left: 5px solid #004494; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Side Note:</strong> Business logic dictates that database should not allow duplicate entry on "ride_id" field. The fact of breach of this
logic is noted down to be communicated with data team and consider constraining this field as UNIQUE.
</div>


### 5.3.2. Check NULLs and Blanks

Identify if any columns contain NULL values and/or are Blank.

<details>
<summary>Code: Checking NULLs</summary>

```sql
SELECT 
    SUM(CASE WHEN ride_id IS NULL THEN 1 ELSE 0 END) AS null_rideids,
    SUM(CASE WHEN rideable_type IS NULL THEN 1 ELSE 0 END) AS null_types,
    SUM(CASE WHEN started_at IS NULL THEN 1 ELSE 0 END) AS null_startimes,
    SUM(CASE WHEN ended_at IS NULL THEN 1 ELSE 0 END) AS null_endtimes,
    SUM(CASE WHEN start_station_name IS NULL THEN 1 ELSE 0 END) AS null_startstations,
    SUM(CASE WHEN start_station_id IS NULL THEN 1 ELSE 0 END) AS null_startstationids,
    SUM(CASE WHEN end_station_name IS NULL THEN 1 ELSE 0 END) AS null_endstationnames,
    SUM(CASE WHEN end_station_id IS NULL THEN 1 ELSE 0 END) AS null_endstationids,
    SUM(CASE WHEN start_lat IS NULL THEN 1 ELSE 0 END) AS null_startlat,
    SUM(CASE WHEN start_lng IS NULL THEN 1 ELSE 0 END) AS null_startlng,
    SUM(CASE WHEN end_lat IS NULL THEN 1 ELSE 0 END) AS null_endlat,
    SUM(CASE WHEN end_lng IS NULL THEN 1 ELSE 0 END) AS null_endlng,
    SUM(CASE WHEN member_casual IS NULL THEN 1 ELSE 0 END) AS null_usertype
FROM combined_yearly;
```

</details>

<br>

<details>
<summary>Code: Checking Blanks</summary>

```sql
SELECT 
    SUM(CASE WHEN ride_id = "" THEN 1 ELSE 0 END) AS blank_rideids,
    SUM(CASE WHEN rideable_type = "" THEN 1 ELSE 0 END) AS blank_types,
    SUM(CASE WHEN start_station_name = "" THEN 1 ELSE 0 END) AS blank_startstations,
    SUM(CASE WHEN start_station_id = "" THEN 1 ELSE 0 END) AS blank_startstationids,
    SUM(CASE WHEN end_station_name = "" THEN 1 ELSE 0 END) AS blank_endstationnames,
    SUM(CASE WHEN end_station_id = "" THEN 1 ELSE 0 END) AS blank_endstationids,
    SUM(CASE WHEN start_lat = "" THEN 1 ELSE 0 END) AS blank_startlat,
    SUM(CASE WHEN start_lng = "" THEN 1 ELSE 0 END) AS blank_startlng,
    SUM(CASE WHEN end_lat = "" THEN 1 ELSE 0 END) AS blank_endlat,
    SUM(CASE WHEN end_lng = "" THEN 1 ELSE 0 END) AS blank_endlng,
    SUM(CASE WHEN member_casual = "" THEN 1 ELSE 0 END) AS blank_usertype
FROM combined_yearly;
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> No NULL values were found. Blank values were identified as follows.
</div>

| blank_startstations | null_startstationids | null_endstationnames | null_endstationids | null_endlat | null_endlng |
|:------------------:|:--------------------:|:--------------------:|:------------------:|:-----------:|:-----------:|
|       947002       |        947002        |        989396        |       989396       |     7717    |     7717    |

### 5.3.3. DATETIME columns - NULL/Blank/Default check

DATETIME data type does not permit NULL values. Additionaly, column enforces NO-ZERO-VALUES, which means ‘0000-00-00 00:00:00’ is also not permitted. And since we do not know if there was any default value supposed for the DATETIME column when no data was obtained for that field, I run the following query to identify if there are significant outliers which would indicate that it is a “Default” value.

<details>
<summary>Code: Searching for default value</summary>

```sql
SELECT 
started_at, 
COUNT(*) AS date_count
FROM combined_yearly
GROUP BY started_at
ORDER BY date_count DESC
LIMIT 20;
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> No records that would appear as carrying default datetime value were identified. Date & Timesstamps are therefore concluded to be all valid.
</div>

### 5.3.4. Trip start & end time validation

Trip end time cannot be earlier than start time. Identifying if there are any such rows.

<details>
<summary>Code: Identifying erroneous end time records</summary>

```sql
SELECT *
FROM combined_yearly
WHERE ended_at - started_at < 0
```

</details>
<br>


<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> 411 rows identified. Amount is insignificant for the analysis and is, therefore, OK to delete.
</div>

<br>

<details>
<summary>Code: Deleting erroneous end time records</summary>

```sql
DELETE FROM combined_yearly
WHERE ended_at - started_at < 0
```

</details>

<br>

### 5.3.5. Fixing integrity of station IDs and station names

First, create indexes on start & end station ID and Name columns. Indexes will help optimize queries on respective columns.

<details>
<summary>Code: Create Indexes</summary>

```sql
CREATE INDEX idx_endstationids
ON combined_yearly (end_station_id);

CREATE INDEX idx_startstationids
ON combined_yearly (start_station_id);
-- same for names
```

</details>

#### 5.3.5.1. Count unique station IDs & Names (combined & individual) <!-- omit from toc -->

Counting objects of interest to anchor these numbers and compare against after data is cleaned/repaired.

<details>
<summary>Code: Count combined unique names</summary>

```sql
-- Calculating combined unique IDs
SELECT
COUNT(*) AS total_station_ids
FROM (
    SELECT start_station_id AS stationid FROM combined_yearly
    UNION
    SELECT end_station_id AS stationid FROM combined_yearly
) AS all_unique_IDs;

-- Calculating combined unique names
SELECT
COUNT(*) AS total_station_names
FROM (
    SELECT start_station_name AS stationname FROM combined_yearly
    UNION
    SELECT end_station_name AS stationname FROM combined_yearly
) AS all_unique_names;
```

</details>
<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> 1700 unique IDs in total. 1742 unique names in total.
</div>

Count individual unique names.

<details>
<summary>Code: Count individual unique names</summary>

```sql
-- Calculate individual unique IDs 
SELECT
COUNT(DISTINCT start_station_id) AS distinct_startids,
COUNT(DISTINCT end_station_id) AS distinct_endids
FROM combined_yearly;

-- Calculate individual unique names 
SELECT
COUNT(DISTINCT start_station_name) AS distinct_startnames,
COUNT(DISTINCT end_station_name) AS distinct_endnames
FROM combined_yearly;
```

</details>
<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> 1670 unique 'start_station_id' and 1682 unique 'end_station_id'. 1706 unique 'start_station_name' and 1720 unique 'end_station_name'.
</div>

---

**Interim Conclusion:** 

<div style="background-color: #f0f0f0; border-left: 5px solid #cccccc; color: #333333; padding: 10px; margin: 10px 0;">
<strong>Thought Process Behind:</strong> There are more unique IDs than unique names (1742 > 1700). This means some IDs are definitely linked to more than one name. Combined totals are bigger than individual totals. In addition, individual totals of start & end columns do not match in between themselves. It means that some station names or IDs are not shared, meaning they are only used in either 'start_station' or 'end_station' columns.

<br>

In addition, amount of unique station_ids is not too big, so conducting a visual inspection is viable. Most of station IDs are numeric. Some IDs carry over '.0' as if they were interpreted as a number in original dataset. 
Some seem to follow different code name structure, while others are obviously wrong, reading name instead of ID. 
</div>

Further inspecting and cleaning is required.

---

#### 5.3.5.2. Removing '.0' from station IDs <!-- omit from toc -->

Matching with REGEXP and batch-update by 'mrowid' to balance load.

<details>
<summary>Code: Remove '.0' from station IDs</summary>

```sql
UPDATE combined_yearly
SET start_station_id = REPLACE(start_station_id, '.0','')
WHERE start_station_id REGEXP '\\.[0-9]*[0-9][0-9]*$' AND mrowid >= 2800000;

UPDATE combined_yearly
SET end_station_id = REPLACE(end_station_id, '.0','')
WHERE end_station_id REGEXP '\\.[0-9]*[0-9][0-9]*$' AND mrowid <= 2800000;
```

</details>

#### 5.3.5.3. Trimming station_IDs & Names <!-- omit from toc -->

To remove leading & trailing whitespaces.

<details>
<summary>Code: Trimming station Names & IDs</summary>

```sql
UPDATE combined_yearly
SET end_station_id = TRIM(end_station_id)
WHERE LENGTH(end_station_id) <> LENGTH(TRIM(end_station_id));

UPDATE combined_yearly
SET start_station_id = TRIM(start_station_id)
WHERE LENGTH(start_station_id) <> LENGTH(TRIM(start_station_id));
-- same for names
```

</details>

#### 5.3.5.4. Fix station IDs linked to multiple station names <!-- omit from toc -->

Identify duplicate usage of station IDs.

<details>
<summary>Code: Identifying duplicate station IDs</summary>

```sql
WITH combined AS (
    SELECT 
    start_station_id AS station_id, 
    start_station_name AS station_name
    FROM combined_yearly

    UNION ALL

    SELECT
    end_station_id AS station_id, 
    end_station_name AS station_name
    FROM combined_yearly
),
ids_with_multiple_names AS (
    SELECT station_id
    FROM combined
    GROUP BY station_id
    HAVING COUNT(DISTINCT station_name) > 1
)
SELECT 
station_id, 
station_name, 
COUNT(*) AS occurrence
FROM combined
GROUP BY station_id, station_name
HAVING station_id IN (SELECT station_id FROM ids_with_multiple_names)
ORDER BY station_id;
```

</details>
<br>

<details>
<summary>Output: Example Extract for Preview</summary>

| station_id                          | station_name                                            | occurrence |
|-------------------------------------|---------------------------------------------------------|------------|
| 13290                               | Noble St & Milwaukee Ave                                | 10068      |
| 13290                               | Noble St & Milwaukee Ave (Temp)                         | 3897       |
| 15541                               | Buckingham Fountain (Columbus/Balbo)                    | 306        |
| 15541                               | Buckingham Fountain (Michigan/11th)                     | 1          |
| 15541                               | Buckingham Fountain (Temp)                              | 4449       |
| 15541.1.1                           | Buckingham - Fountain                                   | 380        |
| 15541.1.1                           | Buckingham Fountain                                     | 17945      |
| 21322                               | Grace & Cicero                                          | 54         |
| 21322                               | Grace St & Cicero Ave                                   | 289        |
| 21366                               | Spaulding Ave & 16th                                    | 8          |
| 21366                               | Spaulding Ave & 16th St                                 | 37         |
| 21371                               | Kildare & Chicago Ave                                   | 8          |
| 21371                               | Kildare Ave & Chicago Ave                               | 60         |
| 21393                               | Oketo Ave & Addison                                     | 1          |
| 21393                               | Oketo Ave & Addison St                                  | 24         |
| 23114                               | Kildare Ave & 85th St                                   | 47         |
| 23114                               | Kildare Ave & 85th St (Kostner Ave & 87th St TEMPORARY) | 10         |
| 23187                               | Lockwood Ave & Grand Ave                                | 96         |
| 23187                               | Lockwood Ave and Grand Ave                              | 60         |
| 23215                               | Lexington & California Ave                              | 29         |
| 23215                               | Lexington St & California Ave                           | 258        |
| 24156                               | Glenlake Ave & Pulaski Rd                               | 24         |
| 24156                               | Granville Ave & Pulaski Rd                              | 74         |
| 331                                 | Halsted St & Clybourn Ave                               | 40272      |
| 331                                 | Pulaski Rd & 21st St                                    | 205        |
| 514                                 | Public Rack - Hamlin Ave & Grand Ave                    | 41         |
| 514                                 | Ridge Blvd & Howard St                                  | 1795       |
| 515                                 | Paulina St & Howard St                                  | 4727       |
| 515                                 | Public Rack - Hamlin Ave & Chicago Ave                  | 8          |
| 517                                 | Clark St & Jarvis Ave                                   | 2675       |
| 517                                 | Public Rack - Pulaski Rd & Armitage Ave                 | 558        |
| 518                                 | Conservatory Dr & Lake St                               | 1149       |

</details>

<br>

---

**Interim Conclusion 2:** 183 unique combinations where ID is associated with more than one name. Visually skimming through the dataset, it is noticeable, that many duplicate ID records are due to minor differences in station names.

**Examine what could be minor differences:**

- "(Temp)" bit present in one record type, but not the other.
- "&" used as conjuction in one record vs. "and" in the other.
- "Street" and/or "Avenue" written as "St", "St.", "Ave" or "Ave." and vice-a-versa.
- Typos and minor symbol differences.

<div style="background-color: #f0f0f0; border-left: 5px solid #cccccc; color: #333333; padding: 10px; margin: 10px 0;">
<strong>Thought Process Behind:</strong> It is required to merge stations where reasonably justified or clean otherwise, in order to make sure that station-relevant statistics are accurate and provide valid insights for trip analysis. 
</div>

<br>

**Additional ways to identify where station merge is suitable include:**

- Checking whether latitude and longitude match or lie within plausible difference in proximity. Similarly for records where station names & IDs are blank.
- In cases where there is difference which is not apparently wrong, ranking rows based on occurrence and merging into the one that clearly dominates may be viable.
- Using external address validation software to check whether difference is caused by obsolete records. (Not applied in this project)
- Using Levenshtein (String) Distance calculation. (Not applied in this project)

Leftover inconsistencies (if any) can be dealt with on individual basis.

---

#### 5.3.5.5. Addressing minor and apparent string differences in station names <!-- omit from toc -->

Replace '(Temp)' with nothing ''

<details>
<summary>Code: Replace '(Temp)'</summary>

```sql
UPDATE combined_yearly
SET start_station_name = TRIM(REPLACE(start_station_name, '(Temp)', ''))
WHERE start_station_name LIKE '%(Temp)%';

UPDATE combined_yearly
SET end_station_name = TRIM(REPLACE(end_station_name, '(Temp)', ''))
WHERE end_station_name LIKE '%(Temp)%';
```

</details>
<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> this updated 20772 rows for 'start_station_name' and 20338 rows for 'end_station_name'.
</div>

Replace 'and' with '&' for consistency

<div style="background-color: #3385ff; border-left: 5px solid #004494; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Note:</strong> using REPLACE() as per above example does not work as intended here, because 'and' also occurs as part of other words.
Instead, using REGEXP_REPLACE() is required. Same could also be used for '(Temp)' for extra safety.
</div>

<details>
<summary>Code: Replace 'and' with '&' for consistency</summary>

```sql
UPDATE combined_yearly
SET start_station_name = REGEXP_REPLACE(start_station_name, '\\band\\b', '&')
WHERE start_station_name REGEXP '\\band\\b';

UPDATE combined_yearly
SET end_station_name = REGEXP_REPLACE(end_station_name, '\\band\\b', '&')
WHERE end_station_name REGEXP '\\band\\b';
```

</details>
<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> this updated 5681 rows for 'start_station_name' and 5534 rows for 'end_station_name'.
</div>

<br>

<div style="background-color: #3385ff; border-left: 5px solid #004494; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Note:</strong> substituting "St", "Ave" and so on, was attempted, but did not result in any duplication reduction, therfore is not mentioned here.
</div>

#### 5.3.5.6. Standartizing Coordinates <!-- omit from toc -->

Round coordiantes to 4 decimal points. 

<div style="background-color: #f0f0f0; border-left: 5px solid #cccccc; color: #333333; padding: 10px; margin: 10px 0;">
<strong>Thought Process Behind:</strong> Inaccuracy of 4th decimal points within limits of Chicago city amounts roughly to 11m on the ground which is neglectable amount for identifying station merge potential. Because it is logical to assume that stations would be placed much further away from each other than 11 or even 111 meters.
</div>

<details>
<summary>Code: Round coordinates to 4th decimal point</summary>

Balancing load with batch-update, segmenting via 'mrowid'.

```sql
UPDATE combined_yearly
SET start_lat = ROUND(start_lat, 4)
WHERE mrowid > 2800000;

UPDATE combined_yearly
SET start_lat = ROUND(start_lat, 4)
WHERE mrowid <= 2800000;

UPDATE combined_yearly
SET start_lng = ROUND(start_lng, 4)
WHERE mrowid <= 2800000;

UPDATE combined_yearly
SET start_lng = ROUND(start_lng, 4)
WHERE mrowid > 2800000;

-- similarly for 'end_lat' and 'end_lng'
```

</details> <br>

---

**Interim Obseravations:**

- As established before, there are almost 1 million records where either start_station_id and start_station_name OR end_station_id and end_station_name is EMPTY.

| null_startstations | null_startstationids | null_endstationnames | null_endstationids | null_endlat | null_endlng |
|:------------------:|:--------------------:|:--------------------:|:------------------:|:-----------:|:-----------:|
|       947002       |        947002        |        989396        |       989396       |     7717    |     7717    |

<br>

- Distinct combinations of end_station_name, end_station_id, end_lat, end_lng is significantly lower than same of start startions, while distinct combinations of just end_station_name and end_station_id is about the same as that of the start stations.  

| distinct_starts | distinct_ends | distinct_start_coords | distinct_end_coords |
|:---------------:|:-------------:|:---------------------:|:-------------------:|
|       1753      |      1768     |         44887         |         2886        |

<br>

<div style="background-color: #f0f0f0; border-left: 5px solid #cccccc; color: #333333; padding: 10px; margin: 10px 0;">
<strong>Thought Process Behind:</strong> Therefore, it makes sense to start standartizing on end_stations and extrapolate to Start Stations, because these are essentially same stations, categorized differently based on Inbound or Outbound trip direction. Records with Blank station data can be reviewed based on coordinate proximity.
</div>

---

#### 5.3.5.7. Create separate end station data table <!-- omit from toc -->

This table will store unique end station data list and faster to run JOIN queries on, since it'll be much smaller than on main 'combined_yearly' table. It is employed in order to carry out station merging more efficiently.

<details>
<summary>Code: Create separate station data table</summary>

```sql
INSERT INTO distinct_ends_with_coords_v2 (end_station_id, end_station_name, end_lat, end_lng)
SELECT DISTINCT end_station_id, end_station_name, end_lat, end_lng
```

</details> <br>

Also populate occurrence per combination of 4 parameters "station name, id, lat & lng" via following query:

<details>
<summary>Code: Populate occurrence per combination</summary>

```sql
SELECT
t1.end_station_id,
t1.end_station_name,
t1.end_lat,
t1.end_lng,
COUNT(*) AS occurrence
FROM combined_yearly t1
JOIN distinct_ends_with_coords_v2 t2
ON t1.end_station_id = t2.end_station_id
AND t1.end_station_name = t2.end_station_name
AND t1.end_lat = t2.end_lat
AND t1.end_lng = t2.end_lng
GROUP BY t1.end_station_id, t1.end_station_name, t1.end_lat,t1.end_lng;
```

</details> <br>

#### 5.3.5.8. Fix Blanks station records <!-- omit from toc -->

First, convert all blank to NULLs to be able to utilize smart SQL handling of NULL values as opposed to blanks.

<details>
<summary>Code: Convert Blanks to NULLs</summary>

```sql
UPDATE combined_yearly
SET end_station_id = NULL
WHERE end_station_id = ''
AND mrowid < 2800000;

UPDATE combined_yearly
SET end_station_id = NULL
WHERE end_station_id = ''
AND mrowid >= 2800000;
-- also in station table
```

</details>

<br>

Check all records where there are NULL station_name & station_id, but non-NULL corresponding coordinates. If these coordinates have exact and unambiguous match to one of the existing station_id & station_name combination, populate this name & id instead of NULLs. 

<details>
<summary>Code: Match NULL stations with non-NULL via coordinates</summary>

```sql
UPDATE distinct_ends_with_coords_v2 t1
JOIN (
    SELECT 
    end_station_id, 
    end_station_name, 
    end_lat, 
    end_lng
    FROM distinct_ends_with_coords_v2
    WHERE end_station_name IS NOT NULL 
    AND end_station_id IS NOT NULL
    GROUP BY end_station_id, end_station_name, end_lat, end_lng
    HAVING COUNT(*) = 1  -- Unambiguous match
) t2
ON t1.end_lat = t2.end_lat 
AND t1.end_lng = t2.end_lng
SET 
    t1.end_station_name = t2.end_station_name, 
    t1.end_station_id = t2.end_station_id
WHERE t1.end_station_name IS NULL 
  AND t1.end_station_id IS NULL;
  ```

</details>


#### 5.3.5.9. Uniforming End station ID, Name and Coordinate combinations <!-- omit from toc -->

Before fixing start stations based on ends stations as established earlier, end stations can be further cleaned and uniformed in between themsevles.

<div style="background-color: #f0f0f0; border-left: 5px solid #cccccc; color: #333333; padding: 10px; margin: 10px 0;">
<strong>Thought Process Behind:</strong> Aim is to get as close to having one set of latitude & longitude per combination of end_station_id and end_station_name as reasonably possible, and eventually have single record per station. It is logical that station is not moving (stationary) and should have similar coordinates at all times. The assumption is that spread in slight coordinate difference is caused by inaccuracy of GPS recording, and that is what we need to fix for the purpose of attributing trips to the correct station and produce meaningful station-relevant statistics.
</div>

#### 5.3.5.10. Correcting Combinations of ID & Name with more than 1 set of coordinates <!-- omit from toc -->

Create temporary tables for those end_station_name & end_station_id combinations, that have more than one set of coordinates associated with them.

<details>
<summary>Code: Create temp table for stations with multiple coordinates</summary>

```sql
CREATE TEMPORARY TABLE ends_with_multiple_coords AS (
SELECT 
    end_station_name, 
    end_station_id,
    COUNT(DISTINCT end_lat, end_lng) AS number_of_coords
FROM combined_yearly
GROUP BY end_station_name, end_station_id
HAVING COUNT(DISTINCT end_lat, end_lng) > 1);
```

</details> <br>

Join the above temporary table on separate end_station data table created before to obtain and evaluate occurrences (associated trips count) only for those records which have similar station ID & Name, but have more than one set of coordinates, to see if any set of coordinates is a clear outlier. Save result in another temporary table for the time being.  

<details>
<summary>Code: Match occurrences with multiple coordinate station records</summary>

```sql
CREATE TEMPORARY TABLE ends_with_multiple_coords_occurrences_withcoords AS (
    t1.end_station_name, 
    t1.end_station_id, 
    t1.end_lat, 
    t1.end_lng, 
    t1.occurrence
FROM distinct_ends_with_coords_v2 t1
JOIN ends_with_multiple_coords t2
    ON t1.end_station_name = t2.end_station_name
    AND t1.end_station_id = t2.end_station_id
GROUP BY t1.end_station_name, t1.end_station_id, t1.end_lat, t1.end_lng, t1.occurrence
ORDER BY t1.end_station_name, t1.end_station_id, t1.occurrence DESC);
```

</details>

#### 5.3.5.11. Proximity check on records identified in previous table <!-- omit from toc -->

Perform proximity check on the table from the previous step and store that in separate table too. 

<div style="background-color: #f0f0f0; border-left: 5px solid #cccccc; color: #333333; padding: 10px; margin: 10px 0;">
<strong>Thought Process Behind:</strong> This further narrows down the scope for merging and adds an extra layer of safety to merge only those records which definitely make sense to be merge.
</div>

<details>
<summary>Code: Proximity check on multiple coordinate stations</summary>

```sql
CREATE TEMPORARY TABLE endswithmultiplecoordsoccurrences_proximitypassed AS (
SELECT 
    t1.end_station_name,
    t1.end_station_id,
    t1.end_lat AS lat1,
    t1.end_lng AS lng1,
    t2.end_lat AS lat2,
    t2.end_lng AS lng2,
    t1.occurrences
FROM ends_with_multiple_coords_occurrences_withcoords t1
JOIN ends_with_multiple_coords_occurrences_withcoords t2 
    ON t1.end_station_name = t2.end_station_name 
    AND t1.end_station_id = t2.end_station_id
    AND (t1.end_lat != t2.end_lat OR t1.end_lng != t2.end_lng)
    AND ABS(t1.end_lat - t2.end_lat) <= 0.0002 
    AND ABS(t1.end_lng - t2.end_lng) <= 0.0002
ORDER BY end_station_id, occurrences DESC);
```

</details> <br>

#### 5.3.5.12. Rank records that passed proximity check based on occurrence of each set <!-- omit from toc -->

Now use previous table to rank each stations' uniqe set of coordinates based on trip count associated (occurrences). Save results in separate temporary table.

<details>
<summary>Code: Rank sets of coordiantes based on occurrence </summary>

```sql
CREATE TABLE ranked_proximity_coords AS
SELECT
    end_station_name,
    end_station_id,
    lat1 AS latitude,
    lng1 AS longitude,
    occurrences,
    RANK() OVER (PARTITION BY latitude, longitude ORDER BY occurrences DESC) AS ranking
FROM endswithmultiplecoordsoccurrences_proximitypassed
ORDER BY end_station_name, end_station_id, rank;
```

</details>

<br>

#### 5.3.5.13. Update initial distinct table from ranking <!-- omit from toc -->

<details>
<summary>Code: Update query </summary>

```sql
-- First, double-check and review ranking results and see if functioned as expected and merge looks adequate indeed. 

SELECT *
FROM ranked_proximity_coords;

-- Once confirmed, carry out update. 

UPDATE distinct_ends_with_coords_v2 t1
JOIN ranked_proximity_coords t2
    ON t1.end_station_name = t2.end_station_name
    AND t1.end_station_id = t2.end_station_id
SET 
    t1.end_lat = t2.latitude,
    t1.end_lng = t2.longitude
WHERE t2.ranking = 1; 
```

</details>

<br>

---

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> This has matched 206 rows and updated 116 of them.
</div>

Rerunning previous check for NON-NULL station name & id combinations have >1 set of coordinates, returns 82 rows. These are the combinations that have not passed proximity in previous step. 

<div style="background-color: #f0f0f0; border-left: 5px solid #cccccc; color: #333333; padding: 10px; margin: 10px 0;">
<strong>Thought Process Behind:</strong> However, we still need to get single record for each station. Therefore, need to check how big is the impact (sum of occurrence amounts) of these stations to evaluate if it is justified to expand or fully eliminate proximity check and merge merely on the basis of clear occurrence outliers.  
</div>

---

#### 5.3.5.14. Impact Assessment of leftover >1 set of coords duplicates <!-- omit from toc -->

<details>
<summary>Code: Impact Assessment</summary>

```sql
SELECT 
SUM(occurrence) AS total_impact
FROM (
    SELECT
    end_station_name,
    end_station_id,
    end_lat,
    end_lng,
    occurrence,
    ROW_NUMBER() OVER (PARTITION BY end_station_name, end_station_id ORDER BY occurrence DESC) AS ranking
    FROM (
        SELECT 
        t1.end_station_name, 
        t1.end_station_id, 
        t1.end_lat, 
        t1.end_lng, 
        t1.occurrence
        FROM distinct_ends_with_coords_occurrences t1
        JOIN ends_with_multiple_coords_v2 t2
        ON t1.end_station_name = t2.end_station_name
        AND t1.end_station_id = t2.end_station_id
        GROUP BY t1.end_station_name, t1.end_station_id, t1.end_lat, t1.end_lng, t1.occurrence
        ORDER BY t1.end_station_name, t1.end_station_id, t1.occurrence DESC
    ) AS impact_assessment
) AS rankings
WHERE ranking > 1;
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> SUM is equal to 25832 for any rank except 1, while total occurrences for all coordinate sets of these stations
amounts to 725175. This indicates total domination of rank 1 rows over all others. In addition, total number of records with rank >1 is insignificant anyway.
Therefore, it is considered justified for our analysis to merge stations on rank 1 to achieve full uniformity.
</div>

<div style="background-color: #3385ff; border-left: 5px solid #004494; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Note:</strong> As noted earlier, in general, such data inaccurracy must be addressed at the root, before it even enters the database. This would be something to communicate with data science team of the company to introduce solutions. However, for now it is outside of the scope of this project.
</div>

#### 5.3.5.15. Further unification of end stations <!-- omit from toc -->

Create ranking table based on occurrence of leftover combinations of stations with >1 set of coordinates.

<details>
<summary>Code: Create temp table for leftover stations ranked by occurrence</summary>

```sql
CREATE TEMPORARY TABLE ranked_occurrences_leftovers AS
SELECT
    end_station_name,
    end_station_id,
    end_lat,
    end_lng,
    occurrence,
    RANK() OVER (PARTITION BY end_station_name, end_station_id ORDER BY occurrence DESC) AS ranking
FROM (
SELECT 
    t1.end_station_name, 
    t1.end_station_id, 
    t1.end_lat, 
    t1.end_lng, 
    t1.occurrence
FROM distinct_ends_with_coords_v2 t1
JOIN ends_with_multiple_coords_v2 t2
    ON t1.end_station_name = t2.end_station_name
    AND t1.end_station_id = t2.end_station_id
GROUP BY t1.end_station_name, t1.end_station_id, t1.end_lat, t1.end_lng, t1.occurrence
) AS rankings
ORDER BY end_station_name, end_station_id, ranking;
```

</details>

Run update query to assign coordinates to end_station_name and end_station_id combinations as per those associated with rank 1 of lat & lng set.

<details>
<summary>Code: Update leftover stations based on rank</summary>

```sql
UPDATE distinct_ends_with_coords_v2 t1
JOIN ranked_occurrences_leftovers t2
    ON t1.end_station_name = t2.end_station_name
    AND t1.end_station_id = t2.end_station_id
SET 
    t1.end_lat = t2.end_lat,
    t1.end_lng = t2.end_lng
WHERE t2.ranking = 1; 
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> 175 rows matched, 93 updated. Now number of unique combinations of non-null name,id,lat,lng is equal to distinct combinations of name & id.
</div>

<br>

#### 5.3.5.16. Checking reverse duplicate coordinate ambiguity <!-- omit from toc -->

Check reverse coordinate ambiguity - where similar Lat & Lng associated with >1 station name & ID.

<details>
<summary>Code: duplicate ID & names per set of coordinates</summary>

```sql
CREATE TEMPORARY TABLE duplicate_coords AS (
SELECT 
end_lat,
end_lng,     
COUNT(DISTINCT end_lat, end_lng) AS number_of_coords 
FROM distinct_ends_with_coords_v2 
GROUP BY end_lat, end_lng 
HAVING COUNT(DISTINCT end_station_name, end_station_id) > 1
);
```

</details>

#### 5.3.5.17. Approximate impact assessment on duplicate coords <!-- omit from toc -->


<details>
<summary>Code: Approximate impact assessment</summary>

```sql
SELECT
SUM(occurrence) AS total_impact
FROM (
SELECT 
    t1.end_station_name, 
    t1.end_station_id, 
    t1.end_lat, 
    t1.end_lng, 
    t1.occurrence
FROM distinct_ends_with_coords_occurrences t1
JOIN duplicate_coords t2
    ON t1.end_lat = t2.end_lat
    AND t1.end_lng = t2.end_lng
GROUP BY t1.end_station_name, t1.end_station_id, t1.end_lat, t1.end_lng, t1.occurrence
ORDER BY t1.end_station_name, t1.end_station_id, t1.occurrence DESC) as impact_assessment;
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> There are 48 cases where same set of coords associated with >1 pair of ID & name. In total, there are 31884 trips for all of those coordinates. 
</div>

<div style="background-color: #f0f0f0; border-left: 5px solid #cccccc; color: #333333; padding: 10px; margin: 10px 0;">
<strong>Thought Process Behind:</strong> It is small neglectable amount, so unification on the basis of occurrence rank is sufficient good-guess for our purposes. Additionally, it is visually observed that many such coordinate duplicate cases are related to other ambiguities, such as same ID being associated with multiple station names; same name associated with different IDs. In many cases, human heuristic makes it evident
that there's either a typo or otherwise duplicate record.
</div>

For example:

|           start_station_name           | latitude | longitude |
|:--------------------------------------:|:--------:|:---------:|
|     Museum of Science and Industry     |  41.7918 |  -87.5841 |
|     Museum of Science and Industry     |  41.7917 |  -87.5841 |
| Griffin Museum of Science and Industry |  41.7917 |  -87.5839 |
| Griffin Museum of Science and Industry |  41.7918 |  -87.5839 |

<br>

Therefore, this update is not only justified on the basis of coordinates duplicacy, but also desired because it solves other type of duplication issues in one go.

#### 5.3.5.18. Proceed with update based on duplicate coord occurrence ranking <!-- omit from toc -->

First create table, then update.  

<details>
<summary>Code: Approximate impact assessment</summary>

```sql
CREATE TABLE ranked_coord_duplicate_occurrence AS (
SELECT 
    t1.end_station_name, 
    t1.end_station_id, 
    t1.end_lat, 
    t1.end_lng, 
    t1.occurrence,
    RANK() OVER (PARTITION BY end_lat, end_lng ORDER BY occurrence DESC) AS ranking
FROM distinct_ends_with_coords_occurrences t1
JOIN duplicate_coords t2
    ON t1.end_lat = t2.end_lat
    AND t1.end_lng = t2.end_lng
GROUP BY t1.end_station_name, t1.end_station_id, t1.end_lat, t1.end_lng, t1.occurrence
ORDER BY t1.end_lat, t1.end_lng, t1.occurrence DESC)
```

```sql
UPDATE distinct_ends_with_coords_v2 t1
JOIN ranked_coord_duplicate_occurrence t2
    ON t1.end_lat = t2.end_lat
    AND t1.end_lng = t2.end_lng
SET 
    t1.end_station_name = t2.end_station_name,
    t1.end_station_id = t2.end_station_id
WHERE t2.end_station_name IS NOT NULL AND t2.ranking = 1; 
```

</details>

<br>

### 5.3.6. Data Cleaning: Station unification 2nd iteration cycle

Some updates may have resulted in creatign as overlapping records, since multiple updates were carried out on the combination of both ID & Name together.
Therefore, 2nd iteration of clean-up is needed.  

#### 5.3.6.1. Check for IDs with >1 associated names <!-- omit from toc -->

Impact assessment for IDs with >1 name associated.

<details>
<summary>Code: Impact Assessment for IDs with >1 name associated</summary>

```sql
WITH ids_with_multiple_names AS (
    SELECT 
    end_station_id,
    COUNT(*) AS names_associated
    FROM distinct_ends_with_coords_v2
    GROUP BY end_station_id
    HAVING COUNT(DISTINCT end_station_name) > 1
    ORDER BY names_associated DESC
), occurrence_count AS (
    SELECT 
    end_station_id, 
    end_station_name,
    end_lat,
    end_lng,
    COUNT(*) AS occurrence
    FROM combined_yearly
    GROUP BY end_station_id, end_station_name, end_lat, end_lng
    HAVING end_station_id IN (SELECT end_station_id FROM ids_with_multiple_names)
    ORDER BY end_station_id, occurrence DESC
)
SELECT
SUM(occurrence) AS impact_assessment
FROM occurrence_count;
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> Count of such distinct records is 162 which in total account for 198979 records (from combined_yearly table).
</div>

Now rank those records based on occurrence to check for merging potential.

<details>
<summary>Code: Ranking based on occurrence</summary>

```sql
WITH ids_with_multiple_names AS (
    SELECT 
    end_station_id,
    COUNT(*) AS names_associated
    FROM distinct_ends_with_coords_v2
    GROUP BY end_station_id
    HAVING COUNT(DISTINCT end_station_name) > 1
    ORDER BY names_associated DESC
), occurrence_count AS (
    SELECT 
    end_station_id, 
    end_station_name,
    end_lat,
    end_lng,
    COUNT(*) AS occurrence
    FROM combined_yearly
    GROUP BY end_station_id, end_station_name, end_lat, end_lng
    HAVING end_station_id IN (SELECT end_station_id FROM ids_with_multiple_names)
    ORDER BY end_station_id, occurrence DESC
), idswithmultplnames_occurencerank AS (
    SELECT
    end_station_id,
    end_station_name,
    end_lat,
    end_lng,
    occurrence,
    RANK() OVER (PARTITION BY end_station_id ORDER BY occurrence DESC) AS ranking
    FROM occurrence_count
)
SELECT 
SUM(occurrence) AS rank_1_impact
FROM idswithmultplnames_occurencerank
WHERE ranking = 1;
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> Out of these 198979, 191387 are attributed to rows ranked 1. Unification is, therefore, justified. Proceed with update.
</div>

<div style="background-color: #f0f0f0; border-left: 5px solid #cccccc; color: #333333; padding: 10px; margin: 10px 0;">
<strong>Thought Process Behind:</strong> Also unify coordinates based on this data, since it logically makes sense. And for our purpose with such neglectable amount of occurrences of rank >1 records,
this is will not hurt the analysis even in cases where such merge hypothetically would not be justified.
</div>

<details>
<summary>Code: Updating based on previous ranking</summary>

```sql
UPDATE distinct_ends_with_coords_v2 t1 --and combined_yearly
JOIN idswithmultplnames_occurencerank t2
ON t1.end_station_id = t2.end_station_id
SET t1.end_station_name = t2.end_station_name,
t1.end_lat = t2.end_lat,
t1.end_lat = t2.end_lat
WHERE t2.ranking = 1;  
```

</details>

#### 5.3.6.2. Reverse assessment - Names with >1 IDs associated <!-- omit from toc -->

Similar approach. Impact Assessment for Names with >1 IDs associated.

<details>
<summary>Code: Impact Assessment for names with >1 ID associated</summary>

```sql
WITH names_with_multiple_ids AS (
    SELECT 
    end_station_name,
    COUNT(*) AS ids_associated
    FROM distinct_ends_with_coords_v2
    GROUP BY end_station_name
    HAVING COUNT(DISTINCT end_station_id) > 1
    ORDER BY ids_associated DESC
), occurrence_count AS (
    SELECT 
    end_station_id, 
    end_station_name,
    end_lat,
    end_lng,
    COUNT(*) AS occurrence
    FROM combined_yearly
    GROUP BY end_station_id, end_station_name, end_lat, end_lng
    HAVING end_station_name IN (SELECT end_station_name FROM names_with_multiple_ids)
    ORDER BY end_station_name, occurrence DESC
), nameswithmultplids_occurencerank AS (
    SELECT
    end_station_id,
    end_station_name,
    end_lat,
    end_lng,
    occurrence,
    RANK() OVER (PARTITION BY end_station_name ORDER BY occurrence DESC) AS ranking
    FROM occurrence_count
)
SELECT
SUM(occurrence) AS total_impact
FROM nameswithmultplids_occurrencerank;
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> There are 66 records where name associated with >1 ID. Unique names 29. All combined, they amount to 15687 records, 9081 of which attributed to rank 1 records.
</div>

Therefore, for similar reasons as previously, unification on the rank basis is justified.

<details>
<summary>Code: Impact Assessment for names with >1 ID associated</summary>

```sql
UPDATE combined_yearly t1 
JOIN nameswithmulplids_occurrencerank t2
ON t1.end_station_name = t2.end_station_name
SET t1.end_station_id = t2.end_station_id,
t1.end_lat = t2.end_lat,
t1.end_lat = t2.end_lat
WHERE t2.ranking = 1;
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> Update query updated 32 rows on distinct_ends_with_coords_v2 table.
</div>

<br>

#### 5.3.6.3. Deleting remaining full duplicates <!-- omit from toc -->

As a result of previous updates, there were identified to be 159 duplicate records in secondary distinct_ends_with_coords_v2 table where all 4 parameters
(station_name, id, lat, lng) were identical. In order to clean those out, I have introduced 'idd_column' AUTO_INCREMENT PRIMARY KEY column on this table, since
otherwise it was not possible to target only one of the duplicates. Now delete those duplicates. 

<details>
<summary>Code: Deleting full duplicates from secondary station table</summary>

```sql
START TRANSACTION; 

WITH ranked_duplicates AS (
    SELECT 
    idd_column,
    ROW_NUMBER() OVER (PARTITION BY end_station_name, end_station_id, end_lat, end_lng ORDER BY end_station_id) AS rownum
    FROM distinct_ends_with_coords_v2
)
DELETE FROM distinct_ends_with_coords_v2
WHERE idd_column IN (
    SELECT idd_column
    FROM ranked_duplicates
    WHERE rownum > 1
);

COMMIT; 
```

</details>

</br>

---

**Interim Result of unification:**

| distinct_ids | distinct_names | distinct_coords | distinct_idname_combos | distinct_all_combo |
|:------------:|:--------------:|:---------------:|:----------------------:|:------------------:|
|     1609     |      1609      |       1705      |          1609          |        1705        |

<br>

Names & IDs are now in order.

---

#### 5.3.6.4. Fixing NULL station names <!-- omit from toc -->

Preliminary check on NULL values was conducted when blanks were converted to NULLs. Then I have only merged those NULL records where coordinates matched unambiguously with one of the stations. Next steps are to carry out similar in-depth NULL station name & ID record evaluation based on coordinate proximity and occurrence count.

#### 5.3.6.5. Proximity check for NULL stations <!-- omit from toc -->

<details>
<summary>Code: Proximity check on NULL stations</summary>

```sql
CREATE TABLE first_null_update AS (
    SELECT
    t1.end_station_name,
    t1.end_station_id,
    t1.end_lat,
    t1.end_lng,
    t2.end_station_name AS name_to_update,
    t2.end_station_id AS id_to_update,
    t2.end_lat AS lat_to_update,
    t2.end_lng AS lng_to_update
    FROM distinct_ends_with_coords_v2 t1
    JOIN distinct_ends_with_coords_v2 t2
    ON (ABS(t1.end_lat - t2.end_lat) <= 0.0002) AND (ABS(t1.end_lng - t2.end_lng) <= 0.0002)
    WHERE t1.end_station_name IS NOT NULL 
    AND t2.end_station_id IS NULL
) AS iteration1;
```

</details>

#### 5.3.6.6. Update query based on proximity <!-- omit from toc -->

<details>
<summary>Code: Update based on proximity</summary>

```sql
UPDATE distinct_ends_with_coords_v2 t1
JOIN first_null_update t2
ON t2.lat_to_update = t1.end_lat AND t2.lng_to_update = t1.end_lng
SET 
t1.end_station_name = t2.end_station_name,
t1.end_station_id = t2.end_station_id
WHERE t1.end_station_name IS NULL AND t1.end_station_id IS NULL;

--same for main combined_yearly table
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> Merged 29 records out of 911. Not a major success.
</div>

#### 5.3.6.7. Update main table combined_yearly <!-- omit from toc -->

While end_stations were updated simulatenously in both tables for the most part, it is rerun to be sure nothing is omitted. Then, proceeding to update start_stations accordingly.

<details>
<summary>Code: Update query on main table</summary>

```sql
-- update by ID first
UPDATE combined_yearly t1
JOIN distinct_ends_with_coords_v2 t2
ON t1.end_station_id = t2.end_station_id
SET 
t1.end_station_name = t2.end_station_name,
t1.end_lat = t2.end_lat,
t1.end_lng = t2.end_lng
WHERE t1.end_station_id IS NOT NULL;

-- then update by name
UPDATE combined_yearly t1
JOIN distinct_ends_with_coords_v2 t2
ON t1.end_station_name = t2.end_station_name
SET 
t1.end_station_id = t2.end_station_id,
t1.end_lat = t2.end_lat,
t1.end_lng = t2.end_lng
WHERE t1.end_station_name IS NOT NULL;
```

</details>

Double-checking interim results of merging.

<details>
<summary>Code: Comparing interim update results via comparing unique numbers</summary>

```sql
SELECT 
COUNT(DISTINCT end_station_name) AS distinct_names,
COUNT(DISTINCT end_station_id) AS distinct_ids,
COUNT(DISTINCT end_station_id, end_station_name) AS distinct_both
FROM combined_yearly
WHERE end_station_id IS NOT NULL and end_station_name IS NOT NULL;

SELECT
COUNT(DISTINCT end_station_name) AS distinct_names,
COUNT(DISTINCT end_station_id) AS distinct_ids,
COUNT(DISTINCT end_station_id, end_station_name) AS distinct_both
FROM distinct_ends_with_coords_v2
WHERE end_station_id IS NOT NULL and end_station_name IS NOT NULL;
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> Still minor misalignment exists and needs to be addressed.
</div>

Distinct counts in distinct_ends_with_coords_v2 table:

| distinct_names | distinct_ids | distinct_both |
|:--------------:|:------------:|:-------------:|
|      1609      |     1609     |      1609     |

<br>
Distinct counts in combined_yearly table:

| distinct_names | distinct_ids | distinct_both |
|:--------------:|:------------:|:-------------:|
|      1630      |     1629     |      1630     |

<br>

Possibly, some one or more update steps were missed which caused misalignment. That is exactly why conducting periodical double-checks is important.

Now it is required to identify what causes misalignment via LEFT JOIN query. Results of those saved into separate table for subsequent update.

<details>
<summary>Code: Identifying Misalignment</summary>

```sql
CREATE TEMPORARY TABLE idsincombined_butnotindistincs AS (
SELECT DISTINCT
t1.end_station_id,
t1.end_station_name,
t1.end_lat,
t1.end_lng
FROM combined_yearly t1
LEFT JOIN distinct_ends_with_coords_v2 t2
ON t1.end_station_id = t2.end_station_id
WHERE t1.end_station_id IS NOT NULL 
AND t2.end_station_id IS NULL);
```

</details>

<br>

Coincidentally returns 21 rows, which is same amount for names found in combined table, but not in distincts table. Those are potential for merge.

Now these are checked via coordinate match and it's clearly seen that ID & name are different, but very similar.

<details>
<summary>Object: Extract of the Left Join results</summary>

| end_station_id |           end_station_name          | end_lat |  end_lng | end_station_id |         end_station_name         | end_lat |  end_lng |
|:--------------:|:-----------------------------------:|:-------:|:--------:|:--------------:|:--------------------------------:|:-------:|:--------:|
|  TA1307000066  |        Damen Ave & Coulter St       | 41.8492 | -87.6756 |     PI00001    |       Damen Ave/Coulter St       | 41.8492 | -87.6756 |
|  KA1503000049  |            Rainbow Beach            | 41.7579 | -87.5494 | KA1503000049.1 |          Rainbow - Beach         | 41.7579 | -87.5494 |
|       378      |           Nordica & Medill          | 41.9223 |  -87.802 |      21378     |     Nordica Ave & Medill Ave     | 41.9223 |  -87.802 |
|       351      |    Mulligan Ave & Wellington Ave    | 41.9347 | -87.7844 |      21351     |   Wellington Ave & Mulligan Ave  | 41.9347 | -87.7844 |
|       356      |         Lavergne & Fullerton        |  41.924 | -87.7512 |      21356     |   Lavergne Ave & Fullerton Ave   |  41.924 | -87.7512 |
|       915      |  Public Rack - Laflin St & 51st St  | 41.8014 | -87.6621 |      1042      | Public Rack - Laflin St &51st ST | 41.8014 | -87.6621 |
|      1227      | Public Rack - Monticello & Lawrence | 41.9682 | -87.7195 |      23108     |   Monticello Ave & Lawrence Ave  | 41.9682 | -87.7195 |
|       349      |       Spaulding Ave & 63rd St       | 41.7792 | -87.7056 |      21349     |       Spaulding Ave & 63rd       | 41.7792 | -87.7056 |
|       359      |          Kilbourn & Belden          | 41.9224 |  -87.739 |      21359     |     Kilbourn Ave & Belden Ave    | 41.9224 |  -87.739 |
|      1269      |     Public Rack - Western & 79th    | 41.7508 | -87.6827 |      23131     |       Western Ave & 79th St      | 41.7508 | -87.6827 |

</details>

<br>

Therefore, update based on coordinate match is carried out.

<br>

<details>
<summary>Code: Update based on coordinate match</summary>
```sql
UPDATE combined_yearly t1
JOIN distinct_ends_with_coords_v2 t2
ON t1.end_lat = t2.end_lat
AND t2.end_lng = t2.end_lng
SET t1.end_station_id = t2.end_station_id,
t1.end_station_name = t2.end_station_name
WHERE (t1.end_station_id, t1.end_station_name, t1.end_lat, t1.end_lng) IN
(SELECT end_station_id, end_station_name, end_lat, end_lng FROM idsincombined_butnotindistincs);
```

</details>

<br>

And now there's a perfect match between Combined_yearly and Distincts.

### 5.3.7. Proceed to update start stations

Perform updates and unification similarly as for end_stations.

<details>
<summary>Code: Updating start stations</summary>

```sql
UPDATE combined_yearly t1
JOIN distinct_ends_with_coords_v2 t2
ON t1.start_station_id = t2.end_station_id
SET 
t1.start_station_name = t2.end_station_name,
t1.start_lat = t2.end_lat,
t1.start_lng = t2.end_lng
WHERE t1.start_station_id IS NOT NULL;
```

</details>

<br>

**Interim Result:** Interim count of start distincts after 1st iteration update as follows:

| distinct_names | distinct_ids | distinct_both | distinct_four |
|:--------------:|:------------:|:-------------:|:-------------:|
|      1622      |     1668     |      1672     |      2074     |

<br>

#### 5.3.7.1. Fixing leftover misalignment in start stations <!-- omit from toc -->

Similarly as with end stations, now identifying misalignment, unioning accordingly.

<details>
<summary>Code: Identify misaligment between start & end stations</summary>

```sql
SELECT DISTINCT
t1.start_station_id,
t1.start_station_name,
t1.start_lat,
t1.start_lng
FROM combined_yearly t1
LEFT JOIN distinct_ends_with_coords_v2 t2
ON t1.start_station_id = t2.end_station_id
WHERE t1.start_station_id IS NOT NULL 
AND t2.end_station_id IS NULL

UNION

SELECT DISTINCT
t1.start_station_id,
t1.start_station_name,
t1.start_lat,
t1.start_lng
FROM combined_yearly t1
LEFT JOIN distinct_ends_with_coords_v2 t2
ON t1.start_station_name = t2.end_station_name
WHERE t1.start_station_name IS NOT NULL 
AND t2.end_station_name IS NULL;
```

</details>

<br>

#### 5.3.7.2. Fixing misalignment on coordinates <!-- omit from toc -->

Since misaligment on coordinates is higher, matching on approximate (vs exact) coorindate proximity is viable.

<details>
<summary>Code: Fixing coordinate misalignment</summary>

```sql
UPDATE combined_yearly t1
JOIN distinct_ends_with_coords_v2 t2
ON ABS(t1.start_lat - t2.end_lat) <= 0.0005
AND ABS(t1.start_lng - t2.end_lng) <= 0.0005
SET 
t1.start_station_id = t2.end_station_id,
t1.start_station_name = t2.end_station_name,
t1.start_lat = t2.end_lat,
t1.start_lng = t2.end_lng
WHERE (t1.start_station_id, t1.start_station_name, t1.start_lat, t1.start_lng) IN (
SELECT start_station_id, start_station_name, start_lat, start_lng FROM startsincombined_notindistincts);
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> 4092 rows updated.
</div>

---

#### 5.3.7.3. Station Data Cleaning - Conclusion <!-- omit from toc -->

Eventually, managed to fully merge stations correctly. Each ID & Stations name are now correctly associated with just one Name & ID respectively, as well as with just one set of latitude and longitude.

<details>
<summary>Code: Checking NULL station data distribution over time</summary>

```sql
SELECT
YEAR(started_at) AS trip_year,
MONTH(started_at) AS month_num,
trip_month,
COUNT(*) AS trips_per_date
FROM combined_yearly
WHERE end_station_name IS NULL AND end_station_id IS NULL
GROUP BY trip_year, month_num, trip_month
ORDER BY trip_year, month_num;
```

</details>

<details>
<summary>Object: Query Output</summary>

| # trip_year | month_num | trip_month | trips_per_date |
|:-----------:|:---------:|:----------:|:--------------:|
|     2023    |     8     |   August   |     122416     |
|     2023    |     9     |  September |     104656     |
|     2023    |     10    |   October  |      87132     |
|     2023    |     11    |  November  |      56412     |
|     2023    |     12    |  December  |      36936     |
|     2024    |     1     |   January  |      20065     |
|     2024    |     2     |  February  |      24288     |
|     2024    |     3     |    March   |      45843     |
|     2024    |     4     |    April   |      76636     |
|     2024    |     5     |     May    |     109974     |
|     2024    |     6     |    June    |     144609     |
|     2024    |     7     |    July    |     135886     |

</details>

<br>

<div style="background-color: #f0f0f0; border-left: 5px solid #cccccc; color: #333333; padding: 10px; margin: 10px 0;">
<strong>Thought Process Behind:</strong> At the same time, not all NULL station value records were possible to be realiably merged. My assumption in this scenario is that we already know all stations in the network of Cyclistic.
Therefore, these NULL values cannot be associated with stations beyond what is otherwise mentioned, at least, once in either start or end station columns. We also know that NULL values in station names are spread evenly across analyzed period. This was confirmed by running the above query.
While it is an issue in data recording, it is not an anomaly. Therefore, we can disregard NULL values when assessing station-related statistics,
because it is fair to expect that those records would otherwise fit the overall pattern which we would establish using just sample of data, not entire population.

<br>

However, even if the assumption that NULL values cannot be associated with other stations (which we don't have in any other record) is wrong,
running analysis on geographic data points might be even more viable on multiples sets of coordinates grouped into areas, rather than on individual stations. In this case, NULL values in station name & ID would not pose an obstacle too. However, clustering by area is not performed within this project. 
</div>

Overall conclusion is that station data as we have it now, with NULL values disregarded, is relevant and valid for analysis, and can be safely taken into consideration.

# 6. Process Phase: Data Transformation

## 6.1. Calculating & Populating Trip Duration

Creating additional column for duration and populating it with duration in seconds.

<details>
<summary>Code: Populate trip duration</summary>

```sql
ALTER TABLE combined_yearly
ADD COLUMN trip_duration INT;

UPDATE combined_yearly
SET trip_duration = TIMESTAMPDIFF(SECOND, started_at, ended_at);
```

</details>

## 6.2. Getting day, month, season, day_type

There is only 30356 records where trip start date differs from trip end date.
This is insignificant to the total. Thererfore, we can safely use 'started_at' as a base for extraction.

First, add respective columns.

<details>
<summary>Code: Add additional columns</summary>

```sql
ALTER TABLE combined_yearly
ADD trip_day VARCHAR(20),
ADD trip_month VARCHAR(20),
ADD trip_season VARCHAR(20),
ADD day_type VARCHAR(20);
```

</details>

### 6.2.1. Extract & Populate Day & Month

Populating new columns with relevant data. Running in separate queries and batching by 'mrowid' to balance load.

<details>
<summary>Code: Populating day & month</summary>

```sql
UPDATE combined_yearly
SET trip_day = DAYNAME(started_at)
WHERE mrowid >= 2500000;

UPDATE combined_yearly 
SET trip_month = MONTHNAME(started_at)
WHERE mrowid >= 2500000;
```

</details>

### 6.2.2. Populate season from Month column

Running in batches segmented by 'mrowid' to balance load.

<details>
<summary>Code: Populating season</summary>

```sql
UPDATE combined_yearly
SET trip_season = 
    CASE
        WHEN trip_month IN ('July', 'June', 'August') THEN 'Summer'
        WHEN trip_month IN ('September', 'October', 'November') THEN 'Autumn'
        WHEN trip_month IN ('December', 'January', 'February') THEN 'Winter'
        WHEN trip_month IN ('March', 'April', 'May') THEN 'Spring'
        ELSE 'Unknown'
    END
WHERE mrowid >= 2000000;
```

</details>

### 6.2.3. Populate day_type from day column (weekend/weekday)

<details>
<summary>Code: Populating day type</summary>

```sql
UPDATE combined_yearly
SET day_type = CASE
    WHEN combined_yearly.trip_day IN ('Saturday', 'Sunday') THEN 'Weekend'
    ELSE 'Weekday'
    END
WHERE mrowid > 10000;
```

</details>

### 6.2.4. Extracting hour of the day when trip started

Similarly, using started_at timestamp to extract hour of the day when trip started.

<details>
<summary>Code: Extracting and populating trip start hour</summary>

```sql
ALTER TABLE combined_yearly
ADD COLUMN start_hour INT; 

UPDATE combined_yearly
SET start_hour = HOUR(started_at);
```

</details>

<br>

# 7. Analyze Phase

## 7.1. Trip duration stats - high level Preview

Run key statistics check to understand the data better.

<details>
<summary>Code: Key trip duration statistics</summary>

```sql
-- run overall and for each usertype
SELECT
ROUND(AVG(trip_duration), 0) AS mean_duration,
MAX(trip_duration) AS max_duration,
MIN(trip_duration) AS min_duration,
ROUND(STDDEV(trip_duration), 0) AS stdv
FROM combined_yearly;

-- Calculate also median. 

SELECT
AVG(trip_duration) AS median_duration
FROM (
    SELECT trip_duration, 
    ROW_NUMBER() OVER (ORDER BY trip_duration) AS row_num, -- partition by usertype for user-specific stats
    COUNT(*) OVER () AS total_rows
    FROM combined_yearly
    WHERE trip_duration > 60 -- remove super short trips (likely erroneous)
) AS derived
WHERE row_num IN (FLOOR((total_rows + 1)/2), CEIL((total_rows + 1)/2));
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> Following are the obtained trip duration stats.
</div>

| usertype | median_duration | mean |   max   | min | std.dev |
|:--------:|:------------:|:----:|:-------:|:---:|:-------:|
|  casual  |      732     | 1628 | 5909344 |  0  |  13836  |
|  member  |      524     |  780 |  93588  |  0  |   2321  |
|   both   |      585     | 1082 | 5909344 |  0  |   8479  |

<br>

It is clear that there are significant outliers which need to be removed before carrying out trip duration analysis further.

## 7.2. Trips per Usertype

<details>
<summary>Code: Key trip duration statistics</summary>

```sql
SELECT
member_casual,
COUNT(*) trips_per_usertype
FROM combined_yearly
GROUP BY member_casual;
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> following is the trip count per usertype. 
</div>

| member_casual | trips_per_usertype |
|:-------------:|:------------------:|
|     casual    |       2038211      |
|     member    |       3676785      |
|     total     |       5714996      |

### 7.2.1. Trips less than 60 seconds

Check how many trips are shorter than 60 seconds. These are likely erroneous or bike rented by mistake.

<details>
<summary>Code: Check short trips</summary>

```sql
SELECT
COUNT(*) AS short_trips
FROM combined_yearly
WHERE trip_duration < 60; 
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> There are 130773 trips less than a minute long, which likely indicates cases where bike was rented by mistake or user changed their mind and did not proceed with the trip. It makes sense to filter out these trips from analysis.
</div>

## 7.3. Group trips by duration ranges

We saw outliers in trip duration earlier and expect to see it reflect here as well.
First, calculate standard 4 equal quartile ditribution as per below query:

<details>
<summary>Code: Standard trips per quartile view</summary>

```sql
WITH duration_ranges AS (
    SELECT 
    trip_duration,
    COUNT(*) OVER() AS total_trips,
    PERCENT_RANK() OVER (ORDER BY trip_duration) AS percentile
    FROM combined_yearly
    WHERE trip_duration >= 60
),
quartiles AS (
    SELECT
    trip_duration,
    CASE 
        WHEN percentile <= 0.25 THEN '0-25%'
        WHEN percentile <= 0.50 THEN '26-50%'
        WHEN percentile <= 0.75 THEN '51-75%'
        ELSE '76-100%'
    END AS quartile
    FROM duration_ranges
)
SELECT
quartile,
MIN(trip_duration) AS min_duration,
MAX(trip_duration) AS max_duration,
ROUND(STDDEV(trip_duration), 0) AS spread,
COUNT(*) AS trips_in_quartile
FROM quartiles
GROUP BY quartile
ORDER BY quartile; 
```

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> MAX duration indicate outliers. Same can be noticed from
the abnormally big spread of the data within 4th quartile. Splitting last quartile into smaller sub-ranges should yield insight of sensible cut-off point to remove outliers.
</div>

| # quartile | min_duration | max_duration | spread | trips_in_quartile |
|:----------:|:------------:|:------------:|:------:|:-----------------:|
|    0-25%   |      60      |      349     |   73   |      1399392      |
|   26-50%   |      350     |      599     |   72   |      1393950      |
|   51-75%   |      600     |     1055     |   129  |      1395190      |
|   76-100%  |     1056     |    <u>**5909344**</u>   |  <u>**17020**</u> |      1395691      |

### 7.3.1. Splitting further into Sub-Ranges to determine sensible outlier cut-off point

<details>
<summary>Code: Further splitting last quartile</summary>

```sql

WITH duration_ranges AS (
    SELECT 
    trip_duration,
    COUNT(*) OVER() AS total_trips,
    PERCENT_RANK() OVER (ORDER BY trip_duration) AS percentile
    FROM combined_yearly
    WHERE trip_duration >= 1056 --adjusted duration filter to lower limit of last quartile from previous query
),
quartiles AS (
    SELECT
    trip_duration,
    CASE 
        WHEN percentile <= 0.25 THEN '0-25%'
        WHEN percentile <= 0.50 THEN '26-50%'
        WHEN percentile <= 0.75 THEN '51-75%'
        WHEN percentile <= 0.85 THEN '76-85%'
        WHEN percentile <= 0.95 THEN '86-95%'
        WHEN percentile <= 0.97 THEN '96-97%'
        WHEN percentile <= 0.98 THEN '97-98%'
        WHEN percentile <= 0.99 THEN '98-99%'
        ELSE '99-100%'
    END AS quartile
    FROM duration_ranges
)
SELECT
quartile,
MIN(trip_duration) AS min_duration,
MAX(trip_duration) AS max_duration,
ROUND(STDDEV(trip_duration), 0) AS spread,
COUNT(*) AS trips_in_quartile
FROM quartiles
GROUP BY quartile
ORDER BY quartile; 
```

</details>

<br>

Above query detailed breakdown of the last quartile.

<details>
<summary>Object: Query output preview</summary>

| # quartile | min_duration | max_duration | spread | trips_in_quartile |
|:----------:|:------------:|:------------:|:------:|:-----------------:|
|    0-25%   |     6219     |     7319     |   314  |       13969       |
|   26-50%   |     7320     |     9418     |   605  |       13951       |
|   51-75%   |     9419     |     21089    |  2865  |       13959       |
|   76-85%   |     21092    |     78332    |  16683 |        5584       |
|   86-95%   |     78347    |     89994    |  2029  |        6275       |
|   96-97%   |     89995    |     89995    |    0   |        1028       |
|   98-99%   |     89996    |     89996    |    0   |        563        |
|   99-100%  |     89997    |    5909344   | 694529 |        510        |

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> Clear outlier seems to be at 210989. Delete rows where trip_duration > 21089. This results in 13960 row(s) deleted.
</div>

<details>
<summary>Code: Delete Outliers. First iteration</summary>

```sql
DELETE FROM combined_yearly
WHERE trip_duration > 21089;
```

</details>

<br>

Now run second iteration of last percentile breakdown for more precision.

<details>
<summary>Object: Query output preview</summary>

| # quartile | min_duration | max_duration | spread | trips_in_quartile |
|:----------:|:------------:|:------------:|:------:|:-----------------:|
|    0-25%   |     6219     |     6989     |   221  |       10475       |
|   26-50%   |     6990     |     8175     |   342  |       10474       |
|   51-75%   |     8176     |     10243    |   593  |       10461       |
|   76-85%   |     10244    |     11823    |   451  |        4187       |
|   86-95%   |     11824    |     15809    |  1128  |        4188       |
|   96-97%   |     15810    |     17376    |   443  |        837        |
|   97-98%   |     17378    |     18367    |   276  |        420        |
|   98-99%   |     18375    |     19555    |   353  |        418        |
|   99-100%  |     19565    |     21089    |   449  |        419        |

</details>

<br>

<div style="background-color: #4caf50; border-left: 5px solid #388e3c; color: #ffffff; padding: 10px; margin: 10px 0;">
<strong>Result:</strong> Further outlier picked to be at 105465. Proceed to delete rows where trip_duration > 10244. This results in 10465 row(s) deleted.
</div>

<details>
<summary>Code: Delete Outliers: 2nd iteration</summary>>

```sql
DELETE FROM combined_yearly
WHERE trip_duration > 10244;
```

</details>

<br>

**Interim Conclusion:** Now original breakdown by standard 4 quartiles provides more sensible data segmentation.
However, still, arguably could be split further.

| # quartile | min_duration | max_duration | spread | trips_in_quartile |
|:----------:|:------------:|:------------:|:------:|:-----------------:|
|    0-25%   |      60      |      348     |   72   |      1392804      |
|   26-50%   |      349     |      597     |   71   |      1391449      |
|   51-75%   |      598     |     1046     |   127  |      1386993      |
|   76-100%  |     1047     |     10244    |  1291  |      1388552      |

### 7.3.2. Quartiles per usertype

After outliers have been cleaned, running quartile calculation also per each usertype.

**Casual:**

<details>
<summary>Object: Casual: duration quartiles</summary>

| # quartile | min_duration | max_duration | spread | trips_in_quartile |
|:----------:|:------------:|:------------:|:------:|:-----------------:|
|    0-25%   |      60      |      427     |   92   |       491744      |
|   26-50%   |      428     |      747     |   92   |       491678      |
|   51-75%   |      748     |     1378     |   179  |       490599      |
|   76-100%  |     1379     |     10244    |  1634  |       491202      |

</details>

<br>

**Member:**

<details>
<summary>Object: Casual: duration quartiles</summary>

| # quartile | min_duration | max_duration | spread | trips_in_quartile |
|:----------:|:------------:|:------------:|:------:|:-----------------:|
|    0-25%   |      60      |      317     |   64   |       899790      |
|   26-50%   |      318     |      534     |   62   |       900991      |
|   51-75%   |      535     |      901     |   104  |       895434      |
|   76-100%  |      902     |     10243    |   795  |       898360      |

</details>

<br>

## 7.4. Trip duration stats revisited

It makes to revisit first-glance trip duration stats after removing outliers. As well as expand
trip duration analysis beyond general stats and factor by usertype, season and day of the week.

### 7.4.1. Core overall trip duration stats

Core stats after cleaning outliers.

<details>
<summary>Code: Key Stats query</summary>

```sql
SELECT
ROUND(AVG(trip_duration), 0) AS mean_duration,
MAX(trip_duration) AS max_duration,
MIN(trip_duration) AS min_duration,
ROUND(STDDEV(trip_duration), 0) AS stdv
FROM combined_yearly;
-- WHERE member_casual = 'casual' / 'member'
```

</details>

### 7.4.2. Calculating Median trip_duration

By usertype, day, season, month, day type and combinations of each.

<details>
<summary>Code: Median calculation</summary>

```sql
WITH median_calculation AS (
    SELECT 
    trip_duration,
    trip_day,
    member_casual,
    ROW_NUMBER() OVER (PARTITION BY trip_day, member_casual ORDER BY trip_duration) AS row_num,
    --PARTITION BY usertype and/or trip_day and/or trip_season and/or day_type
    COUNT(*) OVER (PARTITION BY trip_day, member_casual) AS total_rows
    FROM combined_yearly
    WHERE trip_duration > 60
    -- remove super short trips (likely erroneous)
)
SELECT
member_casual,
trip_day,
AVG(trip_duration) AS median_duration
FROM median_calculation
WHERE row_num IN (FLOOR((total_rows + 1)/2), CEIL((total_rows + 1)/2))
GROUP BY trip_day, member_casual
ORDER BY median_duration;
```

</details>

<br>

### 7.4.3. Mean & Total Trips

By usertype, day, season, month, day type and combinations of each.

<details>
<summary>Code: Mean & Total trips calculation</summary>

```sql
WITH mean_calculation AS (
    SELECT 
    trip_duration,
    trip_day,
    ROW_NUMBER() OVER (PARTITION BY trip_day ORDER BY trip_duration) AS row_num, --PARTITION BY member_casual and/or trip_day and/or trip_season
    COUNT(*) OVER (PARTITION BY trip_day) AS total_trips
    FROM combined_yearly
    WHERE trip_duration > 60
)
SELECT 
trip_day,
member_casual,
AVG(trip_duration) AS median_duration,
total_trips
FROM mean_calculation
GROUP BY trip_day
ORDER BY median_duration;
```

</details>

## 7.5. Combined Result of trip duration stats queries

Mean, Median and Total trips:

<details>
<summary>Per Usertype</summary>

| usertype | mean_duration | median_duration | max_duration | min_duration | stdv |
|:--------:|:-------------:|:---------------:|:------------:|:------------:|:----:|
|  casual  |      1143     |       747       |     10244    |       0      | 1283 |
|  member  |      705      |       534       |     10243    |       0      |  648 |
|   both   |      860      |       597       |     10244    |       0      |  948 |

</details>

<details>
<summary>Per Day</summary>

|  trip_day | mean_duration | median_duration | total_trips |
|:---------:|:-------------:|:---------------:|:-----------:|
|   Monday  |      823      |       555       |    711310   |
|  Tuesday  |      787      |       555       |    797936   |
|  Thursday |      790      |       557       |    810658   |
| Wednesday |      798      |       566       |    859179   |
|   Friday  |      864      |       587       |    794956   |
|   Sunday  |      1054     |       692       |    726558   |
|  Saturday |      1050     |       702       |    857999   |

</details>

<details>
<summary>Per Month</summary>

| trip_month | mean_duration | median_duration | total_trips |
|:----------:|:-------------:|:---------------:|:-----------:|
|    April   |      854      |       565       |    403250   |
|   August   |      937      |       652       |    749219   |
|  December  |      659      |       470       |    218507   |
|  February  |      720      |       502       |    218194   |
|   January  |      614      |       444       |    139670   |
|    July    |      998      |       676       |    728663   |
|    June    |      985      |       679       |    689578   |
|    March   |      772      |       523       |    293995   |
|     May    |      967      |       647       |    591066   |
|  November  |      701      |       494       |    353969   |
|   October  |      792      |       543       |    523307   |
|  September |      913      |       622       |    649178   |

</details>

<details>
<summary>Per Season</summary>

| # trip_season | mean_duration | median_duration | total_trips |
|:-------------:|:-------------:|:---------------:|:-----------:|
|     Autumn    |      822      |       562       |   1526454   |
|     Spring    |      887      |       590       |   1288311   |
|     Summer    |      973      |       668       |   2167460   |
|     Winter    |      671      |       475       |    576371   |

</details>

<br>

<details>
<summary>Per usertype & day</summary>

| member_casual |  trip_day | mean_duration | median_duration | total_trips |
|:-------------:|:---------:|:-------------:|:---------------:|:-----------:|
|     casual    |   Friday  |      1140     |       729       |    289238   |
|     casual    |   Monday  |      1137     |       698       |    218216   |
|     casual    |  Saturday |      1338     |       879       |    398677   |
|     casual    |   Sunday  |      1363     |       879       |    327538   |
|     casual    |  Thursday |      1021     |       658       |    246815   |
|     casual    |  Tuesday  |      1027     |       655       |    227306   |
|     casual    | Wednesday |      1031     |       670       |    256961   |
|     member    |   Friday  |      707      |       524       |    505718   |
|     member    |   Monday  |      684      |       507       |    493094   |
|     member    |  Saturday |      799      |       585       |    459322   |
|     member    |   Sunday  |      799      |       576       |    399020   |
|     member    |  Thursday |      688      |       520       |    563843   |
|     member    |  Tuesday  |      692      |       521       |    570630   |
|     member    | Wednesday |      699      |       529       |    602218   |

</details>

<details>
<summary>Per usertype & season</summary>

| # member_casual | trip_season | mean_duration | median_duration | total_trips |
|:---------------:|:-----------:|:-------------:|:---------------:|:-----------:|
|      casual     |    Autumn   |      1077     |       677       |    520799   |
|      casual     |    Spring   |      1230     |       768       |    428385   |
|      casual     |    Summer   |      1251     |       818       |    896056   |
|      casual     |    Winter   |      835      |       526       |    119511   |
|      member     |    Autumn   |      690      |       514       |   1005655   |
|      member     |    Spring   |      716      |       527       |    859926   |
|      member     |    Summer   |      777      |       585       |   1271404   |
|      member     |    Winter   |      628      |       463       |    456860   |

</details>

<details>
<summary>Per usertype & month</summary>

| member_casual | trip_month | mean_duration | median_duration | total_trips |
|:-------------:|:----------:|:-------------:|:---------------:|:-----------:|
|     casual    |    April   |      1197     |       732       |    126881   |
|     casual    |   August   |      1189     |       785       |    300676   |
|     casual    |  December  |      794      |       514       |    50208    |
|     casual    |  February  |      954      |       584       |    45780    |
|     casual    |   January  |      694      |       459       |    23523    |
|     casual    |    July    |      1299     |       840       |    307368   |
|     casual    |    June    |      1264     |       831       |    288012   |
|     casual    |    March   |      1074     |       664       |    79901    |
|     casual    |     May    |      1305     |       830       |    221603   |
|     casual    |  November  |      878      |       560       |    95636    |
|     casual    |   October  |      1038     |       646       |    171524   |
|     casual    |  September |      1179     |       751       |    253639   |
|     member    |    April   |      697      |       510       |    276369   |
|     member    |   August   |      769      |       580       |    448543   |
|     member    |  December  |      618      |       458       |    168299   |
|     member    |  February  |      658      |       484       |    172414   |
|     member    |   January  |      597      |       441       |    116147   |
|     member    |    July    |      778      |       584       |    421295   |
|     member    |    June    |      784      |       592       |    401566   |
|     member    |    March   |      659      |       484       |    214094   |
|     member    |     May    |      764      |       567       |    369463   |
|     member    |  November  |      635      |       472       |    258333   |
|     member    |   October  |      673      |       502       |    351783   |
|     member    |  September |      742      |       555       |    395539   |

</details>

~~**Per month & day**~~ / ~~**Per month & season**~~ -> Unnecessary for analysis

<details>
<summary>Per season & day</summary>

| trip_season |  trip_day | mean_duration | median_duration | total_trips |
|:-----------:|:---------:|:-------------:|:---------------:|:-----------:|
|    Autumn   |   Friday  |      800      |       551       |    220962   |
|    Autumn   |   Monday  |      779      |       526       |    196038   |
|    Autumn   |  Saturday |      982      |       653       |    240436   |
|    Autumn   |   Sunday  |      997      |       654       |    207572   |
|    Autumn   |  Thursday |      727      |       528       |    223603   |
|    Autumn   |  Tuesday  |      733      |       522       |    215537   |
|    Autumn   | Wednesday |      730      |       528       |    222306   |
|    Spring   |   Friday  |      835      |       563       |    185104   |
|    Spring   |   Monday  |      853      |       562       |    175577   |
|    Spring   |  Saturday |     1,090     |       719       |    214969   |
|    Spring   |   Sunday  |     1,089     |       694       |    164810   |
|    Spring   |  Thursday |      759      |       534       |    173656   |
|    Spring   |  Tuesday  |      774      |       543       |    181761   |
|    Spring   | Wednesday |      791      |       556       |    192434   |
|    Summer   |   Friday  |      983      |       672       |    306018   |
|    Summer   |   Monday  |      885      |       607       |    259246   |
|    Summer   |  Saturday |     1,134     |       776       |    339783   |
|    Summer   |   Sunday  |     1,134     |       765       |    294515   |
|    Summer   |  Thursday |      902      |       633       |    309024   |
|    Summer   |  Tuesday  |      867      |       612       |    312993   |
|    Summer   | Wednesday |      891      |       635       |    345881   |
|    Winter   |   Friday  |      665      |       471       |    82872    |
|    Winter   |   Monday  |      665      |       465       |    80449    |
|    Winter   |  Saturday |      714      |       496       |    62811    |
|    Winter   |   Sunday  |      753      |       507       |    59661    |
|    Winter   |  Thursday |      642      |       469       |    104375   |
|    Winter   |  Tuesday  |      664      |       473       |    87645    |
|    Winter   | Wednesday |      641      |       466       |    98558    |

</details>

<details>
<summary>Per season, day & usertype</summary>

| member_casual |  trip_day | trip_season | mean_duration | median_duration | total_trips |
|:-------------:|:---------:|:-----------:|:-------------:|:---------------:|:-----------:|
|     casual    |   Friday  |    Autumn   |      1023     |       654       |    76524    |
|     casual    |   Monday  |    Autumn   |      1062     |       639       |    59378    |
|     casual    |  Saturday |    Autumn   |      1250     |       802       |    108255   |
|     casual    |   Sunday  |    Autumn   |      1280     |       818       |    92695    |
|     casual    |  Thursday |    Autumn   |      884      |       587       |    63963    |
|     casual    |  Tuesday  |    Autumn   |      917      |       590       |    58752    |
|     casual    | Wednesday |    Autumn   |      899      |       588       |    61232    |
|     casual    |   Friday  |    Spring   |      1137     |       713       |    61095    |
|     casual    |   Monday  |    Spring   |      1230     |       751       |    52546    |
|     casual    |  Saturday |    Spring   |      1422     |       937       |    97214    |
|     casual    |   Sunday  |    Spring   |      1461     |       932       |    71715    |
|     casual    |  Thursday |    Spring   |      997      |       629       |    46779    |
|     casual    |  Tuesday  |    Spring   |      1030     |       650       |    47191    |
|     casual    | Wednesday |    Spring   |      1050     |       662       |    51845    |
|     casual    |   Friday  |    Summer   |      1253     |       822       |    133098   |
|     casual    |   Monday  |    Summer   |      1175     |       745       |    91294    |
|     casual    |  Saturday |    Summer   |      1389     |       934       |    176443   |
|     casual    |   Sunday  |    Summer   |      1412     |       930       |    146723   |
|     casual    |  Thursday |    Summer   |      1153     |       755       |    116558   |
|     casual    |  Tuesday  |    Summer   |      1115     |       725       |    105543   |
|     casual    | Wednesday |    Summer   |      1125     |       753       |    126397   |
|     casual    |   Friday  |    Winter   |      818      |       528       |    18521    |
|     casual    |   Monday  |    Winter   |      873      |       516       |    14998    |
|     casual    |  Saturday |    Winter   |      884      |       570       |    16765    |
|     casual    |   Sunday  |    Winter   |      969      |       611       |    16405    |
|     casual    |  Thursday |    Winter   |      743      |       492       |    19515    |
|     casual    |  Tuesday  |    Winter   |      831      |       507       |    15820    |
|     casual    | Wednesday |    Winter   |      757      |       485       |    17487    |
|     member    |   Friday  |    Autumn   |      682      |       506       |    144438   |
|     member    |   Monday  |    Autumn   |      656      |       487       |    136660   |
|     member    |  Saturday |    Autumn   |      762      |       558       |    132181   |
|     member    |   Sunday  |    Autumn   |      769      |       552       |    114877   |
|     member    |  Thursday |    Autumn   |      663      |       505       |    159640   |
|     member    |  Tuesday  |    Autumn   |      664      |       501       |    156785   |
|     member    | Wednesday |    Autumn   |      666      |       507       |    161074   |
|     member    |   Friday  |    Spring   |      686      |       507       |    124009   |
|     member    |   Monday  |    Spring   |      692      |       507       |    123031   |
|     member    |  Saturday |    Spring   |      816      |       591       |    117755   |
|     member    |   Sunday  |    Spring   |      802      |       567       |    93095    |
|     member    |  Thursday |    Spring   |      671      |       505       |    126877   |
|     member    |  Tuesday  |    Spring   |      684      |       512       |    134570   |
|     member    | Wednesday |    Spring   |      695      |       524       |    140589   |
|     member    |   Friday  |    Summer   |      775      |       583       |    172920   |
|     member    |   Monday  |    Summer   |      727      |       549       |    167952   |
|     member    |  Saturday |    Summer   |      858      |       643       |    163340   |
|     member    |   Sunday  |    Summer   |      859      |       635       |    147792   |
|     member    |  Thursday |    Summer   |      751      |       573       |    192466   |
|     member    |  Tuesday  |    Summer   |      740      |       564       |    207450   |
|     member    | Wednesday |    Summer   |      756      |       578       |    219484   |
|     member    |   Friday  |    Winter   |      620      |       456       |    64351    |
|     member    |   Monday  |    Winter   |      617      |       454       |    65451    |
|     member    |  Saturday |    Winter   |      652      |       471       |    46046    |
|     member    |   Sunday  |    Winter   |      671      |       474       |    43256    |
|     member    |  Thursday |    Winter   |      619      |       463       |    84860    |
|     member    |  Tuesday  |    Winter   |      627      |       466       |    71825    |
|     member    | Wednesday |    Winter   |      616      |       462       |    81071    |

</details>

~~**Per Season, Month, Usertype**~~ / ~~**Per Season, Month, Day**~~ ~~**Per Day, Month, Usertype**~~ --> Unnecessary for analysis

## 7.6. Trip count per at each trip start hout

Run below query to calculate trip count per each start hour.
To factor by usertype, day, day type and season, add respective field combinations to GROUP BY clause.

<details>
<summary>Code: Trip Count per start hour</summary>

```sql
SELECT
start_hour,
trip_day,
trip_season,
COUNT(*) AS started_trips
FROM combined_yearly
WHERE trip_duration > 60
GROUP BY start_hour, trip_day, trip_season
ORDER BY trip_season, trip_day, started_trips DESC;

-- and with percentage of total
SELECT
start_hour,
COUNT(*) AS started_trips,
CONCAT(ROUND((COUNT(*) / SUM(COUNT(*)) OVER ()) * 100, 1),'%') AS percentage_of_total
FROM combined_yearly
WHERE trip_duration > 60
GROUP BY start_hour
ORDER BY start_hour;
```

</details>

## 7.7. Trips per stations analysis

Most popular/busy station. Inbound & Outbound combined. First create most popular overall in separate temporary table,
then partition by usertype.

<details>
<summary>Code: Identifying most popular stations</summary>

```sql
-- most popular station overall
CREATE TEMPORARY TABLE stations AS (
    SELECT
    usertype,
    station_name,
    station_id,
    latitude,
    longitude,
    SUM(trips_per_station_usertype) AS total_trips
    FROM (
        SELECT
        member_casual AS usertype,
        start_station_name AS station_name,
        start_station_id AS station_id,
        start_lat AS latitude,
        start_lng AS longitude,
        COUNT(*) AS trips_per_station_usertype
        FROM combined_yearly
        WHERE trip_duration > 60 AND start_station_id IS NOT NULL AND start_station_name IS NOT NULL
        GROUP BY usertype, station_name, station_id, latitude, longitude

    UNION ALL


    SELECT
    member_casual AS usertype,
    end_station_name AS station_name,
    end_station_id AS station_id,
    end_lat AS latitude,
    end_lng AS longitude,
    COUNT(*) AS trips_per_station_usertype
    FROM combined_yearly
    WHERE trip_duration > 60 AND end_station_id IS NOT NULL AND end_station_name IS NOT NULL 
    GROUP BY usertype, station_name, station_id, latitude, longitude
    ORDER BY trips_per_station_usertype DESC
    LIMIT 150
    ) AS interimstations
GROUP BY usertype, station_id, station_name, latitude, longitude
ORDER BY total_trips DESC);
```

</details>

<br>

Now return TOP 10 per usertype.

<details>
<summary>Code: TOP 10 stations per usertype</summary>

```sql
SELECT
usertype, 
station_name,
latitude,
longitude,
total_trips,
ranking
FROM (
    SELECT
    usertype, 
    station_name,
    latitude,
    longitude,
    total_trips,
    RANK() OVER(PARTITION BY usertype ORDER BY total_trips DESC) AS ranking
    FROM stations) AS derived
    WHERE ranking <= 10
    ORDER BY usertype;
```

</details>

<br>

## 7.8. Most popular routes analysis (station pairs)

Combinations of start & end stations.

<details>
<summary>Code: Identifying popular station pairs</summary>

```sql
CREATE TEMPORARY TABLE popular_routes AS (
SELECT
start_station_name,
end_station_name,
COUNT(*) AS trips_per_route,
AVG(trip_duration) as mean_duration
FROM combined_yearly
WHERE start_station_name IS NOT NULL AND end_station_name IS NOT NULL AND trip_duration > 60
GROUP BY start_station_name, end_station_name
ORDER BY trips_per_route DESC
LIMIT 30);
```

```sql
CREATE TEMPORARY TABLE popular_route_trips AS (
SELECT
trip_duration,
member_casual,
start_station_name,
end_station_name
FROM combined_yearly
WHERE trip_duration > 60 AND (start_station_name, end_station_name) IN (SELECT start_station_name, end_station_name FROM popular_routes));
```

### 7.8.1. Calculate stats for popular route trips

**Median:**

```sql
WITH median_calculation AS (
    SELECT 
    trip_duration,
    member_casual,
    start_station_name,
    end_station_name,
    ROW_NUMBER() OVER (PARTITION BY member_casual, start_station_name, end_station_name ORDER BY trip_duration) AS row_num,
    COUNT(*) OVER (PARTITION BY member_casual, start_station_name, end_station_name) AS total_rows
    FROM popular_route_trips
)
SELECT
member_casual,
start_station_name,
end_station_name,
total_rows AS trips_per_route,
ROUND(AVG(trip_duration), 0) AS median_duration
FROM median_calculation
WHERE row_num IN (FLOOR((total_rows + 1)/2), CEIL((total_rows + 1)/2))
GROUP BY member_casual, start_station_name, end_station_name
ORDER BY member_casual, trips_per_route DESC;
```

**Mean:**

With & without usertype (member_casual)

```sql
WITH mean_calculation AS (
    SELECT 
    member_casual,
    trip_duration,
    start_station_name,
    end_station_name,
    COUNT(*) OVER (PARTITION BY member_casual, start_station_name, end_station_name) AS total_rows
    FROM popular_route_trips
)
SELECT
member_casual,
start_station_name,
end_station_name,
total_rows AS trips_per_route,
ROUND(AVG(trip_duration), 0) AS mean_duration
FROM mean_calculation
GROUP BY member_casual, start_station_name, end_station_name
ORDER BY member_casual, trips_per_route DESC;
```

</<details>
