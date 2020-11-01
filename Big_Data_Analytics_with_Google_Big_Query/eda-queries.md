# Project 1: Query Project

- In the Query Project, you will get practice with SQL while learning about
  Google Cloud Platform (GCP) and BiqQuery. You'll answer business-driven
  questions using public datasets housed in GCP. To give you experience with
  different ways to use those datasets, you will use the web UI (BiqQuery) and
  the command-line tools, and work with them in Jupyter Notebooks.

#### Problem Statement

- You're a data scientist at Lyft Bay Wheels (https://www.lyft.com/bikes/bay-wheels), formerly known as Ford GoBike, the
  company running Bay Area Bikeshare. You are trying to increase ridership, and
  you want to offer deals through the mobile app to do so. 
  
- What deals do you offer though? Currently, your company has several options which can change over time.  Please visit the website to see the current offers and other marketing information. Frequent offers include: 
  * Single Ride 
  * Monthly Membership
  * Annual Membership
  * Bike Share for All
  * Access Pass
  * Corporate Membership
  * etc.

- Through this project, you will answer these questions: 

  * What are the 5 most popular trips that you would call "commuter trips"? 
   
  * What are your recommendations for offers (justify based on your findings)?

- Please note that there are no exact answers to the above questions, just like in the proverbial real world.  This is not a simple exercise where each question above will have a simple SQL query. It is an exercise in analytics over inexact and dirty data. 

- You won't find a column in a table labeled "commuter trip".  You will find you need to do quite a bit of data exploration using SQL queries to determine your own definition of a communter trip.  In data exploration process, you will find a lot of dirty data, that you will need to either clean or filter out. You will then write SQL queries to find the communter trips.

- Likewise to make your recommendations, you will need to do data exploration, cleaning or filtering dirty data, etc. to come up with the final queries that will give you the supporting data for your recommendations. You can make any recommendations regarding the offers, including, but not limited to: 
  * market offers differently to generate more revenue 
  * remove offers that are not working 
  * modify exising offers to generate more revenue
  * create new offers for hidden business opportunities you have found
  * etc. 

#### All Work MUST be done in the Google Cloud Platform (GCP) / The Majority of Work MUST be done using BigQuery SQL / Usage of Temporary Tables, Views, Pandas, Data Visualizations

A couple of the goals of w205 are for students to learn how to work in a cloud environment (such as GCP) and how to use SQL against a big data data platform (such as Google BigQuery).  In keeping with these goals, please do all of your work in GCP, and the majority of your analytics work using BigQuery SQL queries.

You can make intermediate temporary tables or views in your own dataset in BigQuery as you like.  Actually, this is a great way to work!  These make data exploration much easier.  It's much easier when you have made temporary tables or views with only clean data, filtered rows, filtered columns, new columns, summary data, etc.  If you use intermediate temporary tables or views, you should include the SQL used to create these, along with a brief note mentioning that you used the temporary table or view.

In the final Jupyter Notebook, the results of your BigQuery SQL will be read into Pandas, where you will use the skills you learned in the Python class to print formatted Pandas tables, simple data visualizations using Seaborn / Matplotlib, etc.  You can use Pandas for simple transformations, but please remember the bulk of work should be done using Google BigQuery SQL.

#### GitHub Procedures

In your Python class you used GitHub, with a single repo for all assignments, where you committed without doing a pull request.  In this class, we will try to mimic the real world more closely, so our procedures will be enhanced. 

Each project, including this one, will have it's own repo.

Important:  In w205, please never merge your assignment branch to the master branch. 

Using the git command line: clone down the repo, leave the master branch untouched, create an assignment branch, and move to that branch:
- Open a linux command line to your virtual machine and be sure you are logged in as jupyter.
- Create a ~/w205 directory if it does not already exist `mkdir ~/w205`
- Change directory into the ~/w205 directory `cd ~/w205`
- Clone down your repo `git clone <https url for your repo>`
- Change directory into the repo `cd <repo name>`
- Create an assignment branch `git branch assignment`
- Checkout the assignment branch `git checkout assignment`

The previous steps only need to be done once.  Once you your clone is on the assignment branch it will remain on that branch unless you checkout another branch.

The project workflow follows this pattern, which may be repeated as many times as needed.  In fact it's best to do this frequently as it saves your work into GitHub in case your virtual machine becomes corrupt:
- Make changes to existing files as needed.
- Add new files as needed
- Stage modified files `git add <filename>`
- Commit staged files `git commit -m "<meaningful comment about your changes>"`
- Push the commit on your assignment branch from your clone to GitHub `git push origin assignment`

Once you are done, go to the GitHub web interface and create a pull request comparing the assignment branch to the master branch.  Add your instructor, and only your instructor, as the reviewer.  The date and time stamp of the pull request is considered the submission time for late penalties. 

If you decide to make more changes after you have created a pull request, you can simply close the pull request (without merge!), make more changes, stage, commit, push, and create a final pull request when you are done.  Note that the last data and time stamp of the last pull request will be considered the submission time for late penalties.

---

## Parts 1, 2, 3

We have broken down this project into 3 parts, about 1 week's work each to help you stay on track.

**You will only turn in the project once  at the end of part 3!**

- In Part 1, we will query using the Google BigQuery GUI interface in the cloud.

- In Part 2, we will query using the Linux command line from our virtual machine in the cloud.

- In Part 3, we will query from a Jupyter Notebook in our virtual machine in the cloud, save the results into Pandas, and present a report enhanced by Pandas output tables and simple data visualizations using Seaborn / Matplotlib.

---

## Part 1 - Querying Data with BigQuery

### SQL Tutorial

Please go through this SQL tutorial to help you learn the basics of SQL to help you complete this project.

SQL tutorial: https://www.w3schools.com/sql/default.asp

### Google Cloud Helpful Links

Read: https://cloud.google.com/docs/overview/

BigQuery: https://cloud.google.com/bigquery/

Public Datasets: Bring up your Google BigQuery console, open the menu for the public datasets, and navigate to the the dataset san_francisco.

- The Bay Bike Share has two datasets: a static one and a dynamic one.  The static one covers an historic period of about 3 years.  The dynamic one updates every 10 minutes or so.  THE STATIC ONE IS THE ONE WE WILL USE IN CLASS AND IN THE PROJECT. The reason is that is much easier to learn SQL against a static target instead of a moving target.

- (USE THESE TABLES!) The static tables we will be using in this class are in the dataset **san_francisco** :

  * bikeshare_stations

  * bikeshare_status

  * bikeshare_trips

- The dynamic tables are found in the dataset **san_francisco_bikeshare**

### Some initial queries

Paste your SQL query and answer the question in a sentence.  Be sure you properly format your queries and results using markdown. 

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


### Questions of your own
- Make up 3 questions and answer them using the Bay Area Bike Share Trips Data.  These questions MUST be different than any of the questions and queries you ran above.

- Question 1: Have more people taken BayWheels during morning or evening commuter hours? (ie.  Are more trips started during morning or evening commuter hours?)
 (I defined Morning Commuter Hours 7 - 9AM; M through F, Evening Commuter Hours = 4 - 6PM; M through F) 
  * Answer: More individuals take BayWheels during evening commuter hours.
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

- Question 2: What Day are BayWheels trips most often started?
  * Answer: BayWheels usage has been historically highest on Tuesdays with 184,405 recorded trips. 
  
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


- Question 3: What is the most popular start station amongst morning commuters? 
  * Answer: The most popular start station amongst morning commuters is San Francisco Caltrain (Townsend at 4th).
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

### Bonus activity queries (optional - not graded - just this section is optional, all other sections are required)

The bike share dynamic dataset offers multiple tables that can be joined to learn more interesting facts about the bike share business across all regions. These advanced queries are designed to challenge you to explore the other tables, using only the available metadata to create views that give you a broader understanding of the overall volumes across the regions(each region has multiple stations)

We can create a temporary table or view against the dynamic dataset to join to our static dataset.

Here is some SQL to pull the region_id and station_id from the dynamic dataset.  You can save the results of this query to a temporary table or view.  You can then join the static tables to this table or view to find the region:
```sql
#standardSQL
select distinct region_id, station_id
from `bigquery-public-data.san_francisco_bikeshare.bikeshare_station_info`
```

- Top 5 popular station pairs in each region

- Top 3 most popular regions(stations belong within 1 region)

- Total trips for each short station name in each region

- What are the top 10 used bikes in each of the top 3 region. these bikes could be in need of more frequent maintenance.

---

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

1. Rerun the first 3 queries from Part 1 using bq command line tool (Paste your bq
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

2. New Query (Run using bq and paste your SQL query and answer the question in a sentence, using properly formatted markdown):

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

### Project Questions
Identify the main questions you'll need to answer to make recommendations (list
below, add as many questions as you need).

- Question 1: What days are the Lyft Bikes the most busy?

- Question 2: What days are the Lyft Bikes the least busy?

- Question 3: Does the season impact the overall volume of riders? 

- Question 4: Is there an increase in Subscription Volume vs Customer Volume year over year?

- Question 5: For every day of the week, what interval of time are there very few bikes available to ride? 

- Question 6: Has there been a trend in the frequency of rides per day over the years 2013 to 2016?

- Question 7: Which city from the data set are LyftBikes most likely to be used?

### Answers

Answer at least 4 of the questions you identified above You can use either
BigQuery or the bq command line tool.  Paste your questions, queries and
answers below.

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

## Part 3 - Employ notebooks to synthesize query project results

### Get Going

Create a Jupyter Notebook against a Python 3 kernel named Project_1.ipynb in the assignment branch of your repo.

#### Run queries in the notebook 

At the end of this document is an example Jupyter Notebook you can take a look at and run.  

You can run queries using the "bang" command to shell out, such as this:

```
! bq query --use_legacy_sql=FALSE '<your-query-here>'
```

- NOTE: 
- Queries that return over 16K rows will not run this way, 
- Run groupbys etc in the bq web interface and save that as a table in BQ. 
- Max rows is defaulted to 100, use the command line parameter `--max_rows=1000000` to make it larger
- Query those tables the same way as in `example.ipynb`

Or you can use the magic commands, such as this:

```sql
%%bigquery my_panda_data_frame

select start_station_name, end_station_name
from `bigquery-public-data.san_francisco.bikeshare_trips`
where start_station_name <> end_station_name
limit 10
```

```python
my_panda_data_frame
```

#### Report in the form of the Jupter Notebook named Project_1.ipynb

- Using markdown cells, MUST definitively state and answer the two project questions:

  * What are the 5 most popular trips that you would call "commuter trips"? 
  
  * What are your recommendations for offers (justify based on your findings)?

- For any temporary tables (or views) that you created, include the SQL in markdown cells

- Use code cells for SQL you ran to load into Pandas, either using the !bq or the magic commands

- Use code cells to create Pandas formatted output tables (at least 3) to present or support your findings

- Use code cells to create simple data visualizations using Seaborn / Matplotlib (at least 2) to present or support your findings

### Resource: see example .ipynb file 

[Example Notebook](example.ipynb)

