## Big Data Analytics with Google Big Query - Lyft Bay Wheels Bike Share Analysis
---
An analysis on Bike Share utilizing <b>Google Big Query</b> and <i>SQL</i>.

As an aspiring data scientist and an avid bike rider, I am interested in bike share utilization and will to provide
an analysis and data-driven recommendation to Lyft's current membership plans and offers. Data was sourced via
publicly available datasets on [Google Big Query](https://cloud.google.com/bigquery/). Currently based out of the Bay
Area in Northern California, I will focus on Lyft Bike data in the <b>San Francisco Bay Area</b>.
  
## Dataset Access
---
BigQuery: https://cloud.google.com/bigquery/

Public Datasets: Bring up your Google BigQuery console, open the menu for the public datasets, and navigate to the the dataset san_francisco.

- The Bay Bike Share has two datasets: a static one and a dynamic one.  The static one covers an historic period of about 3 years (8/29/2013 - 8/31/2016)
  and contains <b>983,648 trips</b>.  
  The dynamic one updates every 10 minutes or so.  I will be using the static Bay Bike Share dataset to use <i>SQL</i> against a static target 
  instead of a moving target.

- The static tables I will be using in this project are in the dataset **san_francisco** :

  * bikeshare_stations

  * bikeshare_status

  * bikeshare_trips
  
## Goal: Provide a Data Driven Recommendations to Increase Lyft Bike Ridership
---
Currently, Lyft Bikes offers the below membership/payment offers:
  * Single Ride 
  * Monthly Membership
  * Annual Membership
  * Bike Share for All
  * Access Pass
  * Corporate Membership

Through data exploration, cleaning or filtering "dirty" data, etc., I will provide final queries that will serve as the supporting 
data for my recommendations. You can make any recommendations regarding the offers, including, but not limited to: 
  * market offers differently to generate more revenue 
  * remove offers that are not working 
  * modify exising offers to generate more revenue
  * create new offers for hidden business opportunities you have found
  * etc. 
  
## Project Results Notebook
---
In this repo:
  * `bikeshare_analysis.ipynb` - full report with results presented as a Jupyter Notebook in this repo
  * `EDA-queries.md` - Markdown file containing SQL query code and answers to basic EDA questions, 
      both in Google BigQuery SQL and as CL commands
  * various `.csv` files exported from query results
