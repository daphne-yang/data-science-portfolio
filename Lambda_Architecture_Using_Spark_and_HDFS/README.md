# Lambda Architechture Using Spark and HDFS: Tracking User Activity

In this project for my Data Engineering class, I am assuming the role at an ed tech firm. I've created a service that
delivers assessments, and now lots of different customers (e.g., Pearson) want
to publish their assessments on it. I need to get ready for data scientists
who work for these customers to run queries on the data. 

## Tasks

Prepare the infrastructure to land the data in the form and structure it needs
to be to be queried.  I will:

- Use Docker and Docker-Compose
- Publish and consume messages with Kafka
- Use Spark to transform the messages 
- Use Spark to transform the messages so that you can land them in HDFS

I will run Spark through a jupyter notebook containing a report that describes my queries and spark SQL to answer business questions
that I've select. 

## Data

To get access to the data, run 
```
curl -L -o assessment-attempts-20180128-121051-nested.json https://goo.gl/ME6hjp`
```

## Files in This Repo

- One example of your docker-compose.yml files
- Jupyter notebook containing both spark commands and the CLI commands that I have run to publish and consume messages and launch the notebook
