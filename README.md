# Udacity Data Engineering Nanodegree Program
## Project 3: Data Warehouse

### Project Summary
This project, as part of the Udacity Data Engineering Nanodegree, focusses on DWH (Data warehouses).

In this project, skills learned in this course on  data warehouses and AWS are applied to build an ETL pipeline for a database hosted on Redshift. Data is loaded from S3 (AWS data storage) to staging tables on Redshift (AWS DWH). Then, SQL statements are executed that create the analytics tables from these staging tables.

## IMPORTANT NOTE:
It is important to stress that credentials / AWS access keys in `dwh.cfg` are NEVER shared or saved when publishing / committing this file.

### Background
A music streaming startup, Sparkify, has grown their user base and song database and want to move their processes and data onto the cloud. Their data resides in S3, in a directory of JSON logs on user activity on the app, as well as a directory with JSON metadata on the songs in their app.

To this goal, an ETL pipeline is build that extracts their data from S3, stages them in Redshift, and transforms data into a set of dimensional tables for their analytics team to continue finding insights in what songs their users are listening to.

### Datasets
#### Song dataset
The first dataset is a subset of real data from the Million Song Dataset. Each file is in JSON format and contains metadata about a song and the artist of that song. The files are partitioned by the first three letters of each song's track ID. For example, here are filepaths to two files in this dataset.

> song_data/A/B/C/TRABCEI128F424C983.json
> song_data/A/A/B/TRAABJL12903CDCF1A.json

And below is an example of what a single song file, TRAABJL12903CDCF1A.json, looks like.

> {"num_songs": 1, "artist_id": "ARJIE2Y1187B994AB7", "artist_latitude": null, "artist_longitude": null, "artist_location": "", "artist_name": "Line Renaud", "song_id": "SOUPIRU12A6D4FA1E1", "title": "Der Kleine Dompfaff", "duration": 152.92036, "year": 0}

#### Log dataset
The second dataset consists of log files in JSON format generated by this event simulator based on the songs in the dataset above. These simulate activity logs from a music streaming app based on specified configurations.

The log files in the dataset are partitioned by year and month. For example, here are filepaths to two files in this dataset.

> log_data/2018/11/2018-11-12-events.json
> log_data/2018/11/2018-11-13-events.json


### Files
In addition to the data files, the project workspace includes four files:

- `create_tables.py` is where fact and dimension tables are created for the star schema in Redshift.
- `etl.py` is where data from S3 is loaded into staging tables on Redshift and then processed into the analytics tables on Redshift.
- `sql_queries.py` contains all sql queries, and is imported into the files above.
- `README.md` (this file)

### Database Schema and justification
In `sql_queries.py`, DROP, CREATE and INSERT queries are defined. These are read from `create_tables.py`.

There are two staging tables, `copy`ing the JSON files from S3:

- `staging_songs`, which contains songs info
- `staging_events`, which contains events data, like the songs users are listening to

The databases star schema consists of 1 fact table (`songplays`) and 4 dimension tables (`users`, `songs`, `artists` and `time`).

![Schema(db_diagram.jpeg)

- songplays - records in log data associated with song plays
`songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent`

- users - users in the app
`user_id, first_name, last_name, gender, level`
- songs - songs in music database
`song_id, title, artist_id, year, duration`
- artists - artists in music database
`artist_id, name, location, latitude, longitude`
- time - timestamps of records in songplays broken down into specific units
`start_time, hour, day, week, month, year, weekday`

### Extract, transform, load (ETL)
As stated above, `etl.py` reads and processes the files from song_data and log_data and loads them into the tables on Redshift

### Project steps
#### Create Table Schemas
- Design schemas for fact and dimension tables
- Write a SQL CREATE statement for each of these tables in `sql_queries.py`
- Complete the logic in `create_tables.py` to connect to the database and create these tables
- Write SQL DROP statements to drop tables in the beginning of `create_tables.py` if the tables already exist
- Launch a redshift cluster and create an IAM role that has read access to S3.
- Add redshift database and IAM role info to `dwh.cfg`.
- Test by running `create_tables.py` and checking the table schemas in the redshift database

#### Build ETL Pipeline
- Implement the logic in `etl.py` to load data from S3 to staging tables on Redshift.
- Implement the logic in `etl.py` to load data from staging tables to analytics tables on Redshift.
- Test by running `etl.py` after running `create_tables.py` and running the analytic queries on the Redshift database to compare the results with the expected results.
- Delete the redshift cluster when finished.

## Again, AWS keys should NOT be included in the file when sharing / publishing or committing it in some way

### Running the scripts
To run the project in the Udacity *Project Workspace*, open a new terminal window and enter

To create tables:
> python create_tables.py

Then, to run ETL process:
> python etl.py

### NOTE that for this to work, a Redshift cluster has to be launched, an IAM role and user need to be created and credentials have to be entered into `dwh.cfg`. See above.

### Testing
After running `create_tables.py`, the table schemas in the Redshift database can be tested. The Query Editor in the AWS Redshift console can be used for this.

A very basic and simple query to check some results in the Redshift Query editor:

`SELECT songs.title, artists.name
FROM songs
JOIN songplays ON songplays.song_id = songs.song_id
JOIN artists ON artists.artist_id = songplays.artist_id
WHERE artists.name LIKE 'The%'
LIMIT 10`