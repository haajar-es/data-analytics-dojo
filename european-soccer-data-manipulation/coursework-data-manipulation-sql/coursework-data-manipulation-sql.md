# Data Manipulation in SQL

## Overview

This is a part of coursework for **Data Analyst with SQL** track that I've completed from [DataCamp](https://app.datacamp.com/learn/courses/data-manipulation-in-sql).

### Keys takeaway:
- how to manipulate, transform, and make the most sense of our data.
- several key functions necessary to wrangle, filter, and categorize information in a relational database, and answer complex questions. 
- learn the robust use of CASE statements, subqueries, and window functions—all while discovering some interesting facts about soccer using the European Soccer Database.

### Relational Database Management System (RDBMS )

- PostgreSQL

### Dataset:
- Kaggle - [European Soccer Database](https://www.kaggle.com/datasets/hugomathien/soccer)

### Notes:
- For the results display purposes, I have limit the query to return with `LIMIT 10` lines only due to the large size of data.

## Contents


  - [Contents](#contents)
  - [CASE Statement](#case-statement)
    - [Basic CASE Statements](#basic-case-statements)
    - [CASE statements comparing column values](#case-statements-comparing-column-values)
    - [CASE statements comparing two column values part 2](#case-statements-comparing-two-column-values-part-2)
    - [In CASE of rivalry](#in-case-of-rivalry)
    - [Filtering your CASE statement](#filtering-your-case-statement)
    - [COUNT using CASE WHEN](#count-using-case-when)
    - [COUNT and CASE WHEN with multiple conditions](#count-and-case-when-with-multiple-conditions)
    - [Calculating percent with CASE and AVG](#calculating-percent-with-case-and-avg)
  - [Short and Simple Subqueries](#short-and-simple-subqueries)
    - [Filtering using scalar subqueries](#filtering-using-scalar-subqueries)
    - [Filtering using a subquery with a list](#filtering-using-a-subquery-with-a-list)
    - [Filtering with more complex subquery conditions](#filtering-with-more-complex-subquery-conditions)
    - [Joining Subqueries in FROM](#joining-subqueries-in-from)
    - [Building on Subqueries in FROM](#building-on-subqueries-in-from)
    - [Add a subquery to the SELECT clause](#add-a-subquery-to-the-select-clause)
    - [Subqueries in Select for Calculations](#subqueries-in-select-for-calculations)
    - [ALL the subqueries EVERYWHERE](#all-the-subqueries-everywhere)
    - [Add a subquery in FROM](#add-a-subquery-in-from)
    - [Add a subquery in SELECT](#add-a-subquery-in-select)
  - [Correlated Queries, Nested Queries, and Common Table Expressions](#correlated-queries-nested-queries-and-common-table-expressions)
    - [Basic Correlated Subqueries](#basic-correlated-subqueries)
    - [Correlated subquery with multiple conditions](#correlated-subquery-with-multiple-conditions)
    - [Nested simple subqueries](#nested-simple-subqueries)
    - [Nest a subquery in FROM](#nest-a-subquery-in-from)
    - [Clean up with CTEs](#clean-up-with-ctes)
    - [Organizing with CTEs](#organizing-with-ctes)
    - [CTEs with nested subqueries](#ctes-with-nested-subqueries)
    - [Get team names with a subquery](#get-team-names-with-a-subquery)
    - [Get team names with correlated subqueries](#get-team-names-with-correlated-subqueries)
    - [Get team names with CTEs](#get-team-names-with-ctes)
  - [Window Functions](#window-functions)
    - [The match is OVER](#the-match-is-over)
    - [What's OVER here?](#whats-over-here)
    - [Flip OVER your results](#flip-over-your-results)
    - [PARTITION BY a column](#partition-by-a-column)
    - [PARTITION BY multiple columns](#partition-by-multiple-columns)
    - [Slide to the left](#slide-to-the-left)
    - [Slide to the right](#slide-to-the-right)


## CASE Statement

```sql
CASE 
    WHEN x = 1 THEN 'a'
    WHEN x = 2 THEN 'b'
    ELSE 'c'
END AS new_column
```

***

### Basic CASE Statements

**Q1**

- Select the team's long name and API id from the teams_germany table.
- Filter the query for FC Schalke 04 and FC Bayern Munich using IN, giving you the team_api_IDs needed for the next step.

```sql
SELECT
	-- Select the team long name and team API id
	team_long_name,
	team_api_id
FROM teams_germany
-- Only include FC Schalke 04 and FC Bayern Munich
WHERE team_long_name IN ('FC Schalke 04', 'FC Bayern Munich');
```
```
| team_long_name   | team_api_id |
|------------------|-------------|
| FC Bayern Munich | 9823        |
| FC Schalke 04    | 10189       |
```

**Q2**

- Create a CASE statement that identifies whether a match in Germany included FC Bayern Munich, FC Schalke 04, or neither as the home team.
- Group the query by the CASE statement alias, home_team.

```sql
SELECT 
	CASE 
        WHEN hometeam_id = 10189 THEN 'FC Schalke 04'
        WHEN hometeam_id = 9823 THEN 'FC Bayern Munich'
        ELSE 'Other' 
        END AS home_team,
	COUNT(id) AS total_matches
FROM matches_germany
-- Group by the CASE statement alias
GROUP BY home_team;
```
```
| team_long_name   | team_api_id |
|------------------|-------------|
| FC Bayern Munich | 9823        |
| FC Schalke 04    | 10189       |
```

***

### CASE statements comparing column values

**Q1**

Select the date of the match and create a CASE statement to identify matches as home wins, home losses, or ties.

```sql
SELECT 
	-- Select the date of the match
	date,
	-- Identify home wins, losses, or ties
	CASE WHEN home_goal > away_goal THEN 'Home win!'
       WHEN home_goal < away_goal THEN 'Home loss :(' 
       ELSE 'Tie' 
  END AS outcome
FROM matches_spain;
```
```
| date       | outcome      |
|------------|--------------|
| 2012-01-21 | Home loss :( |
| 2012-01-22 | Home win!    |
| 2012-01-22 | Home loss :( |
| 2012-01-23 | Home win!    |
| 2012-01-21 | Tie          |
| 2012-01-22 | Tie          |
| 2012-01-21 | Home loss :( |
| 2012-01-21 | Home win!    |
| 2012-01-22 | Tie          |
| 2012-01-22 | Home loss :( |
```

**Q2**

- Left join the teams_spain table team_api_id column to the matches_spain table awayteam_id. This allows us to retrieve the away team's identity.
- Select team_long_name from teams_spain as opponent and complete the CASE statement from Step 1.

```sql
SELECT 
	m.date,
	--Select the team long name column and call it 'opponent'
	t.team_long_name AS opponent, 
	-- Complete the CASE statement with an alias
	CASE WHEN m.home_goal > m.away_goal THEN 'Home win!'
       WHEN  m.home_goal < m.away_goal THEN 'Home loss :('
       ELSE 'Tie' 
  END AS outcome
FROM matches_spain AS m
-- Left join teams_spain onto matches_spain
LEFT JOIN teams_spain AS t
ON m.awayteam_id = t.team_api_id;
```
```
| date       | opponent    | outcome      |
|------------|-------------|--------------|
| 2011-10-15 | Valencia CF | Tie          |
| 2011-09-24 | Valencia CF | Home win!    |
| 2011-09-17 | Valencia CF | Home loss :( |
| 2012-05-12 | Valencia CF | Home win!    |
| 2012-04-29 | Valencia CF | Home win!    |
| 2012-04-15 | Valencia CF | Home win!    |
| 2012-04-08 | Valencia CF | Tie          |
| 2012-03-24 | Valencia CF | Home win!    |
| 2012-03-18 | Valencia CF | Home loss :( |
| 2012-03-04 | Valencia CF | Home loss :( |
```

**Q3**

- Complete the same CASE statement as the previous steps.
- Filter for matches where the home team is FC Barcelona (id = 8634).

```sql
SELECT 
	m.date,
	t.team_long_name AS opponent,
    -- Complete the CASE statement with an alias
	CASE WHEN m.home_goal > m.away_goal THEN 'Barcelona win!'
       WHEN m.home_goal < m.away_goal THEN 'Barcelona loss :(' 
       ELSE 'Tie' END AS outcome 
FROM matches_spain AS m
LEFT JOIN teams_spain AS t 
ON m.awayteam_id = t.team_api_id
-- Filter for Barcelona as the home team
WHERE m.hometeam_id = 8634; 
```
```
| date       | opponent                | outcome           |
|------------|-------------------------|-------------------|
| 2012-02-19 | Valencia CF             | Barcelona win!    |
| 2011-10-29 | RCD Mallorca            | Barcelona win!    |
| 2011-09-17 | CA Osasuna              | Barcelona win!    |
| 2011-08-29 | Villarreal CF           | Barcelona win!    |
| 2012-04-21 | Real Madrid CF          | Barcelona loss :( |
| 2011-10-15 | Racing Santander        | Barcelona win!    |
| 2011-10-22 | Sevilla FC              | Tie               |
| 2012-03-03 | Real Sporting de Gijón  | Barcelona win!    |
| 2012-04-10 | Getafe CF               | Barcelona win!    |
| 2012-01-15 | Real Betis Balompié     | Barcelona win!    |
| 2012-05-05 | RCD Espanyol            | Barcelona win!    |
| 2012-03-31 | Athletic Club de Bilbao | Barcelona win!    |
| 2011-09-24 | Atlético Madrid         | Barcelona win!    |
| 2012-05-02 | Málaga CF               | Barcelona win!    |
| 2011-11-19 | Real Zaragoza           | Barcelona win!    |
| 2011-12-03 | Levante UD              | Barcelona win!    |
| 2012-02-04 | Real Sociedad           | Barcelona win!    |
| 2012-03-20 | Granada CF              | Barcelona win!    |
| 2011-11-29 | Rayo Vallecano          | Barcelona win!    |
```

***

### CASE statements comparing two column values part 2

- Complete the CASE statement to identify Barcelona's away team games (id = 8634) as wins, losses, or ties.
- Left join the teams_spain table team_api_id column on the matches_spain table hometeam_id column. This retrieves the identity of the home team opponent.
- Filter the query to only include matches where Barcelona was the away team.

```sql
-- Select matches where Barcelona was the away team
SELECT  
	m.date,
	t.team_long_name AS opponent,
	CASE WHEN m.home_goal < m.away_goal THEN 'Barcelona win!'
       WHEN m.home_goal > m.away_goal THEN 'Barcelona loss :(' 
       ELSE 'Tie' END AS outcome
FROM matches_spain AS m
-- Join teams_spain to matches_spain
LEFT JOIN teams_spain AS t 
ON m.hometeam_id = t.team_api_id
WHERE m.awayteam_id = 8634;
```
```
| date       | opponent                | outcome           |
|------------|-------------------------|-------------------|
| 2012-01-22 | Málaga CF               | Barcelona win!    |
| 2011-10-25 | Granada CF              | Barcelona win!    |
| 2011-11-06 | Athletic Club de Bilbao | Tie               |
| 2011-11-26 | Getafe CF               | Barcelona loss :( |
| 2011-12-10 | Real Madrid CF          | Barcelona win!    |
| 2012-01-08 | RCD Espanyol            | Tie               |
| 2012-01-28 | Villarreal CF           | Tie               |
| 2012-02-11 | CA Osasuna              | Barcelona loss :( |
| 2012-02-26 | Atlético Madrid         | Barcelona win!    |
| 2012-03-11 | Racing Santander        | Barcelona win!    |
| 2012-03-17 | Sevilla FC              | Barcelona win!    |
| 2011-09-10 | Real Sociedad           | Tie               |
| 2012-03-24 | RCD Mallorca            | Barcelona win!    |
| 2012-04-07 | Real Zaragoza           | Barcelona win!    |
| 2012-04-14 | Levante UD              | Barcelona win!    |
| 2012-04-29 | Rayo Vallecano          | Barcelona win!    |
| 2012-05-12 | Real Betis Balompié     | Tie               |
| 2011-09-21 | Valencia CF             | Tie               |
| 2011-10-02 | Real Sporting de Gijón  | Barcelona win!    |
```

***

### In CASE of rivalry

**Q1** 

- Complete the first CASE statement, identifying Barcelona or Real Madrid as the home team using the hometeam_id column.
- Complete the second CASE statement in the same way, using awayteam_id.

```sql
SELECT 
	date,
	-- Identify the home team as Barcelona or Real Madrid
	CASE WHEN hometeam_id = 8634 THEN 'FC Barcelona' 
       ELSE 'Real Madrid CF' END AS home,
    -- Identify the away team as Barcelona or Real Madrid
	CASE WHEN awayteam_id = 8634 THEN 'FC Barcelona' 
       ELSE 'Real Madrid CF' END AS away
FROM matches_spain
WHERE (awayteam_id = 8634 OR hometeam_id = 8634)
      AND (awayteam_id = 8633 OR hometeam_id = 8633);
```
```
| date       | home           | away           |
|------------|----------------|----------------|
| 2011-12-10 | Real Madrid CF | FC Barcelona   |
| 2012-04-21 | FC Barcelona   | Real Madrid CF |
| 2013-03-02 | Real Madrid CF | FC Barcelona   |
| 2012-10-07 | FC Barcelona   | Real Madrid CF |
| 2013-10-26 | FC Barcelona   | Real Madrid CF |
| 2014-03-23 | Real Madrid CF | FC Barcelona   |
| 2015-03-22 | FC Barcelona   | Real Madrid CF |
| 2014-10-25 | Real Madrid CF | FC Barcelona   |
```

**Q2**

- Construct the final CASE statement identifying who won each match. Note there are 3 possible outcomes, but 5 conditions that you need to identify.
- Fill in the logical operators to identify Barcelona or Real Madrid as the winner.

```sql
SELECT 
	date,
	CASE WHEN hometeam_id = 8634 THEN 'FC Barcelona' 
         ELSE 'Real Madrid CF' END as home,
	CASE WHEN awayteam_id = 8634 THEN 'FC Barcelona' 
         ELSE 'Real Madrid CF' END as away,
	-- Identify all possible match outcomes
	CASE WHEN home_goal > away_goal AND hometeam_id = 8634 THEN 'Barcelona win!'
        WHEN home_goal > away_goal AND hometeam_id = 8633 THEN 'Real Madrid win!'
        WHEN home_goal < away_goal AND awayteam_id = 8634 THEN 'Barcelona win!'
        WHEN home_goal < away_goal AND awayteam_id = 8633 THEN 'Real Madrid win!'
        ELSE 'Tie!' END AS outcome
FROM matches_spain
WHERE (awayteam_id = 8634 OR hometeam_id = 8634)
      AND (awayteam_id = 8633 OR hometeam_id = 8633);
```
```
| date       | home           | away           | outcome          |
|------------|----------------|----------------|------------------|
| 2011-12-10 | Real Madrid CF | FC Barcelona   | Barcelona win!   |
| 2012-04-21 | FC Barcelona   | Real Madrid CF | Real Madrid win! |
| 2013-03-02 | Real Madrid CF | FC Barcelona   | Real Madrid win! |
| 2012-10-07 | FC Barcelona   | Real Madrid CF | Tie!             |
| 2013-10-26 | FC Barcelona   | Real Madrid CF | Barcelona win!   |
| 2014-03-23 | Real Madrid CF | FC Barcelona   | Barcelona win!   |
| 2015-03-22 | FC Barcelona   | Real Madrid CF | Barcelona win!   |
| 2014-10-25 | Real Madrid CF | FC Barcelona   | Real Madrid win! |
```

***

### Filtering your CASE statement

**Q1**

- Identify Bologna's team ID listed in the teams_italy table by selecting the team_long_name and team_api_id.

```sql
-- Select team_long_name and team_api_id from team
SELECT
	team_long_name,
	team_api_id
FROM teams_italy
-- Filter for team long name
WHERE team_long_name = 'Bologna';
```
```
| team_long_name | team_api_id |
|----------------|-------------|
| Bologna        | 9857        |
```

**Q2**

- Select the season and date that a match was played.
- Complete the CASE statement so that only Bologna's home and away wins are identified.

```sql
-- Select the season and date columns
SELECT 
	season,
	date,
    -- Identify when Bologna won a match
	CASE WHEN hometeam_id = 9857
        AND home_goal > away_goal 
        THEN 'Bologna Win'
		WHEN awayteam_id = 9857
        AND away_goal > home_goal 
        THEN 'Bologna Win' 
		END AS outcome
FROM matches_italy;
```
```
| season    | date       | outcome |
|-----------|------------|---------|
| 2011/2012 | 2011-12-21 | null    |
| 2011/2012 | 2011-12-21 | null    |
| 2011/2012 | 2011-12-20 | null    |
| 2011/2012 | 2011-12-21 | null    |
| 2011/2012 | 2011-12-21 | null    |
| 2011/2012 | 2011-12-21 | null    |
| 2011/2012 | 2011-12-21 | null    |
| 2011/2012 | 2011-12-21 | null    |
| 2011/2012 | 2011-12-20 | null    |
| 2011/2012 | 2011-12-21 | null    |
```


**Q3**

- Select the home_goal and away_goal for each match.
- Use the CASE statement in the WHERE clause to filter all NULL values generated by the statement in the previous step.
  
```sql
-- Select the season, date, home_goal, and away_goal columns
SELECT 
	season,
  date,
	home_goal,
	away_goal
FROM matches_italy
WHERE 
-- Exclude games not won by Bologna
	CASE WHEN hometeam_id = 9857 AND home_goal > away_goal THEN 'Bologna Win'
		WHEN awayteam_id = 9857 AND away_goal > home_goal THEN 'Bologna Win' 
		END IS NOT NULL;
```
```
| season    | date       | home_goal | away_goal |
|-----------|------------|-----------|-----------|
| 2011/2012 | 2011-10-30 | 3         | 1         |
| 2011/2012 | 2011-12-04 | 1         | 0         |
| 2011/2012 | 2012-01-08 | 2         | 0         |
| 2011/2012 | 2012-02-21 | 2         | 0         |
| 2011/2012 | 2012-02-17 | 0         | 3         |
| 2011/2012 | 2012-04-12 | 1         | 0         |
| 2011/2012 | 2012-04-29 | 3         | 2         |
| 2011/2012 | 2012-05-02 | 0         | 1         |
| 2011/2012 | 2012-05-06 | 2         | 0         |
| 2011/2012 | 2011-10-16 | 0         | 2         |
```

***

### COUNT using CASE WHEN

**Q1**

- Create a CASE statement that identifies the id of matches played in the 2012/2013 season. Specify that you want ELSE values to be NULL.
- Wrap the CASE statement in a COUNT function and group the query by the country alias.

```sql
SELECT 
	c.name AS country,
    -- Count games from the 2012/2013 season
	COUNT(CASE WHEN m.season = '2012/2013' 
        	THEN m.id ELSE NULL END) AS matches_2012_2013
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```
```
| country     | matches_2012_2013 |
|-------------|-------------------|
| Portugal    | 240               |
| France      | 380               |
| Scotland    | 228               |
| Netherlands | 306               |
| Spain       | 380               |
| Belgium     | 240               |
| Italy       | 380               |
| Germany     | 306               |
| England     | 380               |
| Switzerland | 180               |
| Poland      | 240               |
```

**Q2**

- Create 3 CASE WHEN statements counting the matches played in each country across the 3 seasons.
- END your CASE statement without an ELSE clause.

```sql
SELECT 
	c.name AS country,
    -- Count matches in each of the 3 seasons
	COUNT(CASE WHEN m.season = '2012/2013' THEN m.id END) AS matches_2012_2013,
	COUNT(CASE WHEN m.season = '2013/2014' THEN m.id END) AS matches_2013_2014,
	COUNT(CASE WHEN m.season = '2014/2015' THEN m.id END) AS matches_2014_2015
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```
```
| country     | matches_2012_2013 | matches_2013_2014 | matches_2014_2015 |
|-------------|-------------------|-------------------|-------------------|
| Portugal    | 240               | 240               | 306               |
| France      | 380               | 380               | 380               |
| Scotland    | 228               | 228               | 228               |
| Netherlands | 306               | 306               | 306               |
| Spain       | 380               | 380               | 380               |
| Belgium     | 240               | 12                | 240               |
| Italy       | 380               | 380               | 379               |
| Germany     | 306               | 306               | 306               |
| England     | 380               | 380               | 380               |
| Switzerland | 180               | 180               | 180               |
| Poland      | 240               | 240               | 240               |
```

***

### COUNT and CASE WHEN with multiple conditions

- Create 3 CASE statements to "count" matches in the '2012/2013', '2013/2014', and '2014/2015' seasons, respectively.
- Have each CASE statement return a 1 for every match you want to include, and a 0 for every match to exclude.
- Wrap the CASE statement in a SUM to return the total matches played in each season.
- Group the query by the country name alias.

```sql
SELECT 
	c.name AS country,
    -- Sum the total records in each season where the home team won
	SUM(CASE WHEN m.season = '2012/2013' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 END) AS matches_2012_2013,
 	SUM(CASE WHEN m.season = '2013/2014' AND m.home_goal > m.away_goal
        THEN 1 ELSE 0 END) AS matches_2013_2014,
	SUM(CASE WHEN m.season = '2014/2015' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 END) AS matches_2014_2015
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```
```
| country     | matches_2012_2013 | matches_2013_2014 | matches_2014_2015 |
|-------------|-------------------|-------------------|-------------------|
| Portugal    | 103               | 108               | 137               |
| France      | 170               | 168               | 181               |
| Scotland    | 89                | 102               | 102               |
| Netherlands | 137               | 144               | 138               |
| Spain       | 189               | 179               | 171               |
| Belgium     | 102               | 6                 | 106               |
| Italy       | 177               | 181               | 152               |
| Germany     | 130               | 145               | 145               |
| England     | 166               | 179               | 172               |
| Switzerland | 84                | 82                | 76                |
| Poland      | 97                | 110               | 114               |
```

***

### Calculating percent with CASE and AVG

**Q1**

- Create 3 CASE statements to COUNT the total number of home team wins, away team wins, and ties, which will allow you to examine the total number of records.

```sql
SELECT 
    c.name AS country,
    -- Count the home wins, away wins, and ties in each country
	COUNT(CASE WHEN m.home_goal > m.away_goal THEN m.id 
        END) AS home_wins,
	COUNT(CASE WHEN m.home_goal < m.away_goal THEN m.id 
        END) AS away_wins,
	COUNT(CASE WHEN m.home_goal = m.away_goal THEN m.id 
        END) AS ties
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
```
```
| country     | home_wins | away_wins | ties |
|-------------|-----------|-----------|------|
| Portugal    | 245       | 156       | 145  |
| France      | 349       | 215       | 196  |
| Scotland    | 204       | 158       | 94   |
| Netherlands | 282       | 173       | 157  |
| Spain       | 350       | 233       | 177  |
| Belgium     | 112       | 78        | 62   |
| Italy       | 333       | 216       | 210  |
| Germany     | 290       | 176       | 146  |
| England     | 351       | 238       | 171  |
| Switzerland | 158       | 113       | 89   |
| Poland      | 224       | 117       | 139  |
```

**Q2**

- Calculate the percentage of matches tied using a CASE statement inside AVG.
- Fill in the logical operators for each statement. Alias your columns as ties_2013_2014 and ties_2014_2015, respectively.

```sql
SELECT 
	c.name AS country,
    -- Calculate the percentage of tied games in each season
	AVG(CASE WHEN m.season='2013/2014' AND m.home_goal = m.away_goal THEN 1
			WHEN m.season='2013/2014' AND m.home_goal != m.away_goal THEN 0
			END) AS ties_2013_2014,
	AVG(CASE WHEN m.season='2014/2015' AND m.home_goal = m.away_goal THEN 1
			WHEN m.season='2014/2015' AND m.home_goal != m.away_goal THEN 0
			END) AS ties_2014_2015
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
```

```
| country     | ties_2013_2014         | ties_2014_2015         |
|-------------|------------------------|------------------------|
| Portugal    | 0.25000000000000000000 | 0.27777777777777777778 |
| France      | 0.28421052631578947368 | 0.23157894736842105263 |
| Scotland    | 0.21929824561403508772 | 0.19298245614035087719 |
| Netherlands | 0.27450980392156862745 | 0.23856209150326797386 |
| Spain       | 0.22631578947368421053 | 0.23947368421052631579 |
| Belgium     | 0.16666666666666666667 | 0.25000000000000000000 |
| Italy       | 0.23684210526315789474 | 0.31662269129287598945 |
| Germany     | 0.20915032679738562092 | 0.26797385620915032680 |
| England     | 0.20526315789473684211 | 0.24473684210526315789 |
| Switzerland | 0.22777777777777777778 | 0.26666666666666666667 |
| Poland      | 0.30416666666666666667 | 0.27500000000000000000 |
```

**Q3**

- The previous "ties" columns returned values with 14 decimal points, which is not easy to interpret. Use the ROUND function to round to 2 decimal points.

```sql
SELECT 
	c.name AS country,
    -- Round the percentage of tied games to 2 decimal points
	ROUND(AVG(CASE WHEN m.season='2013/2014' AND m.home_goal = m.away_goal THEN 1
			 WHEN m.season='2013/2014' AND m.home_goal != m.away_goal THEN 0
			 END),2) AS pct_ties_2013_2014,
	ROUND(AVG(CASE WHEN m.season='2014/2015' AND m.home_goal = m.away_goal THEN 1
			 WHEN m.season='2014/2015' AND m.home_goal != m.away_goal THEN 0
			 END),2) AS pct_ties_2014_2015
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
```
```
| country     | pct_ties_2013_2014 | pct_ties_2014_2015 |
|-------------|--------------------|--------------------|
| Portugal    | 0.25               | 0.28               |
| France      | 0.28               | 0.23               |
| Scotland    | 0.22               | 0.19               |
| Netherlands | 0.27               | 0.24               |
| Spain       | 0.23               | 0.24               |
| Belgium     | 0.17               | 0.25               |
| Italy       | 0.24               | 0.32               |
| Germany     | 0.21               | 0.27               |
| England     | 0.21               | 0.24               |
| Switzerland | 0.23               | 0.27               |
| Poland      | 0.30               | 0.28               |
```

## Short and Simple Subqueries

```sql
SELECT column
FROM 
    (SELECT column
      FROM table) AS subquery
```

***

### Filtering using scalar subqueries

**Q1**

Calculate triple the average home + away goals scored across all matches. This will become your subquery in the next step. Note that this column does not have an alias, so it will be called ?column? in your results.

```sql
-- Select the average of home + away goals, multiplied by 3
SELECT 
	3 * AVG(home_goal + away_goal)
FROM matches_2013_2014;
```
```
| ?column?           |
|--------------------|
| 8.3004617414248020 |
```

**Q2**

- Select the date, home goals, and away goals in the main query.
- Filter the main query for matches where the total goals scored exceed the value in the subquery.

```sql
SELECT 
	-- Select the date, home goals, and away goals scored
    date,
	home_goal,
	away_goal
FROM  matches_2013_2014
-- Filter for matches where total goals exceeds 3x the average
WHERE (home_goal + away_goal) >
       (SELECT 3 * AVG(home_goal + away_goal)
        FROM matches_2013_2014); 
```
```
| date       | home_goal | away_goal |
|------------|-----------|-----------|
| 2013-12-14 | 6         | 3         |
| 2014-03-22 | 3         | 6         |
| 2013-10-30 | 7         | 3         |
```

***

### Filtering using a subquery with a list

- Create a subquery in the WHERE clause that retrieves all unique hometeam_ID values from the match table.
- Select the team_long_name and team_short_name from the team table. Exclude all values from the subquery in the main query.

```sql
SELECT 
	-- Select the team long and short names
	team_long_name,
	team_short_name
FROM team
-- Exclude all values from the subquery
WHERE team_api_id NOT IN
     (SELECT DISTINCT hometeam_id  FROM match);
```
```
| team_long_name       | team_short_name |
|----------------------|-----------------|
| FCV Dender EH        | DEN             |
| KSV Roeselare        | ROS             |
| Tubize               | TUB             |
| Royal Excel Mouscron | MOU             |
| KAS Eupen            | EUP             |
| Middlesbrough        | MID             |
| Portsmouth           | POR             |
| Birmingham City      | BIR             |
| Blackpool            | BLA             |
| Bournemouth          | BOU             |
```
***

### Filtering with more complex subquery conditions

- Create a subquery in WHERE clause that retrieves all hometeam_ID values from match with a home_goal score greater than or equal to 8.
- Select the team_long_name and team_short_name from the team table. Include all values from the subquery in the main query.

```sql
SELECT
	-- Select the team long and short names
	team_long_name,
	team_short_name
FROM team
-- Filter for teams with 8 or more home goals
WHERE team_api_id IN
	  (SELECT hometeam_id
       FROM match
       WHERE home_goal >= 8);
```

```
| team_long_name    | team_short_name |
|-------------------|-----------------|
| Manchester United | MUN             |
| Chelsea           | CHE             |
| Southampton       | SOU             |
| FC Bayern Munich  | BMU             |
| Real Madrid CF    | REA             |
| FC Barcelona      | BAR             |
```

***

### Joining Subqueries in FROM

**Q1**

- Create the subquery to be used in the next step, which selects the country ID and match ID (id) from the match table.
- Filter the query for matches with greater than or equal to 10 goals.

```sql
SELECT 
	-- Select the country ID and match ID
	  country_id, 
    id
FROM match
-- Filter for matches with 10 or more goals in total
WHERE (home_goal + away_goal) >= 10;
```
```
| country_id | id    |
|------------|-------|
| 1729       | 3093  |
| 1729       | 3369  |
| 1729       | 3566  |
| 7809       | 9211  |
| 13274      | 14224 |
| 21518      | 23444 |
| 21518      | 24016 |
| 21518      | 24114 |
| 21518      | 24123 |
```

***

### Building on Subqueries in FROM

- Complete the subquery inside the FROM clause. Select the country name from the country table, along with the date, the home goal, the away goal, and the total goals columns from the match table.
- Create a column in the subquery that adds home and away goals, called total_goals. This will be used to filter the main query.
- Select the country, date, home goals, and away goals in the main query.
- Filter the main query for games with 10 or more total goals.

```sql
SELECT
	-- Select country, date, home, and away goals from the subquery
    country,
    date,
    home_goal,
    away_goal
FROM 
	-- Select country name, date, home_goal, away_goal, and total goals in the subquery
	(SELECT c.name AS country, 
     	    m.date, 
     		  m.home_goal, 
     		  m.away_goal,
          (m.home_goal + m.away_goal) AS total_goals
    FROM match AS m
    LEFT JOIN country AS c
    ON m.country_id = c.id) AS subquery
-- Filter by total goals scored in the main query
WHERE total_goals >= 10;
```
```
| country     | date       | home_goal | away_goal |
|-------------|------------|-----------|-----------|
| England     | 2011-08-28 | 8         | 2         |
| England     | 2012-12-29 | 7         | 3         |
| England     | 2013-05-19 | 5         | 5         |
| Germany     | 2013-03-30 | 9         | 2         |
| Netherlands | 2011-11-06 | 6         | 4         |
| Spain       | 2013-10-30 | 7         | 3         |
| Spain       | 2015-04-05 | 9         | 1         |
| Spain       | 2015-05-23 | 7         | 3         |
| Spain       | 2014-09-20 | 2         | 8         |
```

***

### Add a subquery to the SELECT clause

- In the subquery, select the average total goals by adding home_goal and away_goal.
- Filter the results so that only the average of goals in the 2013/2014 season is calculated.
- In the main query, select the average total goals by adding home_goal and away_goal. This calculates the average goals for each league.
- Filter the results in the main query the same way you filtered the subquery. Group the query by the league name.

```sql
SELECT 
	l.name AS league,
    -- Select and round the league's total goals
    ROUND(AVG(m.home_goal + m.away_goal), 2) AS avg_goals,
    -- Select & round the average total goals for the season
    (SELECT ROUND(AVG(home_goal + away_goal), 2) 
     FROM match
     WHERE season = '2013/2014') AS overall_avg
FROM league AS l
LEFT JOIN match AS m
ON l.country_id = m.country_id
-- Filter for the 2013/2014 season
WHERE season = '2013/2014'
GROUP BY league;
```
```
| league                   | avg_goals | overall_avg |
|--------------------------|-----------|-------------|
| Switzerland Super League | 2.89      | 2.77        |
| Poland Ekstraklasa       | 2.64      | 2.77        |
| Netherlands Eredivisie   | 3.20      | 2.77        |
| Scotland Premier League  | 2.75      | 2.77        |
| France Ligue 1           | 2.46      | 2.77        |
| Spain LIGA BBVA          | 2.75      | 2.77        |
| Germany 1. Bundesliga    | 3.16      | 2.77        |
| Italy Serie A            | 2.72      | 2.77        |
| Portugal Liga ZON Sagres | 2.37      | 2.77        |
| England Premier League   | 2.77      | 2.77        |
| Belgium Jupiler League   | 2.50      | 2.77        |
```

***

### Subqueries in Select for Calculations

- Select the average goals scored in a match for each league in the main query.
- Select the average goals scored in a match overall for the 2013/2014 season in the subquery.
- Subtract the subquery from the average number of goals calculated for each league.
- Filter the main query so that only games from the 2013/2014 season are included.

```sql
SELECT
	-- Select the league name and average goals scored
	l.name AS league,
	ROUND(AVG(m.home_goal + m.away_goal),2) AS avg_goals,
    -- Subtract the overall average from the league average
	ROUND(AVG(m.home_goal + m.away_goal) - 
		(SELECT AVG(home_goal + away_goal)
		 FROM match 
         WHERE season = '2013/2014'),2) AS diff
FROM league AS l
LEFT JOIN match AS m
ON l.country_id = m.country_id
-- Only include 2013/2014 results
WHERE m.season = '2013/2014'
GROUP BY l.name;
```

```
| league                   | avg_goals | diff  |
|--------------------------|-----------|-------|
| Switzerland Super League | 2.89      | 0.12  |
| Poland Ekstraklasa       | 2.64      | -0.13 |
| Netherlands Eredivisie   | 3.20      | 0.43  |
| Scotland Premier League  | 2.75      | -0.02 |
| France Ligue 1           | 2.46      | -0.31 |
| Spain LIGA BBVA          | 2.75      | -0.02 |
| Germany 1. Bundesliga    | 3.16      | 0.39  |
| Italy Serie A            | 2.72      | -0.04 |
| Portugal Liga ZON Sagres | 2.37      | -0.40 |
| England Premier League   | 2.77      | 0.00  |
| Belgium Jupiler League   | 2.50      | -0.27 |
```

***

### ALL the subqueries EVERYWHERE

Extract data examining the average goals scored in each stage of a match. Does the average number of goals scored change as the stakes get higher from one stage to the next?

- Extract the average number of home and away team goals in two SELECT subqueries.
- Calculate the average home and away goals for the specific stage in the main query.
- Filter both subqueries and the main query so that only data from the 2012/2013 season is included.
- Group the query by the m.stage column.

```sql
SELECT 
	-- Select the stage and average goals for each stage
	m.stage,
    ROUND(AVG(m.home_goal + m.away_goal),2) AS avg_goals,
    -- Select the average overall goals for the 2012/2013 season
    ROUND((SELECT AVG(home_goal + away_goal) 
           FROM match 
           WHERE season = '2012/2013'),2) AS overall
FROM match AS m
-- Filter for the 2012/2013 season
WHERE season = '2012/2013'
-- Group by stage
GROUP BY m.stage;
```
```
| stage | avg_goals | overall |
|-------|-----------|---------|
| 29    | 2.63      | 2.77    |
| 4     | 2.80      | 2.77    |
| 34    | 2.68      | 2.77    |
| 32    | 2.75      | 2.77    |
| 9     | 2.70      | 2.77    |
| 7     | 2.69      | 2.77    |
| 10    | 2.96      | 2.77    |
| 35    | 2.43      | 2.77    |
| 38    | 3.17      | 2.77    |
| 15    | 2.76      | 2.77    |
```

***

### Add a subquery in FROM

In this next step, you will turn the main query into a subquery to extract a list of stages where the average home goals in a stage is higher than the overall average for home goals in a match.

- Calculate the average home goals and average away goals from the match table for each stage in the FROM clause subquery.
- Add a subquery to the WHERE clause that calculates the overall average home goals.
- Filter the main query for stages where the average home goals is higher than the overall average.
- Select the stage and avg_goals columns from the s subquery into the main query.

```sql
SELECT 
	-- Select the stage and average goals from the subquery
	s.stage,
    ROUND(s.avg_goals,2) AS avg_goals
FROM 
	-- Select the stage and average goals in 2012/2013
	(SELECT
         stage,
         AVG(home_goal + away_goal) AS avg_goals
     FROM match
     WHERE season = '2012/2013'
     GROUP BY stage) AS s
WHERE 
	-- Filter the main query using the subquery
	s.avg_goals > (SELECT AVG(home_goal + away_goal) 
                   FROM match WHERE season = '2012/2013');
```

```
| stage | avg_goals |
|-------|-----------|
| 3     | 2.83      |
| 4     | 2.80      |
| 6     | 2.78      |
| 8     | 3.09      |
| 10    | 2.96      |
| 11    | 2.92      |
| 12    | 3.23      |
| 17    | 2.85      |
| 20    | 2.96      |
| 21    | 2.90      |
```

***

### Add a subquery in SELECT

Add a subquery in SELECT to compare the average number of goals scored in each stage to the total.

- Create a subquery in SELECT that yields the average goals scored in the 2012/2013 season. Name the new column overall_avg.
- Create a subquery in FROM that calculates the average goals scored in each stage during the 2012/2013 season.
- Filter the main query for stages where the average goals exceeds the overall average in 2012/2013.

```sql
SELECT 
	-- Select the stage and average goals from s
	stage,
    ROUND(avg_goals,2) AS avg_goal,
    -- Select the overall average for 2012/2013
    (SELECT AVG(home_goal + away_goal) FROM match WHERE season = '2012/2013') AS overall_avg
FROM 
	-- Select the stage and average goals in 2012/2013 from match
	(SELECT
		 stage,
         AVG(home_goal + away_goal) AS avg_goals
	 FROM match
	 WHERE season = '2012/2013'
	 GROUP BY stage) AS s
WHERE 
	-- Filter the main query using the subquery
	s.avg_goals > (SELECT AVG(home_goal + away_goal) 
                    FROM match WHERE season = '2012/2013');
```
```
| stage | avg_goal | overall_avg        |
|-------|----------|--------------------|
| 4     | 2.80     | 2.7726993865030675 |
| 10    | 2.96     | 2.7726993865030675 |
| 38    | 3.17     | 2.7726993865030675 |
| 6     | 2.78     | 2.7726993865030675 |
| 12    | 3.23     | 2.7726993865030675 |
| 36    | 2.90     | 2.7726993865030675 |
| 31    | 3.06     | 2.7726993865030675 |
| 30    | 2.87     | 2.7726993865030675 |
| 21    | 2.90     | 2.7726993865030675 |
| 3     | 2.83     | 2.7726993865030675 |
```

## Correlated Queries, Nested Queries, and Common Table Expressions

### Basic Correlated Subqueries

Practice using correlated subqueries to examine matches with scores that are extreme outliers for each country -- above 3 times the average score!

Generated a list of matches with extremely high scores for each country.

- Select the country_id, date, home_goal, and away_goal columns in the main query.
- Complete the AVG value in the subquery.
- Complete the subquery column references, so that country_id is matched in the main and subquery.

```sql
SELECT 
	-- Select country ID, date, home, and away goals from match
	main.country_id,
    date,
    main.home_goal, 
    main.away_goal
FROM match AS main
WHERE 
	-- Filter the main query by the subquery
	(home_goal + away_goal) > 
        (SELECT AVG((sub.home_goal + sub.away_goal) * 3)
         FROM match AS sub
         -- Join the main query to the subquery in WHERE
         WHERE main.country_id = sub.country_id);
```

```
| country_id | date       | home_goal | away_goal |
|------------|------------|-----------|-----------|
| 1          | 2011-10-29 | 4         | 5         |
| 1729       | 2011-08-28 | 8         | 2         |
| 1729       | 2012-12-29 | 7         | 3         |
| 1729       | 2013-05-19 | 5         | 5         |
| 1729       | 2013-12-14 | 6         | 3         |
| 1729       | 2014-03-22 | 3         | 6         |
| 1729       | 2014-08-30 | 3         | 6         |
| 4769       | 2011-10-15 | 5         | 3         |
| 4769       | 2011-12-21 | 4         | 4         |
| 4769       | 2012-02-12 | 4         | 5         |
```

***

### Correlated subquery with multiple conditions

To add an additional column for matching to answer the question -- what was the highest scoring match for each country, in each season?

- Select the country_id, date, home_goal, and away_goal columns in the main query.
- Complete the subquery: Select the matches with the highest number of total goals.
- Match the subquery to the main query using country_id and season.
- Fill in the correct logical operator so that total goals equals the max goals recorded in the subquery.

```sql
SELECT 
	-- Select country ID, date, home, and away goals from match
	main.country_id,
    main.date,
    main.home_goal,
    main.away_goal
FROM match AS main
WHERE 
	-- Filter for matches with the highest number of goals scored
	(home_goal + away_goal) =
        (SELECT MAX(sub.home_goal + sub.away_goal)
         FROM match AS sub
         WHERE main.country_id = sub.country_id
               AND main.season = sub.season);
```
```
| country_id | date       | home_goal | away_goal |
|------------|------------|-----------|-----------|
| 1          | 2011-10-29 | 4         | 5         |
| 1          | 2012-11-17 | 2         | 6         |
| 1          | 2012-12-09 | 1         | 7         |
| 1          | 2013-01-19 | 2         | 6         |
| 1          | 2012-08-19 | 2         | 6         |
| 1          | 2014-04-19 | 2         | 4         |
| 1          | 2014-04-26 | 4         | 2         |
| 1          | 2015-01-17 | 1         | 7         |
| 1          | 2014-09-13 | 3         | 5         |
| 1729       | 2011-08-28 | 8         | 2         |
```

***

### Nested simple subqueries

Practice creating a nested subquery to examine the highest total number of goals in each season, overall, and during July across all seasons.

- Complete the main query to select the season and the max total goals in a match for each season. Name this max_goals.
- Complete the first simple subquery to select the max total goals in a match across all seasons. Name this overall_max_goals.
- Complete the nested subquery to select the maximum total goals in a match played in July across all seasons.
- Select the maximum total goals in the outer subquery. Name this entire subquery july_max_goals.

```sql
SELECT 
	-- Select the season and max goals scored in a match
	season,
    MAX(home_goal + away_goal) AS max_goals,
    -- Select the overall max goals scored in a match
   (SELECT MAX(home_goal + away_goal) FROM match) AS overall_max_goals,
    -- Select the max number of goals scored in any match in July
   (SELECT MAX(home_goal + away_goal) 
        FROM match
        WHERE id IN (
              SELECT id FROM match WHERE EXTRACT(MONTH FROM date) = 07)) AS july_max_goals
FROM match
GROUP BY season;
```
```
| season    | max_goals | overall_max_goals | july_max_goals |
|-----------|-----------|-------------------|----------------|
| 2012/2013 | 11        | 11                | 7              |
| 2014/2015 | 10        | 11                | 7              |
| 2013/2014 | 10        | 11                | 7              |
| 2011/2012 | 10        | 11                | 7              |
```

***

### Nest a subquery in FROM

Nesting subqueries and performing your transformations one step at a time, adding it to a subquery, and then performing the next set of transformations is often the easiest way to yield accurate information about your data. Let's get to it!

**Q1**

- Generate a list of matches where at least one team scored 5 or more goals.

```sql
SELECT
	country_id,
    season,
	id
FROM match
WHERE home_goal >= 5 OR away_goal >= 5;
```

```
| country_id | season    | id  |
|------------|-----------|-----|
| 1          | 2011/2012 | 764 |
| 1          | 2011/2012 | 766 |
| 1          | 2011/2012 | 781 |
| 1          | 2011/2012 | 791 |
| 1          | 2011/2012 | 854 |
| 1          | 2011/2012 | 877 |
| 1          | 2011/2012 | 878 |
| 1          | 2011/2012 | 885 |
| 1          | 2011/2012 | 912 |
| 1          | 2011/2012 | 978 |
```

**Q2**

- Turn the query from the previous step into a subquery in the FROM statement.
- COUNT the match ids generated in the previous step, and group the query by country_id and season.

```sql
-- Count match ids
SELECT
    country_id,
    season,
    COUNT(id) AS matches
-- Set up and alias the subquery
FROM (
	SELECT
    	country_id,
    	season,
    	id
	FROM match
	WHERE home_goal >= 5 OR away_goal >= 5) 
    AS subquery
-- Group by country_id and season
GROUP BY country_id, season;
```
```
| country_id | season    | matches |
|------------|-----------|---------|
| 1          | 2012/2013 | 12      |
| 24558      | 2013/2014 | 6       |
| 10257      | 2013/2014 | 8       |
| 7809       | 2014/2015 | 10      |
| 1729       | 2012/2013 | 15      |
| 17642      | 2011/2012 | 8       |
| 15722      | 2013/2014 | 8       |
| 21518      | 2011/2012 | 23      |
| 1729       | 2013/2014 | 14      |
| 24558      | 2014/2015 | 7       |
```

**Q3**

- Finally, declare the same query from step 2 as a subquery in FROM with the alias outer_s.
- Left join it to the country table using the outer query's country_id column.
- Calculate an AVG of high scoring matches per country in the main query.

```sql
SELECT
	c.name AS country,
    -- Calculate the average matches per season
    AVG(outer_s.matches) AS avg_seasonal_high_scores
FROM country AS c
-- Left join outer_s to country
LEFT JOIN (
  SELECT country_id, season,
         COUNT(id) AS matches
  FROM (
    SELECT country_id, season, id
	FROM match
	WHERE home_goal >= 5 OR away_goal >= 5) AS inner_s
  -- Close parentheses and alias the subquery
  GROUP BY country_id, season) AS outer_s
ON c.id = outer_s.country_id
GROUP BY country;
```
```
| country     | avg_seasonal_high_scores |
|-------------|--------------------------|
| Portugal    | 7.5000000000000000       |
| France      | 8.0000000000000000       |
| Scotland    | 8.0000000000000000       |
| Netherlands | 21.0000000000000000      |
| Spain       | 22.0000000000000000      |
| Belgium     | 11.3333333333333333      |
| Italy       | 8.7500000000000000       |
| Germany     | 13.7500000000000000      |
| England     | 15.0000000000000000      |
| Switzerland | 5.5000000000000000       |
| Poland      | 5.2500000000000000       |
```

***

### Clean up with CTEs

- Complete the syntax to declare your CTE.
- Select the country_id and match id from the match table in your CTE.
- Left join the CTE to the league table using country_id.

```sql
-- Set up your CTE
WITH match_list AS (
    SELECT 
  		country_id, 
      id
    FROM match
    WHERE (home_goal + away_goal) >= 10)
-- Select league and count of matches from the CTE
SELECT
    l.name AS league,
    COUNT(match_list.id) AS matches
FROM league AS l
-- Join the CTE to the league table
LEFT JOIN match_list ON l.id = match_list.country_id
GROUP BY l.name;
```
```
| league                   | matches |
|--------------------------|---------|
| Switzerland Super League | 0       |
| Poland Ekstraklasa       | 0       |
| Netherlands Eredivisie   | 1       |
| Scotland Premier League  | 0       |
| France Ligue 1           | 0       |
| Spain LIGA BBVA          | 4       |
| Germany 1. Bundesliga    | 1       |
| Italy Serie A            | 0       |
| Portugal Liga ZON Sagres | 0       |
| England Premier League   | 3       |
| Belgium Jupiler League   | 0       |
```

***

### Organizing with CTEs

Looking at details about matches with very high scores using CTEs. Just like a subquery in FROM, you can join tables inside a CTE.

- Declare your CTE, where you create a list of all matches with the league name.
- Select the league, date, home, and away goals from the CTE.
- Filter the main query for matches with 10 or more goals.

```sql
-- Set up your CTE
WITH match_list AS (
  -- Select the league, date, home, and away goals
    SELECT 
  		l.name AS league, 
     	m.date, 
  		m.home_goal, 
  		m.away_goal,
       (m.home_goal + m.away_goal) AS total_goals
    FROM match AS m
    LEFT JOIN league as l ON m.country_id = l.id)
-- Select the league, date, home, and away goals from the CTE
SELECT league, date, home_goal, away_goal
FROM match_list
-- Filter by total goals
WHERE total_goals >= 10;
```
```
| league                 | date       | home_goal | away_goal |
|------------------------|------------|-----------|-----------|
| England Premier League | 2011-08-28 | 8         | 2         |
| England Premier League | 2012-12-29 | 7         | 3         |
| England Premier League | 2013-05-19 | 5         | 5         |
| Germany 1. Bundesliga  | 2013-03-30 | 9         | 2         |
| Netherlands Eredivisie | 2011-11-06 | 6         | 4         |
| Spain LIGA BBVA        | 2013-10-30 | 7         | 3         |
| Spain LIGA BBVA        | 2015-04-05 | 9         | 1         |
| Spain LIGA BBVA        | 2015-05-23 | 7         | 3         |
| Spain LIGA BBVA        | 2014-09-20 | 2         | 8         |
```

***

### CTEs with nested subqueries

Since many queries are written with the intention of being saved and re-run in the future, proper organization is key to a seamless workflow. Arranging subqueries as CTEs will save you time, space, and confusion in the long run!

- Declare a CTE that calculates the total goals from matches in August of the 2013/2014 season.
- Left join the CTE onto the league table using country_id from the match_list CTE.
- Filter the list on the inner subquery to only select matches in August of the 2013/2014 season.

```sql
-- Set up your CTE
WITH match_list AS (
    SELECT 
  		country_id, 
  	  (home_goal + away_goal) AS goals
    FROM match
    -- Create a list of match IDs to filter data in the CTE
    WHERE id IN (
       SELECT id
       FROM match
       WHERE season = '2013/2014' AND EXTRACT(MONTH FROM date) = 08))
-- Select the league name and average of goals in the CTE
SELECT
	l.name,
    AVG(match_list.goals)
FROM league AS l
-- Join the CTE onto the league table
LEFT JOIN match_list ON l.id = match_list.country_id
GROUP BY l.name;
```
```
| name                     | avg                |
|--------------------------|--------------------|
| Switzerland Super League | 1.9375000000000000 |
| Poland Ekstraklasa       | 2.3103448275862069 |
| Netherlands Eredivisie   | 3.4146341463414634 |
| Scotland Premier League  | 2.1379310344827586 |
| France Ligue 1           | 2.0270270270270270 |
| Spain LIGA BBVA          | 2.9200000000000000 |
| Germany 1. Bundesliga    | 3.2352941176470588 |
| Italy Serie A            | 2.7500000000000000 |
| Portugal Liga ZON Sagres | 3.0000000000000000 |
| England Premier League   | 2.0000000000000000 |
| Belgium Jupiler League   | null               |
```

***

### Get team names with a subquery

Let's solve a problem we've encountered a few times in this course so far -- How do you get both the home and away team names into one final query result?

Out of the 4 techniques we just discussed, this can be performed using subqueries, correlated subqueries, and CTEs. Let's practice creating similar result sets using each of these 3 methods over the next 3 exercises, starting with subqueries in FROM.

**Q1**

- Create a query that left joins team to match in order to get the identity of the home team. This becomes the subquery in the next step.

```sql
SELECT 
	m.id, 
    t.team_long_name AS hometeam
-- Left join team to match
FROM match AS m
LEFT JOIN team as t
ON m.hometeam_id = team_api_id;
```
```
| id  | hometeam            |
|-----|---------------------|
| 757 | Oud-Heverlee Leuven |
| 758 | RAEC Mons           |
| 759 | KRC Genk            |
| 760 | KAA Gent            |
| 761 | Sporting Lokeren    |
| 762 | KV Kortrijk         |
| 763 | KV Mechelen         |
| 764 | Club Brugge KV      |
| 765 | Club Brugge KV      |
| 766 | RSC Anderlecht      |
```

**Q2**

Add a second subquery to the FROM statement to get the away team name, changing only the hometeam_id. Left join both subqueries to the match table on the id column.

*Warning: if your code is timing out, you have probably made a mistake in the JOIN and tried to join on the wrong fields which caused the table to be too big! Read the provided code and comments carefully, and check your ON conditions!*

```sql
SELECT
	m.date,
    -- Get the home and away team names
    hometeam,
    awayteam,
    m.home_goal,
    m.away_goal
FROM match AS m

-- Join the home subquery to the match table
LEFT JOIN (
  SELECT match.id, team.team_long_name AS hometeam
  FROM match
  LEFT JOIN team
  ON match.hometeam_id = team.team_api_id) AS home
ON home.id = m.id

-- Join the away subquery to the match table
LEFT JOIN (
  SELECT match.id, team.team_long_name AS awayteam
  FROM match
  LEFT JOIN team
  -- Get the away team ID in the subquery
  ON match.awayteam_id = team.team_api_id) AS away
ON away.id = m.id;
```
```
| date       | hometeam            | awayteam          | home_goal | away_goal |
|------------|---------------------|-------------------|-----------|-----------|
| 2011-07-29 | Oud-Heverlee Leuven | RSC Anderlecht    | 2         | 1         |
| 2011-07-30 | RAEC Mons           | Standard de Liège | 1         | 1         |
| 2011-07-30 | KRC Genk            | Beerschot AC      | 3         | 1         |
| 2011-07-30 | KAA Gent            | KSV Cercle Brugge | 0         | 1         |
| 2011-07-30 | Sporting Lokeren    | SV Zulte-Waregem  | 0         | 0         |
| 2011-07-30 | KV Kortrijk         | Lierse SK         | 1         | 1         |
| 2011-07-30 | KV Mechelen         | Sint-Truidense VV | 2         | 1         |
| 2011-07-31 | Club Brugge KV      | KVC Westerlo      | 5         | 0         |
| 2011-10-16 | Club Brugge KV      | KAA Gent          | 2         | 0         |
| 2011-10-16 | RSC Anderlecht      | Standard de Liège | 5         | 0         |
```

*This is one clear way to generate a list of team names who played in a match. This is much easier to read than team ID numbers!*

***

### Get team names with correlated subqueries

Let's solve the same problem using correlated subqueries -- How do you get both the home and away team names into one final query result?

This can easily be performed using correlated subqueries. But how might that impact the performance of your query? Complete the following steps and let's find out!

Please note that your query will run more slowly than the previous exercise!

**Q1**

Using a correlated subquery in the SELECT statement, match the team_api_id column from team to the hometeam_id from match.

```sql
SELECT
    m.date,
   (SELECT team_long_name
    FROM team AS t
    -- Connect the team to the match table
    WHERE t.team_api_id = m.hometeam_id) AS hometeam
FROM match AS m;
```
```
| date       | hometeam            |
|------------|---------------------|
| 2011-07-29 | Oud-Heverlee Leuven |
| 2011-07-30 | RAEC Mons           |
| 2011-07-30 | KRC Genk            |
| 2011-07-30 | KAA Gent            |
| 2011-07-30 | Sporting Lokeren    |
| 2011-07-30 | KV Kortrijk         |
| 2011-07-30 | KV Mechelen         |
| 2011-07-31 | Club Brugge KV      |
| 2011-10-16 | Club Brugge KV      |
| 2011-10-16 | RSC Anderlecht      |
```

**Q2**

- Create a second correlated subquery in SELECT, yielding the away team's name.
- Select the home and away goal columns from match in the main query.

```sql
SELECT
    m.date,
    (SELECT team_long_name
     FROM team AS t
     WHERE t.team_api_id = m.hometeam_id) AS hometeam,
    -- Connect the team to the match table
    (SELECT team_long_name
     FROM team AS t
     WHERE t.team_api_id = m.awayteam_id) AS awayteam,
    -- Select home and away goals
     home_goal,
     away_goal
FROM match AS m;
```

```
| date       | hometeam            | awayteam          | home_goal | away_goal |
|------------|---------------------|-------------------|-----------|-----------|
| 2011-07-29 | Oud-Heverlee Leuven | RSC Anderlecht    | 2         | 1         |
| 2011-07-30 | RAEC Mons           | Standard de Liège | 1         | 1         |
| 2011-07-30 | KRC Genk            | Beerschot AC      | 3         | 1         |
| 2011-07-30 | KAA Gent            | KSV Cercle Brugge | 0         | 1         |
| 2011-07-30 | Sporting Lokeren    | SV Zulte-Waregem  | 0         | 0         |
| 2011-07-30 | KV Kortrijk         | Lierse SK         | 1         | 1         |
| 2011-07-30 | KV Mechelen         | Sint-Truidense VV | 2         | 1         |
| 2011-07-31 | Club Brugge KV      | KVC Westerlo      | 5         | 0         |
| 2011-10-16 | Club Brugge KV      | KAA Gent          | 2         | 0         |
| 2011-10-16 | RSC Anderlecht      | Standard de Liège | 5         | 0         |
```

***

### Get team names with CTEs

>You've now explored two methods for answering the question, How do you get both the home and away team names into one final query result?

>Let's explore the final method - common table expressions. Common table expressions are similar to the subquery method for generating results, mainly differing in syntax and the order in which information is processed.

**Q1**

- Select id from match and team_long_name from team. Join these two tables together on hometeam_id in match and team_api_id in team.

```sql
SELECT 
	-- Select match id and team long name
    m.id, 
    t.team_long_name AS hometeam
FROM match AS m
-- Join team to match using team_api_id and hometeam_id
LEFT JOIN team AS t 
ON t.team_api_id = m.hometeam_id;
```
```
| id  | hometeam            |
|-----|---------------------|
| 757 | Oud-Heverlee Leuven |
| 758 | RAEC Mons           |
| 759 | KRC Genk            |
| 760 | KAA Gent            |
| 761 | Sporting Lokeren    |
| 762 | KV Kortrijk         |
| 763 | KV Mechelen         |
| 764 | Club Brugge KV      |
| 765 | Club Brugge KV      |
| 766 | RSC Anderlecht      |
```

**Q2**

- Declare the query from the previous step as a common table expression. SELECT everything from the CTE into the main query. Your results will not change at this step!

```sql
-- Declare the home CTE
WITH home AS (
	SELECT m.id, t.team_long_name AS hometeam
	FROM match AS m
	LEFT JOIN team AS t 
	ON m.hometeam_id = t.team_api_id)
-- Select everything from home
SELECT *
FROM home;
```


**Q3**

- Let's declare the second CTE, away. Join it to the first CTE on the id column.
- The date, home_goal, and away_goal columns have been added to the CTEs. SELECT them into the main query.

```sql
WITH home AS (
  SELECT m.id, m.date, 
  		 t.team_long_name AS hometeam, m.home_goal
  FROM match AS m
  LEFT JOIN team AS t 
  ON m.hometeam_id = t.team_api_id),
-- Declare and set up the away CTE
away AS (
  SELECT m.id, m.date, 
  		 t.team_long_name AS awayteam, m.away_goal
  FROM match AS m
  LEFT JOIN team AS t 
  ON m.awayteam_id = t.team_api_id)
-- Select date, home_goal, and away_goal
SELECT 
	home.date,
    home.hometeam,
    away.awayteam,
    home.home_goal,
    away.away_goal
-- Join away and home on the id column
FROM home
INNER JOIN away
ON home.id = away.id;
```
```
| date       | hometeam            | awayteam          | home_goal | away_goal |
|------------|---------------------|-------------------|-----------|-----------|
| 2011-07-29 | Oud-Heverlee Leuven | RSC Anderlecht    | 2         | 1         |
| 2011-07-30 | RAEC Mons           | Standard de Liège | 1         | 1         |
| 2011-07-30 | KRC Genk            | Beerschot AC      | 3         | 1         |
| 2011-07-30 | KAA Gent            | KSV Cercle Brugge | 0         | 1         |
| 2011-07-30 | Sporting Lokeren    | SV Zulte-Waregem  | 0         | 0         |
| 2011-07-30 | KV Kortrijk         | Lierse SK         | 1         | 1         |
| 2011-07-30 | KV Mechelen         | Sint-Truidense VV | 2         | 1         |
| 2011-07-31 | Club Brugge KV      | KVC Westerlo      | 5         | 0         |
| 2011-10-16 | Club Brugge KV      | KAA Gent          | 2         | 0         |
| 2011-10-16 | RSC Anderlecht      | Standard de Liège | 5         | 0         |
```


## Window Functions

### The match is OVER

>The OVER() clause allows you to pass an aggregate function down a data set, similar to subqueries in SELECT. The OVER() clause offers significant benefits over subqueries in select -- namely, your queries will run faster, and the OVER() clause has a wide range of additional functions and clauses you can include with it that we will cover later on in this chapter.

In this exercise, you will revise some queries from previous chapters using the OVER() clause.

- Select the match ID, country name, season, home, and away goals from the match and country tables.
- Complete the query that calculates the average number of goals scored overall and then includes the aggregate value in each row using a window function.


```sql
SELECT 
	-- Select the id, country name, season, home, and away goals
	m.id, 
    c.name AS country, 
    m.season,
	m.home_goal,
	m.away_goal,
    -- Use a window to include the aggregate average in each row
	AVG(m.home_goal + m.away_goal) OVER() AS overall_avg
FROM match AS m
LEFT JOIN country AS c ON m.country_id = c.id;
```
```
| id  | country | season    | home_goal | away_goal | overall_avg        |
|-----|---------|-----------|-----------|-----------|--------------------|
| 757 | Belgium | 2011/2012 | 2         | 1         | 2.7321025161642128 |
| 758 | Belgium | 2011/2012 | 1         | 1         | 2.7321025161642128 |
| 759 | Belgium | 2011/2012 | 3         | 1         | 2.7321025161642128 |
| 760 | Belgium | 2011/2012 | 0         | 1         | 2.7321025161642128 |
| 761 | Belgium | 2011/2012 | 0         | 0         | 2.7321025161642128 |
| 762 | Belgium | 2011/2012 | 1         | 1         | 2.7321025161642128 |
| 763 | Belgium | 2011/2012 | 2         | 1         | 2.7321025161642128 |
| 764 | Belgium | 2011/2012 | 5         | 0         | 2.7321025161642128 |
| 765 | Belgium | 2011/2012 | 2         | 0         | 2.7321025161642128 |
| 766 | Belgium | 2011/2012 | 5         | 0         | 2.7321025161642128 |
```

***

### What's OVER here?

>Window functions allow you to create a RANK of information according to any variable you want to use to sort your data. When setting this up, you will need to specify what column/calculation you want to use to calculate your rank. This is done by including an ORDER BY clause inside the OVER() clause. Below is an example:

```
SELECT 
    id,
    RANK() OVER(ORDER BY home_goal) AS rank
FROM match;
```

In this exercise, you will create a data set of ranked matches according to which leagues, on average, score the most goals in a match.

- Select the league name and average total goals scored from league and match.
- Complete the window function so it calculates the rank of average goals scored across all leagues in the database.
- Order the rank by the average total of home and away goals scored.

```sql
SELECT 
	-- Select the league name and average goals scored
	l.name AS league,
    AVG(m.home_goal + m.away_goal) AS avg_goals,
    -- Rank each league according to the average goals
    RANK() OVER(ORDER BY AVG(m.home_goal + m.away_goal)) AS league_rank
FROM league AS l
LEFT JOIN match AS m 
ON l.id = m.country_id
WHERE m.season = '2011/2012'
GROUP BY l.name
-- Order the query by the rank you created
ORDER BY league_rank;
```
```
| league                   | avg_goals          | league_rank |
|--------------------------|--------------------|-------------|
| Poland Ekstraklasa       | 2.1958333333333333 | 1           |
| France Ligue 1           | 2.5157894736842105 | 2           |
| Italy Serie A            | 2.5837988826815642 | 3           |
| Switzerland Super League | 2.6234567901234568 | 4           |
| Scotland Premier League  | 2.6359649122807018 | 5           |
| Portugal Liga ZON Sagres | 2.6416666666666667 | 6           |
| Spain LIGA BBVA          | 2.7631578947368421 | 7           |
| England Premier League   | 2.8052631578947368 | 8           |
| Germany 1. Bundesliga    | 2.8594771241830065 | 9           |
| Belgium Jupiler League   | 2.8791666666666667 | 10          |
| Netherlands Eredivisie   | 3.2581699346405229 | 11          |
```
***

### Flip OVER your results

>In the last exercise, the rank generated in your query was organized from smallest to largest. By adding DESC to your window function, you can create a rank sorted from largest to smallest.

```
SELECT 
    id,
    RANK() OVER(ORDER BY home_goal DESC) AS rank
FROM match;
```

- Complete the same parts of the query as the previous exercise.
- Complete the window function to rank each league from highest to lowest average goals scored.
- Order the main query by the rank you just created.


```sql
SELECT 
	-- Select the league name and average goals scored
	l.name AS league,
    AVG(m.home_goal + m.away_goal) AS avg_goals,
    -- Rank leagues in descending order by average goals
    RANK() OVER(ORDER BY AVG(m.home_goal + m.away_goal) DESC) AS league_rank
FROM league AS l
LEFT JOIN match AS m 
ON l.id = m.country_id
WHERE m.season = '2011/2012'
GROUP BY l.name
-- Order the query by the rank you created
ORDER BY league_rank DESC;
```

```
| league                   | avg_goals          | league_rank |
|--------------------------|--------------------|-------------|
| Poland Ekstraklasa       | 2.1958333333333333 | 11          |
| France Ligue 1           | 2.5157894736842105 | 10          |
| Italy Serie A            | 2.5837988826815642 | 9           |
| Switzerland Super League | 2.6234567901234568 | 8           |
| Scotland Premier League  | 2.6359649122807018 | 7           |
| Portugal Liga ZON Sagres | 2.6416666666666667 | 6           |
| Spain LIGA BBVA          | 2.7631578947368421 | 5           |
| England Premier League   | 2.8052631578947368 | 4           |
| Germany 1. Bundesliga    | 2.8594771241830065 | 3           |
| Belgium Jupiler League   | 2.8791666666666667 | 2           |
| Netherlands Eredivisie   | 3.2581699346405229 | 1           |
```
***

### PARTITION BY a column

The PARTITION BY clause allows you to calculate separate "windows" based on columns you want to divide your results. For example, you can create a single column that calculates an overall average of goals scored for each season.

In this exercise, you will be creating a data set of games played by Legia Warszawa (Warsaw League), the top ranked team in Poland, and comparing their individual game performance to the overall average for that season.

Where do you see more outliers? Are they Legia Warszawa's home or away games?

- Complete the two window functions that calculate the home and away goal averages. Partition the window functions by season to calculate separate averages for each season.
- Filter the query to only include matches played by Legia Warszawa, id = 8673.

```sql
SELECT 
	date,
	season,
    home_goal,
    away_goal,
    CASE WHEN hometeam_id = 8673 THEN 'home' 
         ELSE 'away' END AS warsaw_location,
    -- Calculate the average goals scored partitioned by season
    AVG(home_goal) OVER(PARTITION BY season) AS season_homeavg,
    AVG(away_goal) OVER(PARTITION BY season) AS season_awayavg
FROM match
-- Filter the data set for Legia Warszawa matches only
WHERE 
	hometeam_id = 8673 
    OR awayteam_id = 8673
ORDER BY (home_goal + away_goal) DESC;
```
```
| date       | season    | home_goal | away_goal | warsaw_location | season_homeavg     | season_awayavg     |
|------------|-----------|-----------|-----------|-----------------|--------------------|--------------------|
| 2013-09-14 | 2013/2014 | 3         | 5         | away            | 1.7666666666666667 | 1.2333333333333333 |
| 2014-09-13 | 2014/2015 | 4         | 3         | home            | 1.5666666666666667 | 1.3333333333333333 |
| 2013-07-20 | 2013/2014 | 5         | 1         | home            | 1.7666666666666667 | 1.2333333333333333 |
| 2014-08-09 | 2014/2015 | 5         | 0         | home            | 1.5666666666666667 | 1.3333333333333333 |
| 2013-02-23 | 2012/2013 | 3         | 2         | away            | 1.5666666666666667 | 1.1333333333333333 |
| 2013-09-25 | 2013/2014 | 2         | 3         | away            | 1.7666666666666667 | 1.2333333333333333 |
| 2013-12-15 | 2013/2014 | 4         | 1         | home            | 1.7666666666666667 | 1.2333333333333333 |
| 2012-10-28 | 2012/2013 | 3         | 2         | home            | 1.5666666666666667 | 1.1333333333333333 |
| 2013-10-20 | 2013/2014 | 4         | 1         | home            | 1.7666666666666667 | 1.2333333333333333 |
| 2013-06-02 | 2012/2013 | 5         | 0         | home            | 1.5666666666666667 | 1.1333333333333333 |
```

***

### PARTITION BY multiple columns

The PARTITION BY clause can be used to break out window averages by multiple data points (columns). You can even calculate the information you want to use to partition your data! For example, you can calculate average goals scored by season and by country, or by the calendar year (taken from the date column).

In this exercise, you will calculate the average number home and away goals scored Legia Warszawa, and their opponents, partitioned by the month in each season.

- Construct two window functions partitioning the average of home and away goals by season and month.
- Filter the dataset by Legia Warszawa's team ID (8673) so that the window calculation only includes matches involving them.
  

```sql
SELECT 
	date,
    season,
    home_goal,
    away_goal,
    CASE WHEN hometeam_id = 8673 THEN 'home' 
         ELSE 'away' END AS warsaw_location,
    -- Calculate average goals partitioned by season and month
    AVG(home_goal) OVER(PARTITION BY season, 
         	EXTRACT(MONTH FROM date)) AS season_mo_home,
    AVG(away_goal) OVER(PARTITION BY season, 
            EXTRACT(MONTH FROM date)) AS season_mo_away
FROM match
WHERE 
	hometeam_id = 8673 
    OR awayteam_id = 8673
ORDER BY (home_goal + away_goal) DESC;
```
```
| date       | season    | home_goal | away_goal | warsaw_location | season_mo_home     | season_mo_away         |
|------------|-----------|-----------|-----------|-----------------|--------------------|------------------------|
| 2013-09-14 | 2013/2014 | 3         | 5         | away            | 2.2500000000000000 | 2.5000000000000000     |
| 2014-09-13 | 2014/2015 | 4         | 3         | home            | 2.0000000000000000 | 2.6666666666666667     |
| 2013-07-20 | 2013/2014 | 5         | 1         | home            | 2.5000000000000000 | 2.0000000000000000     |
| 2014-08-09 | 2014/2015 | 5         | 0         | home            | 2.0000000000000000 | 1.00000000000000000000 |
| 2013-12-15 | 2013/2014 | 4         | 1         | home            | 2.2500000000000000 | 0.25000000000000000000 |
| 2013-06-02 | 2012/2013 | 5         | 0         | home            | 5.0000000000000000 | 0E-20                  |
| 2013-02-23 | 2012/2013 | 3         | 2         | away            | 3.0000000000000000 | 2.0000000000000000     |
| 2012-10-28 | 2012/2013 | 3         | 2         | home            | 1.6666666666666667 | 2.0000000000000000     |
| 2013-09-25 | 2013/2014 | 2         | 3         | away            | 2.2500000000000000 | 2.5000000000000000     |
| 2013-10-20 | 2013/2014 | 4         | 1         | home            | 2.2500000000000000 | 0.75000000000000000000 |
```

***

### Slide to the left

>Sliding windows allow you to create running calculations between any two points in a window using functions such as PRECEDING, FOLLOWING, and CURRENT ROW. You can calculate running counts, sums, averages, and other aggregate functions between any two points you specify in the data set.

In this exercise, you will expand on the examples discussed in the video, calculating the running total of goals scored by the FC Utrecht when they were the home team during the 2011/2012 season. Do they score more goals at the end of the season as the home or away team?

- Complete the window function by:
  - Assessing the running total of home goals scored by FC Utrecht.
  - Assessing the running average of home goals scored.
  - Ordering both the running average and running total by date.


```sql
SELECT 
	date,
	home_goal,
	away_goal,
    -- Create a running total and running average of home goals
    SUM(home_goal) OVER(ORDER BY date 
         ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)AS running_total,
    AVG(home_goal) OVER(ORDER BY date
         ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_avg
FROM match
WHERE 
	hometeam_id = 9908 
	AND season = '2011/2012';
```

```
| date       | home_goal | away_goal | running_total | running_avg        |
|------------|-----------|-----------|---------------|--------------------|
| 2011-08-14 | 2         | 2         | 2             | 2.0000000000000000 |
| 2011-08-27 | 3         | 1         | 5             | 2.5000000000000000 |
| 2011-09-18 | 2         | 2         | 7             | 2.3333333333333333 |
| 2011-10-01 | 3         | 0         | 10            | 2.5000000000000000 |
| 2011-10-22 | 1         | 4         | 11            | 2.2000000000000000 |
| 2011-11-06 | 6         | 4         | 17            | 2.8333333333333333 |
| 2011-12-04 | 2         | 6         | 19            | 2.7142857142857143 |
| 2011-12-11 | 2         | 2         | 21            | 2.6250000000000000 |
| 2012-01-22 | 1         | 1         | 22            | 2.4444444444444444 |
| 2012-02-12 | 1         | 1         | 23            | 2.3000000000000000 |
| 2012-02-19 | 3         | 0         | 26            | 2.3636363636363636 |
| 2012-03-04 | 0         | 0         | 26            | 2.1666666666666667 |
| 2012-03-18 | 3         | 1         | 29            | 2.2307692307692308 |
| 2012-03-30 | 3         | 2         | 32            | 2.2857142857142857 |
| 2012-04-15 | 4         | 2         | 36            | 2.4000000000000000 |
| 2012-04-28 | 1         | 3         | 37            | 2.3125000000000000 |
| 2012-05-02 | 2         | 2         | 39            | 2.2941176470588235 |
```

*Sliding windows allow for a wide variety of calculations with your database.*

***


### Slide to the right

>Now let's see how FC Utrecht performs when they're the away team. You'll notice that the total for the season is at the bottom of the data set you queried. Depending on your results, this could be pretty long, and scrolling down is not very helpful.

In this exercise, you will slightly modify the query from the previous exercise by sorting the data set in reverse order and calculating a backward running total from the CURRENT ROW to the end of the data set (earliest record).

- Complete the window function by:
  - Assessing the running total of home goals scored by FC Utrecht.
  - Assessing the running average of home goals scored.
  - Ordering both the running average and running total by date, descending.

```sql
SELECT 
	-- Select the date, home goal, and away goals
	date,
    home_goal,
    away_goal,
    -- Create a running total and running average of home goals
    SUM(home_goal) OVER(ORDER BY date DESC
         ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) AS running_total,
    AVG(home_goal) OVER(ORDER BY date DESC
         ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) AS running_avg
FROM match
WHERE 
	awayteam_id = 9908 
    AND season = '2011/2012';
```
```
| date       | home_goal | away_goal | running_total | running_avg            |
|------------|-----------|-----------|---------------|------------------------|
| 2012-05-06 | 1         | 3         | 25            | 1.4705882352941176     |
| 2012-04-21 | 0         | 2         | 24            | 1.5000000000000000     |
| 2012-04-12 | 3         | 0         | 24            | 1.6000000000000000     |
| 2012-03-25 | 3         | 1         | 21            | 1.5000000000000000     |
| 2012-03-11 | 1         | 1         | 18            | 1.3846153846153846     |
| 2012-02-26 | 1         | 0         | 17            | 1.4166666666666667     |
| 2012-02-05 | 0         | 2         | 16            | 1.4545454545454545     |
| 2012-01-28 | 2         | 0         | 16            | 1.6000000000000000     |
| 2011-12-17 | 1         | 0         | 14            | 1.5555555555555556     |
| 2011-11-25 | 2         | 0         | 13            | 1.6250000000000000     |
| 2011-11-20 | 2         | 2         | 11            | 1.5714285714285714     |
| 2011-10-30 | 3         | 1         | 9             | 1.5000000000000000     |
| 2011-10-15 | 1         | 0         | 6             | 1.2000000000000000     |
| 2011-09-24 | 1         | 0         | 5             | 1.2500000000000000     |
| 2011-09-11 | 2         | 3         | 4             | 1.3333333333333333     |
| 2011-08-20 | 2         | 1         | 2             | 1.00000000000000000000 |
| 2011-08-06 | 0         | 0         | 0             | 0E-20                  |
```

*You can reverse your calculations with your window functions to best suit your needs in an analysis*
