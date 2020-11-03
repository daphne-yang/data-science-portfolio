# Exploratory Data Analysis Queries

Through this project, I ultimately want to answer these questions: 

  * What are the 5 most popular trips that I would call "commuter trips"?
      - Will require some feature engineering to define "commuter trip"
      - I defined a commuter trip as a trip satisfying all of the following requirements:  

1. a trip between the hours of 7 AM - 9 AM and 4PM - 6PM during the weekdays (Monday - Friday).  
    - This was determined according to a [trip advisor forum post](https://www.tripadvisor.com/ShowTopic-g60713-i30-k2240412-Rush_hour_in_SF-San_Francisco_California.html#:~:text=Typical%20rush%20hour%20times%20are,SFO%20on%20a%20Saturday%2C%20though.) denoting such times as rush hour in the Bay Area  

2. a trip not lasting a minimum of 2 minutes and not in excess of 60 minutes
    - This was determined according to a post by [mobilitylab.org](https://mobilitylab.org/2017/02/27/how-far-bike-work/) that stated a bike commute to work should be not in excess of 10 miles, roughly a 60 minute bike commute.
   
  * What are my recommendations for offers (presented in bikeshare_analysis.ipynb)?


I separated my project into 3 parts:

- In Part 1, I will query using the Google BigQuery GUI interface in the cloud.

- In Part 2, I will query using the Linux command line from our virtual machine in the cloud.

- In Part 3, I will query from a Jupyter Notebook in our virtual machine in the cloud, save the results into Pandas, and present a report enhanced by Pandas output tables and simple data visualizations using Seaborn / Matplotlib.

---

## Part 1 - Querying Data with BigQuery

### Some initial queries

Pasted my formatted SQL query and answered the question. 

- What's the size of this dataset? (i.e., how many trips)

There are 983,648 trips in the bikeshare_trips static table.

``` sql
SELECT
  COUNT(*) AS `Total_Trips`
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`;
```

- What is the earliest start date and time and latest end date and time for a trip?

The earliest start date for a trip was 9:08:00 AM on 8/29/2013 and the latest end date for a trip was 23:48:00PM on 8/31/2016.

```sql
SELECT
  MIN(start_date) AS Earliest_Start,
  MAX(end_date) AS Latest_End
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`;
```

- How many bikes are there?
There are 700 bikes.

```sql
SELECT
  COUNT(DISTINCT bike_number) AS Number_of_Bikes
FROM
  `bigquery-public-data.san_francisco.bikeshare_trips`;
```

- Have more people taken BayWheels during morning or evening commuter hours? (ie.  Are more trips started during morning or evening commuter hours?)
 (I defined Morning Commuter Hours 7 - 9AM; M through F, Evening Commuter Hours = 4 - 6PM; M through F) 
  * More individuals take BayWheels during evening commuter hours.
  * SQL query:

```sql
SELECT
  ampm_commute,
  COUNT(pst_trip_id) AS no_of_trips
FROM
  `bike_trip_data.trips_full_commute_pst`
WHERE
  ampm_commute IN ('AM',
    'PM')
GROUP BY
  ampm_commute
ORDER BY
  no_of_trips DESC;
```

- What Day are BayWheels trips most often started?
  * BayWheels usage has been historically highest on Tuesdays with 184,405 recorded trips. 
  
  * SQL query:
  
```sql
SELECT
  start_day_of_week,
  COUNT(DISTINCT pst_trip_id) AS no_of_trips
FROM
  `bike_trip_data.trips_full_commute_pst`
GROUP BY
  start_day_of_week
ORDER BY
  no_of_trips DESC
LIMIT
  1;
```


- What is the most popular start station amongst morning commuters? 
  * The most popular start station amongst morning commuters is San Francisco Caltrain (Townsend at 4th).
  * SQL query:

```sql
SELECT
  start_station_name
FROM (
  SELECT
    start_station_name,
    ampm_commute,
    COUNT(DISTINCT pst_trip_id) AS no_of_trips,
  FROM
    `bike_trip_data.trips_full_commute_pst`
  WHERE
    ampm_commute IN ('AM',
      'PM')
  GROUP BY
    ampm_commute,
    start_station_name
  ORDER BY
    no_of_trips DESC)
WHERE
  ampm_commute = 'AM'
LIMIT
  1;
```

  * How many trips are in the morning vs in the afternoon?

I defined Morning trips as trips that started at or after 04:00 PST and ended before 12:00 PST and defined Afternoon trips as trips that started at or after 12:00 PST and before 18:00 PST. There were 366,249 morning trips and 392,334 afernoon trips.  

```sql
--used an intermediate view `bike_trip_data.trips_full_commute_PST`
SELECT
  start_time_of_day,
  COUNT(DISTINCT pst_trip_id) AS no_of_trips
FROM
  `bike_trip_data.trips_full_commute_pst`
WHERE
  start_time_of_day = end_time_of_day
GROUP BY
  start_time_of_day
HAVING
  start_time_of_day = 'Morning'
  OR start_time_of_day = 'Afternoon'
ORDER BY
  start_time_of_day DESC;

```

```
output:
+-----------+-------------+
|    Row    | no_of_trips |
+-----------+-------------+
| Morning   |   392334    |
+-----------+-------------+
| Afternoon |   366249    |
+-----------+-------------+
```

## Part 2 - Querying data from the BigQuery CLI 

- Use BQ from the Linux command line:

  * General query structure

    ```
    bq query --use_legacy_sql=false '
        SELECT COUNT(*)
        FROM
           `bigquery-public-data.san_francisco.bikeshare_trips`'
    ```

### Queries

1. To practice my understanding, I rerun the first 3 queries from Part 1 using bq command line tool (I've pasted my BigQuery
   queries and results here, using properly formatted markdown):

  * What's the size of this dataset? (i.e., how many trips)

```sql
bq query --use_legacy_sql=false '
     SELECT COUNT(*) AS 'no_of_trips'
     FROM
         `bigquery-public-data.san_francisco.bikeshare_trips`'
```

```
+-------------+
| no_of_trips |
+-------------+
|   983648    |
+-------------+
```


  * What is the earliest start time and latest end time for a trip?
``` sql
bq query --use_legacy_sql=False '
     SELECT min(EXTRACT(TIME FROM start_date)) AS earliest_start_time, 
            max(EXTRACT(TIME FROM end_date)) AS latest_end_time
     FROM `bigquery-public-data.san_francisco.bikeshare_trips`'
```

```
+---------------------+-----------------+
| earliest_start_time | latest_end_time |
+---------------------+-----------------+
|            00:00:00 |        23:59:00 |
+---------------------+-----------------+
```

  * How many bikes are there?

```sql
bq query --use_legacy_sql=False '
      SELECT count(distinct bike_number) AS Number_of_Bikes 
      FROM `bigquery-public-data.san_francisco.bikeshare_trips`'
```

```
+-----------------+
| Number_of_Bikes |
+-----------------+
|             700 |
+-----------------+
```

### Project Questions
Below are some of the main questions (with corresponding BigQuery code and results) I might need to answer to make recommendations.

- Question 1: What days are the Lyft Bikes most busy?

  * Answer: I defined the event of being busy as the event that at any given status check there are less that 25% bikes available to be used. The Lyft Bikes were the most busy on Wednesdays with 2,174,624 instances of low bike availability. The Lyft Bikes were the least busy on Sundays with 1,464,488 instances of low bike availability. 

```
+-------------+-------------+
| day_of_week | max_count   |
+-------------+-------------+
| Wednesday   |   2174624   |
+-------------+-------------+

```

  * SQL query:
  
```sql
--Creating a new status_w_availability view
SELECT station_id, 
       DATE(time) AS status_check_date,
       TIME(time) AS status_check_time,
       CASE EXTRACT (DAYOFWEEK FROM DATE(time))
           WHEN 1 THEN 'Sunday'
           WHEN 2 THEN 'Monday'
           WHEN 3 THEN 'Tuesday'
           WHEN 4 THEN 'Wednesday'
           WHEN 5 THEN 'Thursday'
           WHEN 6 THEN 'Friday'
           WHEN 7 THEN 'Saturday'
           END AS status_check_day_of_week,
       docks_available, bikes_available,
       (docks_available + bikes_available) AS total_bikes,
       CASE
           WHEN bikes_available = 0 AND docks_available = 0 THEN 'NA'
           WHEN bikes_available / (docks_available + bikes_available) < 0.25 THEN 'low'
           ELSE 'high'
           END AS bike_availability,
FROM `bigquery-public-data.san_francisco.bikeshare_status`
```

Aggregating Counts of Low Availability:

```sql
-- new view: availability_agg
SELECT
  status_check_day_of_week AS day_of_week,
  COUNT(status_check_date) AS no_of_low_availability,
FROM `bike_trip_data.status_w_availability`
WHERE bike_availability = 'low'
GROUP BY status_check_day_of_week
```

Most Busy Day and Number of Low Availability Events:
``` sql
SELECT day_of_week, max_count
FROM `bike_trip_data.availability_agg` AS agg
RIGHT JOIN (
  SELECT MAX(no_of_low_availability) AS max_count
  FROM `september-2020-288123.bike_trip_data.availability_agg`) AS max
ON max.max_count = agg.no_of_low_availability
```


- Question 2: What days are the Lyft Bikes the least busy? 
  * Answer: I defined the event of being busy as the event that at any given status check there are less that 25% bikes available to be used. The Lyft Bikes were the least busy on Sundays with 1,464,488 instances of low bike availability. 
  
```
+-------------+-------------+
| day_of_week | min_count   |
+-------------+-------------+
|   Sunday    |   1464488   |
+-------------+-------------+
```
  * SQL query:
  
Least Busy Day and Number of Low Availability Events:

```sql
SELECT day_of_week, min_count
FROM `bike_trip_data.availability_agg` AS agg
RIGHT JOIN (
  SELECT MIN(no_of_low_availability) AS min_count
  FROM `september-2020-288123.bike_trip_data.availability_agg`) AS min
ON min.min_count = agg.no_of_low_availability
```

- Question 3: Does the season impact the overall volume of trips? 
  * Answer: Season does impact the overall volumn of trips because there were relatively similar numbers of trips durin the Summer, Fall, and Spring seasons (between 250,000 to 277,000 distinct trips in each season) with a large decrease in the Winter trip volume (199,734 distinct trips). 
``` 
+------------------+-------------+
| start_date_month | trip_volume |
+------------------+-------------+
| Summer           |      276787 |
| Fall             |      254790 |
| Spring           |      252337 |
| Winter           |      199734 |
+------------------+-------------+
```

  * SQL query:
  
```sql
SELECT start_date_month,
  COUNT(DISTINCT pst_trip_id) AS trip_volume
FROM `bike_trip_data.trips_seasons_year`
GROUP BY start_date_month
ORDER BY trip_volume DESC;
```
  
- Question 4: Is there an change in the Volume of trips taken by Subscribers between the first and last year?
  * Answer: There was an 17.6% increase in the percentage of trips in total that were taken by Subscribers between 2013 and 2016. There was a 54.8% decrease in the percentage of trips in total that were taken by Customers between 2013 and 2016.
  
```
+-----------------+---------------+
| subscriber_type | percent_diff  |
+-----------------+---------------+
| Subscriber      |    17.634388  |
| Customer.       |   -54.750581  |
+-----------------+---------------+
```
  
  * SQL query:
```sql
SELECT recent.subscriber_type, 
       100*(recent.percent_of_trips - old.percent_of_trips) / old.percent_of_trips AS percent_diff
FROM `bike_trip_data.percent_2016` AS recent
JOIN `bike_trip_data.percent_2013` AS old
ON recent.subscriber_type = old.subscriber_type
```
---

## Part 3 - Synthesizing Query Project Results

### Get Going

Created a Jupyter Notebook against a Python 3 kernel named `bikeshare_analysis.ipynb` in this repo.

#### Run queries in the notebook  

I can run queries using the "bang" command to shell out, such as this:

```
! bq query --use_legacy_sql=FALSE '<your-query-here>'
```

- NOTE: 
- Queries that return over 16K rows will not run this way, 
- Run groupbys etc in the bq web interface and save that as a table in BQ. 
- Max rows is defaulted to 100, use the command line parameter `--max_rows=1000000` to make it larger
- Query those tables the same way as in `example.ipynb`

Or I can use the magic commands, such as this:

```sql
%%bigquery my_panda_data_frame

select start_station_name, end_station_name
from `bigquery-public-data.san_francisco.bikeshare_trips`
where start_station_name <> end_station_name
limit 10
```

And then call the data frame in a Python Kernel Jupyter Notebook like this: 

```python
my_panda_data_frame
```

** Note: I chose to run queries using the "bang" command to shell out.


