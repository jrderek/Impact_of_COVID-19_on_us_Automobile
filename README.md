# Impact_of_COVID-19_on_us_Automobile

Project Summary

A Automotive Telematics startup, Global Telematics, has grown their traffic data and want to move their processes and data onto the cloud.
Their data resides in S3, in a directory of CSV files representing daily automotive accidents in U.S, as well as a directory with daily U.S COVID-19 data at county level.

As their data engineer, you are tasked with building an ETL pipeline that extracts their data from S3, stages them in Redshift, and transforms data into a set of dimensional tables for their Analytics team to continue finding insights.
Due to a pandemic that started in January 2020, the Analytics Team wants to ingest COVID-19 data to understand how the lockdown in U.S impacted automotive accidents.

Project Structure


│   README.md                    # Project description

│   requirements.txt             # Python dependencies

│   docker-compose.yml           # Docker Containers Configuration

└───airflow                      # Airflow home
|   |
│   └───dags                       # Airflow DAGs location

│   |   │ create_datalake_dag.py   # DAG definition

│   |   │ us_accidents_etl_dag.py  # DAG definition
|   |   |
|   └───plugins
│       │
|       └───helpers
|       |   | sql_creates.py  # Sql queries to create objects

|       |   | sql_load.py     # Sql queries to load data into sql tables
|       |
|       └───operators
|       |   | check_s3_file_count.py # CheckS3FileCount

|       |   | create_s3_bucket.py    # CreateS3BucketOperator

|       |   | upload_files_to_s3.py  # UploadFilesToS3Operator

|       |   | data_quality.py        # DataQualityOperator

|       |   | load_table.py          # LoadTableOperator

|       |   | s3_to_redshift.py      # S3ToRedshiftOperator
|___data-profiling
│   | covid-19.html
│   | us-accidents.html
│   | us-cities-demographics.html
|___data
|   |
│   └───raw                          # Raw data is saved here

│   |   └─── covid-19                # Covid-19 data set directory

│   |   └─── us-accidents            # us-accidents data set directory

│   |   └─── us-cities-demographics  # us-cities-demographics data set directory

│   └───split                        # Split data is saved here automatically (Directories are created automatically as well)
|___images
|   | datalake_dag_graph_view.png     # Datalake DAG Graph View

|   | datalake_dag_trigger.png        # Datalake DAG Trigger

|   | accident_elt_dag_graph_view.png # Accident ETL DAG Graph View

|   | accident_elt_dag_trigger.png    # Accident ETL DAG Trigger

|   | data-model.png                  # Redshift Entity Relationship Diagram
|___src
|   | create_resources.py # Script to create resources required

|   | delete_resources.py # Script to delete resources created

|   | split_data.py       # Script to split large datasets

|   | dwh.cfg             # Configuration file

Requirements for running locally

Install Python3

Install Docker

Install Docker Compose

AWS Account

Step 1: Scope the Project

Scope

The idea is to create a data lake and a DataWarehouse on AWS, enabling users to analyze accidents data and extract insights.

The main goal of this project is to build an end-to-end data pipeline which is capable to work with big volumes of data.


Technologies

The datasets will be cleaned, enriched to add more information and will be loaded into a data warehouse for this we will use following tools & technologies:

1. AWS S3:

We are going to store the raw data on Amazon S3, which is is an object storage service that offers industry-leading scalability, availability and durability.

2. AWS Redshift:

Considering the current data size, we are going to use Amazon Redshift to ingest the data from S3 and perform the ETL process, denormalizing the datasets to create FACT and DIMENSION tables.

3. Apache Airflow:

We are going to build a data pipeline using Apache Airflow to orchestrate everything Airflow provides an intuitive UI where we can track the progress and bottlenecks of our pipelines.
4. Python:

Python is a easy to use programming language as it supports wide variety of libraries to perform tasks such as creation of pipelines as well as data analysis.
5. Juypter Notebooks:

Open-source web application that allows you to create and share documents that contain live code, visualizations etc. Uses include: data cleaning and transformation, data visualization, machine learning, and much more.
Describe and Gather Data
We are going to work with 3 datasets:

U.S Accidents

This is a countrywide traffic accident dataset, which covers 49 states of the United States.
U.S Cities: Demographics

This dataset contains information about the demographics of all US cities and census-designated places with a population greater or equal to 65,000.
U.S COVID-19
This dataset contains information about COVID-19 cases in US at county level.

Step 2: Explore and Assess the Data
Navigate to "data-profiling" and open the comprehensive Data Profiling for each dataset.

US Accidents

US Cities: Demographics

US COVID-19

Based on these reports, I was able to define a set of rules to clean and model the data accordingly.

Step 3: Define the Data Model
After reviewing and cleaning all the datasets, it was defined the need of two types of tables.

Staging tables: This can be used for preprocessing the data before loading into the main Data Warehousing tables.

Datawarehouse tables: This will be the main Fact & Dimension tables

Staging Tables
• staging.us_demographics - Provides demographics information about US cities
• staging.us_accidents - Provides the accident details
• staging.us_covid_19 - Provides daily information about COVID-19 in US
Click here to see the Staging Tables Schema
Datawarehouse Tables
The dimensional model consists of seven tables.
The details and relationship between them are listed below:

Fact Tables
• fact.us_demographics - Provides demographics information about US cities
• fact.us_accidents - Provides the accident details
• fact.us_covid_19 - Provides daily information about COVID-19 in US
Dimension Tables
• dim.accident_address - Provides a unique list addresses based on accidents
• dim.accident_location_details - Provides the accident location details based on the accident address surroundings
• dim.weather_condition - Provides the weather condition at the time of the accident
• dim.dates - Provides timestamps of records broken down into specific units
Datawarehouse - Entity Relationship Diagram
Click here to see the Datawarehouse Tables Schema
Step 4: Run ETL to Model the Data
Clone repository to local machine
git clone https://github.com/drobim-data-engineering/Capstone-Project-Data-Engineering-Nanodegree.git
Change directory to local repository
cd Capstone-Project-Data-Engineering-Nanodegree
Create python virtual environment
python3 -m venv venv             # create virtualenv
source venv/bin/activate         # activate virtualenv
pip install -r requirements.txt  # install requirements (this can take couple of minutes)
Download data
Download and save the data as followed below:

U.S Accidents
data/raw/us-accidents/
U.S Cities: Demographics
data/raw/us-cities-demographics/
U.S COVID-19
data/raw/covid-19/
Split the data
The script below goes through the directory input by the user and splits the data.

cd src/
python -m split_data.py # Split the data into chunks
The split data sets are saved on data/split/

Edit dwh.cfg file
This file holds the configuration variables used on the scripts to create and configure the AWS resources.
These are the variables the user needs to set up before running the create_resources.py script.

AWS_ACCESS_KEY = <ENTER AWS ACCESS KEY>   # paste your user Access Key
AWS_SECRET_ACCESS_KEY = <ENTER AWS SECRET KEY>  # paste your user Secret Key
REGION = <ENTER THE AWS REGION> # paste the AWS Region to create resources
VPC_ID = <ENTER VPC ID>  # paste the VPC_ID you want to create the resources (If blank the first VPC on user's AWS account is considered)
REMEMBER: Never share your AWS ACCESS KEY & SECRET KEY on scripts.
This is just an experiment to get familiarized with AWS SDK for Python.

Start Airflow Container
Everything is configured in the docker-compose.yml file.

cd ..
docker-compose up
Create AWS Resources
This is the entry point to kick-off a series of processes from creating resources on AWS to creating custom connections on Airflow.

cd src/
python -m create_resources.py # Creates required resources
The execution of this script incur REAL MONEY costs so be aware of that.

Start "Create Datalake" DAG
  
Navigate to "airflow/dag/create_datalake_dag" and set the variable "datalake_bucket_name", the default is 'us-accidents-datalake'. However, this might crash as S3 Bucket names has to be unique.
Visit the Airflow UI and start the "create_datalake_dag" by switching it state from OFF to ON.
Refresh the page and click on the "trigger dag" button.
  
  ![image](https://user-images.githubusercontent.com/96236642/162977772-6589a855-27fb-485e-ba4f-4aca73ae4140.png)


datalake_dag_trigger

Finally, click on "create_datalake_dag" and then on "Graph View" to view the current DAG state.
This pipeline creates the S3 bucket for our data lake and uploads the files from local machine.
Wait until the pipeline has successfully completed (it should take around 15-20 minutes).
  
  ![image](https://user-images.githubusercontent.com/96236642/162977683-1f812479-6b4c-44d6-96ca-fc0f0f0b005f.png)

 

datalake_dag_state

Start "Accident ETL" DAG
  
Go back to Airflow home page and start the "us_accident_etl_dag" by switching it state from OFF to ON.
Refresh the page and click on the "trigger dag" button.
  
  ![image](https://user-images.githubusercontent.com/96236642/162977306-f626b899-2b96-40e8-ac41-267f4ae57338.png)


accident_etl_dag_trigger

Finally, click on "us_accident_etl_dag" and then on "Graph View" to view the current DAG state.
  
This pipeline creates the dimensional model we are going to use for the analysis, it creates database schema, dimension and fact tables on Redshift and loads data from S3 to Redshift
  
Wait until the pipeline has successfully completed.
  
  ![image](https://user-images.githubusercontent.com/96236642/162977235-d60a332c-e0c2-4428-b034-4e7c0d835f5f.png)


accident_etl_dag_graph_view

Exploring the Data
Run the code below on you default shell

jupyter notebook  # launch jupyter notebook app
The notebook interface will appear in a new browser window or tab.
Navigate to src/analytics.ipynb and run some queries.
  
Have Fun!

Delete Resources
  
Please make sure to run the script below once the process is completed.

cd src/
python -m delete_resources.py # Entry point to kick-off a series of processes to delete resources resources on AWS and connections on Airflow.


Step 5: Complete Project Write Up
  
I think most has already been said. Now that this dataset is available on AWS Redshift, Data Scientists and Data Analysts can take over and create AI models and dashboards. New data can be processed as it arrives, Airflow can be used to track and visualize the progress.

Due to uncertainty about the data update frequency, The pipeline will be executed on demand, which makes sense as long as new data is published. The user can easily schedule the DAG to reflect a monthly run for instance (schedule_interval='@monthly').

Addressing Other Scenarios
  
The data was increased by 100x
Data Lake

The data lake would not require significant changes since we have a flexible schema and S3 is meant for storing big data.
ETL Process

Regarding the ETL job, it would require moving to a EMR Cluster on AWS running Apache Spark, which is optimize for Big Data Processing.
Orchestration

The Airflow container is currently running on a single container on our local machine. In a production environment, Airflow would be running on a cluster of machines likely coordinated by Kubernetes.
The pipelines would be run on a daily basis by 7 am every day.

We can schedule our Airflow pipelines so that they follow this pattern.
Airflow will store useful statistics regarding job status and we can easily spot faults in the pipeline.
The database needed to be accessed by 100+ people.

The more people accessing the database the more CPU resources you need to get a fast experience. By using a distributed database you can improve your replications and partitioning to get faster query results for each user. If the load is getting to high it would make sense to scale the database horizontally by adding more nodes to bring down the load for each individual node.
