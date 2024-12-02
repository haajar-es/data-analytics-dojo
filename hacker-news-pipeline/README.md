# Hacker News Pipeline

## Overview

This project implements a data pipeline to extract, transform, and load (ETL) data from the Hacker News API. The pipeline fetches the latest stories, processes the data, and stores it in a SQLite database. It's designed to run periodically to keep the database updated with the latest Hacker News stories.

## Features

1. **Data Extraction**: Fetches the latest story IDs and details from the Hacker News API.
2. **Data Transformation**: Processes and cleans the raw data, including handling missing values and converting timestamps.
3. **Data Loading**: Stores the processed data in a SQLite database.
4. **Incremental Updates**: Implements logic to avoid duplicating data and only add new stories.
5. **Error Handling**: Includes robust error handling and logging mechanisms.
6. **Scheduling**: Can be scheduled to run at regular intervals using tools like cron.

## Technologies Used

- Python
- SQLite
- Requests library for API calls
- Pandas for data manipulation
- logging module for error logging

## How It Works

1. The pipeline starts by fetching the latest story IDs from the Hacker News API.
2. It then retrieves detailed information for each story.
3. The data is cleaned and transformed into a suitable format.
4. New stories are identified and inserted into the SQLite database.
5. Any errors during the process are logged for later review.

## Setup and Running

1. Ensure you have Python installed on your system.
2. Install required libraries:
    pip install pandas requests
3. Clone this repository:
    `git clone https://github.com/haajar-es/Project-Hacker-News-Pipeline.git`
4. Navigate to the project directory and run the script:
   `python hacker_news_pipeline.py`

## Database Schema

The SQLite database contains a single table `stories` with the following columns:
- id (INTEGER PRIMARY KEY)
- title (TEXT)
- url (TEXT)
- score (INTEGER)
- descendants (INTEGER)
- time (INTEGER)

## License

This project is licensed under the MIT License - see the LICENSE.md file for details.

## Acknowledgements
Data sourced from Hacker News API. This project was completed as part of personal learning journey with Dataquest.