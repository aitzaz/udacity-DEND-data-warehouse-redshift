# Project: Data Warehouse with Amazon Redshift

## Project Overview
A music streaming startup, Sparkify, has grown their user base and song database and want to move their processes and data onto the cloud. Their data resides in S3, in a directory of JSON logs on user activity on the app, as well as a directory with JSON metadata on the songs in their app.

## Project Description
This project builds an ETL pipeline that extracts Sparkify's data from S3, stages them in Redshift, and transforms data into a set of dimensional tables for their analytics team to continue finding insights in what songs their users are listening to.

## Run Guide
This code uses `python3`.

- Install `psycopg2` using pip.
- Update `dwh.cfg` with Redshift cluster related parameters and an IAM role having access to read S3.
- Run `create_tables.py` script to create tables on Redshift database configured in `dwh.cfg`.
- Run `etl.py` script to populate all tables.

## Data Model
Data model is divided into two set of tables. Staging tables are for loading raw data into Redshift. These tables are then used to populated analytics tables after applying transformations using SQL. 

### Staging Tables

**staging_log_events**: Loads log events data from `s3://udacity-dend/log_data` using `COPY` command. `song` column is used as `distkey`.

**staging_songs**: Loads songs data from `s3://udacity-dend/log_data` using `COPY` command. `title` column is used as `distkey` which is song title.

Both data sources contain JSON data.

### Analytics Tables (Star Schema)

**fact_songplays**: Fact table of the schema. `song_id` is chosen as `distkey` to distribute data across slices. `dim_songs` table also uses `song_id` as `distkey`, thus reducing data shuffling while joining these tables. Constraints are also added in table creation statement.

**dim_songs**: Songs dimensional table uses `song_id` as both `distkey` and `sortkey`.  

**dim_artists**: Artists dimensional table uses `diststyle all` and is sorted on `artist_id`.

**dim_users**: Users dimensional table uses `diststyle all` and is sorted on `user_id`.

**dim_time**: Time dimensional table uses `diststyle all` and is sorted on `start_time`.

## Description of Project Files 

**create_tables.py**: Drops tables if exists and then creates tables using queries defined in `sql_queries.py` module.

**dwh.cfg**: Contains Redshift database connection parameters, IAM role used by `COPY` command to access S3 data files and S3 data files paths.

**etl.py**: Runs queries defined in `sql_queries.py` module. First populates staging tables and then move data to analytics table by applying SQL based transformations.

**sql_queries.py**: Contains queries to created tables, copy data from S3 and then transform and insert data into analytics tables.


