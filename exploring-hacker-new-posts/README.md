# Exploring Hacker News Posts

## Overview

This project analyzes a dataset of posts from Hacker News, a popular technology news website. The goal is to compare two types of posts: "Ask HN" and "Show HN", to determine which type receives more comments on average and at what time posts receive the most comments.

## Dataset

The dataset used in this project is a reduced version of the full Hacker News dataset, containing about 20,000 rows. Each row represents a post with the following information:
- ID
- Title
- URL
- Number of points
- Number of comments
- Author
- Created time

## Analysis

The project performs the following analyses:

1. Separates "Ask HN" and "Show HN" posts from other posts.
2. Calculates the average number of comments for both "Ask HN" and "Show HN" posts.
3. Determines which type of post receives more comments on average.
4. Analyzes the relationship between the time a post is created and the number of comments it receives.

## Key Findings

1. "Ask HN" posts receive more comments on average compared to "Show HN" posts.
2. The best time to create a post for maximum comments is around 15:00 (3 PM), with 38.59 comments per post on average.
3. There's a general trend of higher comment activity during typical working hours (9 AM to 5 PM).

## Tools and Libraries Used

- Python
- Pandas for data manipulation
- Datetime for time-based operations

## How to Run

1. Ensure you have Python and the required libraries installed.
2. Download the Jupyter notebook and the dataset.
3. Open the notebook in a Jupyter environment.
4. Run the cells in order to reproduce the analysis.

## Future Improvements

- Analyze a larger dataset for more robust conclusions.
- Investigate other factors that might influence comment counts.
- Perform statistical tests to validate the findings.


## Acknowledgements

Data sourced from Hacker News. This project was completed as part of personal learning journey with Dataquest.