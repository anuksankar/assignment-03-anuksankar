# template-activity-03

Edited by: Anu Sankar

# Query Project

- In the Query Project, you will get practice with SQL while learning about
  Google Cloud Platform (GCP) and BiqQuery. You'll answer business-driven
  questions using public datasets housed in GCP. To give you experience with
  different ways to use those datasets, you will use the web UI (BiqQuery) and
  the command-line tools, and work with them in jupyter notebooks.

- We will be using the Bay Area Bike Share Trips Data
  (https://cloud.google.com/bigquery/public-data/bay-bike-share). 

#### Problem Statement
- You're a data scientist at Ford GoBike (https://www.fordgobike.com/), the
  company running Bay Area Bikeshare. You are trying to increase ridership, and
  you want to offer deals through the mobile app to do so. What deals do you
  offer though? Currently, your company has three options: a flat price for a
  single one-way trip, a day pass that allows unlimited 30-minute rides for 24
  hours and an annual membership. 

- Through this project, you will answer these questions: 
  * What are the 5 most popular trips that you would call "commuter trips"?
  * What are your recommendations for offers (justify based on your findings)?


## Assignment 03 - Querying data from the BigQuery CLI - set up 

### What is Google Cloud SDK?
- Read: https://cloud.google.com/sdk/docs/overview

- If you want to go further, https://cloud.google.com/sdk/docs/concepts has
  lots of good stuff.

### Get Going

- Install Google Cloud SDK: https://cloud.google.com/sdk/docs/

- Try BQ from the command line:

  * General query structure

    ```
    bq query --use_legacy_sql=false '
        SELECT count(*)
        FROM
           `bigquery-public-data.san_francisco.bikeshare_trips`'
    ```

### Queries

1. Rerun last week's queries using bq command line tool (Paste your bq
   queries):

- What's the size of this dataset? (i.e., how many trips)

```
 bq query --use_legacy_sql=false '
     SELECT count(*) 
     FROM 
         `bigquery-public-data.san_francisco.bikeshare_trips`'
```


|  f0_   |
|--------|
| 983648 |


- What is the earliest start time and latest end time for a trip?

```
bq query --use_legacy_sql=false '
    SELECT
       MIN(TIME(start_date)) as early_start,
       MAX(TIME(end_date)) as late_end
    FROM
       `bigquery-public-data.san_francisco.bikeshare_trips`'
```

| early_start | late_end |
|-------------|----------|
|    00:00:00 | 23:59:00 |


- How many bikes are there?

```
 bq query --use_legacy_sql=false '
     SELECT count(distinct(bike_number)) 
     FROM 
        `bigquery-public-data.san_francisco.bikeshare_trips`'
```

| f0_ |
|-----|
| 700 |



2. New Query (Paste your SQL query and answer the question in a sentence):

- How many trips are in the morning vs in the afternOON	

```
 bq query --use_legacy_sql=false '
 SELECT
   "Morning" AS trip_hour,
   COUNT(*) as num_trips
 FROM
   `bigquery-public-data.san_francisco.bikeshare_trips`
 WHERE
   EXTRACT(HOUR
   FROM
     start_date) < 12
 GROUP BY
   trip_hour UNION ALL
 SELECT
   "Evening" AS trip_hour,
   COUNT(*) as num_trips
 FROM
   `bigquery-public-data.san_francisco.bikeshare_trips`
 WHERE
   EXTRACT(HOUR
   FROM
     start_date) >= 12
 GROUP BY
   trip_hour
 ORDER BY trip_hour DESC'
```

| trip_hour | num_trips |
|-----------|-----------|
| Morning   |    412339 |
| Evening   |    571309 |



### Project Questions
Identify the main questions you'll need to answer to make recommendations (list
below, add as many questions as you need).

- Question 1: What are the top 5 popular trips? 

- Question 2: What are the different values for subscriber_type? How many trips do each type of subscriber make by year?

- Question 3: What are the popular commuter trips (subscriber only )?

- Question 4: What is the  bike utilization?

- Question 5: How many docks are available in the stations that have the most popular commuter trips?



- ...

- Question n: 

### Answers

Answer at least 4 of the questions you identified above You can use either
BigQuery or the bq command line tool.  Paste your questions, queries and
answers below.

- Question 1: What are the top 5 popular trips? 

  * Answer:

|Row	|start_station_name	                   |end_station_name	                  |num_trips |
|-------|------------------------------------------|--------------------------------------|----------|	 
|1	|Harry Bridges Plaza (Ferry Building)	   |Embarcadero at Sansome	          |9150	     |
|-------|------------------------------------------|--------------------------------------|----------|
|2	|San Francisco Caltrain 2 (330 Townsend)   |Townsend at 7th	                  |8508	     |
|-------|------------------------------------------|--------------------------------------|----------| 
|3	|2nd at Townsend	                   |Harry Bridges Plaza (Ferry Building)  |7620	     |
|-------|------------------------------------------|--------------------------------------|----------| 
|4	|Harry Bridges Plaza (Ferry Building)	   |2nd at Townsend	                  |6888	     |
|-------|------------------------------------------|--------------------------------------|----------| 
|5	|Embarcadero at Sansome	                   |Steuart at Market	                  |6874      |


  * SQL query:
```
#standardSQL
SELECT
  start_station_name,
  end_station_name,
  count(*) as num_trips
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`
GROUP BY
   start_station_name,
   end_station_name
ORDER BY 
   num_trips DESC
LIMIT 5
```


- Question 2:What are the different values for subscriber_type? How many trips do each type of subscriber make by year?
  * Answer:

|Row	|subscriber_type	|Yr	|num_trips	| 
|-------|-----------------------|-------|---------------|
|1	|Customer		|2013	|24499	 	|
|-------|-----------------------|-------|---------------|
|2	|Customer		|2014	|48576	 	|
|-------|-----------------------|-------|---------------|
|3	|Customer		|2015	|40530	 	|
|-------|-----------------------|-------|---------------|
|4	|Customer		|2016	|23204	 	|
|-------|-----------------------|-------|---------------|
|5	|Subscriber		|2013	|76064	 	|
|-------|-----------------------|-------|---------------|
|6	|Subscriber		|2014	|277763	 	|
|-------|-----------------------|-------|---------------|
|7	|Subscriber		|2015	|305722	 	|
|-------|-----------------------|-------|---------------|
|8	|Subscriber		|2016	|187290	 	|


  * SQL query:
```
#standardSQL
SELECT
  subscriber_type,
  EXTRACT(YEAR FROM start_date) as Yr,
  COUNT(*) num_trips
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips` 
GROUP BY 
  subscriber_type,
  Yr
ORDER BY
  subscriber_type,
  Yr 
```


- Question 3: What are the popular commuter trips (subscriber only)?
  * Answer:

|Row    |start_station_name				|end_station_name			|num_trips	|
|-------|-----------------------------------------------|---------------------------------------|---------------|
|1	|San Francisco Caltrain 2 (330 Townsend)	|Townsend at 7th			|8305	 	|
|-------|-----------------------------------------------|---------------------------------------|---------------|
|2	|2nd at Townsend				|Harry Bridges Plaza (Ferry Building)	|6931	 	|
|-------|-----------------------------------------------|---------------------------------------|---------------|
|3	|Townsend at 7th				|San Francisco Caltrain 2 (330 Townsend)|6641	 	|
|-------|-----------------------------------------------|---------------------------------------|---------------|
|4	|Harry Bridges Plaza (Ferry Building)		|2nd at Townsend			|6332		|
|-------|-----------------------------------------------|---------------------------------------|---------------|
|5	|Embarcadero at Sansome				|Steuart at Market			|6200		|


  * SQL query:

```
#standardSQL
SELECT
  start_station_name,
  end_station_name,
  count(*) as num_trips
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`
WHERE 
   subscriber_type = 'Subscriber'
GROUP BY
   start_station_name,
   end_station_name
ORDER BY 
   num_trips DESC
LIMIT 5
```


- Question 4: What is the bike utilization?

  * Answer:

|Row	|start_station_name				|num_trips	|num_distinct_bikes	| 
|-------|-----------------------------------------------|---------------|-----------------------|
|1	|Yerba Buena Center of the Arts (3rd @ Howard)	|11817		|464	 		|
|-------|-----------------------------------------------|---------------|-----------------------|
|2	|Washington at Kearny				|5002		|433	 		|
|-------|-----------------------------------------------|---------------|-----------------------|
|3	|Washington at Kearney				|911		|317	 		|
|-------|-----------------------------------------------|---------------|-----------------------|
|4	|University and Emerson				|503		|210	 		|
|-------|-----------------------------------------------|---------------|-----------------------|
|5	|Townsend at 7th				|32788		|500			|


  * SQL query:

```
#standardSQL
SELECT
  start_station_name,
  count(*) as num_trips,
  count(distinct(bike_number)) as num_distinct_bikes
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`
WHERE
   subscriber_type = 'Subscriber'
GROUP BY
   start_station_name
ORDER BY 
   start_station_name DESC
LIMIT 5

```

- ...

- Question n:
  * Answer:
  * SQL query:
 
