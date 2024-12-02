# Data Manipulation with SQL - Manchester United's 2014/2015 Season Analysis

## Overview
This case study demonstrates advanced SQL techniques to analyze Manchester United's performance during the 2014/2015 English Premier League season. The project showcases skills in data manipulation, complex queries, and window functions using SQL.

## Objective
To determine which teams defeated Manchester United in the 2014/2015 season and by what margin.

## Data Source
**Kaggle:** [European Soccer Database](https://www.kaggle.com/datasets/hugomathien/soccer)

## Key SQL Concepts Demonstrated
- Common Table Expressions (CTEs)
- CASE statements
- Window functions
- JOIN operations
- Subqueries

## Analysis Process

### 1. Setting up the Home Team CTE
First, we created a CTE to identify matches where Manchester United played as the home team:

```sql
WITH home AS (
  SELECT
    m.id,
    t.team_long_name,
    -- Identify matches as home/away wins or ties
    CASE 
      WHEN m.home_goal > m.away_goal THEN 'MU Win'
      WHEN m.home_goal < m.away_goal THEN 'MU Loss'
      ELSE 'Tie'
    END AS outcome
  FROM match AS m
  -- Left join team on the home team ID and team API id
LEFT JOIN team AS t 
  LEFT JOIN team AS t 
    ON m.hometeam_id = t.team_api_id
  WHERE m.season = '2014/2015' -- Filter for 2014/2015 and Manchester United as the home team
    AND t.team_long_name = 'Manchester United'
)
```

### 2. Setting up the Away Team CTE
We then created a similar CTE for matches where Manchester United played as the away team

```sql
away AS (
  SELECT
    m.id,
    t.team_long_name,
    -- Identify matches as home/away wins or ties
    CASE 
      WHEN m.home_goal > m.away_goal THEN 'MU Loss'
      WHEN m.home_goal < m.away_goal THEN 'MU Win'
      ELSE 'Tie'
    END AS outcome
  FROM match AS m
  -- Join team table to the match table
  LEFT JOIN team AS t 
    ON m.awayteam_id = t.team_api_id
  WHERE m.season = '2014/2015'  -- Filter for 2014/2015 and Manchester United as the away team
    AND t.team_long_name = 'Manchester United'
)
```

### 3. Combining CTEs and Adding Window Function
Finally, we combined both CTEs and added a window function to rank the matches based on goal difference:

```sql
SELECT DISTINCT
  m.date,
  home.team_long_name AS home_team,
  away.team_long_name AS away_team,
  m.home_goal,
  m.away_goal,
  RANK() OVER(ORDER BY ABS(home_goal - away_goal) DESC) as match_rank
FROM match AS m
LEFT JOIN home ON m.id = home.id
LEFT JOIN AWAY ON m.id = away.id
WHERE m.season = '2014/2015'
  AND ((home.team_long_name = 'Manchester United' AND home.outcome = 'MU Loss')
    OR (away.team_long_name = 'Manchester United' AND away.outcome = 'MU Loss'))
```

## Results

The query returned the following results:

| date       | home_team         | away_team            | home_goal | away_goal | match_rank |
| ---------- | ----------------- | -------------------- | --------- | --------- | ---------- |
| 2014-08-16 | Manchester United | Swansea City         | 1         | 2         | 3          |
| 2014-09-21 | Leicester City    | Manchester United    | 5         | 3         | 2          |
| 2014-11-02 | Manchester City   | Manchester United    | 1         | 0         | 3          |
| 2015-01-11 | Manchester United | Southampton          | 0         | 1         | 3          |
| 2015-02-21 | Swansea City      | Manchester United    | 2         | 1         | 3          |
| 2015-04-18 | Chelsea           | Manchester United    | 1         | 0         | 3          |
| 2015-04-26 | Everton           | Manchester United    | 3         | 0         | 1          |
| 2015-05-02 | Manchester United | West Bromwich Albion | 0         | 1         | 3          |


## Key Insights
Manchester United lost 8 matches during the 2014/2015 season.
The biggest loss was against Everton (0-3), ranked as match_rank 1.
The second biggest loss was against Leicester City (3-5), ranked as match_rank 2.
Most losses were by a 1-goal margin, ranked as match_rank 3.

## Conclusion
This analysis demonstrates proficiency in using advanced SQL techniques to extract meaningful insights from complex datasets. The query effectively identified and ranked Manchester United's losses in the 2014/2015 season, providing a clear picture of their performance against different teams.

## Future Work
* Extend the analysis to multiple seasons for trend analysis.
* Include additional metrics like possession, shots on target, etc., for a more comprehensive performance evaluation.
* Develop visualizations based on the SQL output for better data presentation.