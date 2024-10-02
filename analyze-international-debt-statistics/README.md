# Analyze International Debt Statistics

## Project Overview
This project analyzes international debt data collected by The World Bank. The dataset contains information about the amount of debt (in USD) owed by developing countries across several categories. The analysis aims to answer key questions about global debt and provide insights into economic trends.


## Objectives
- Calculate the total amount of debt owed by countries in the dataset
- Identify the country with the highest debt and its amount.
- Determine the average amount of debt owed by countries across different debt indicators
- Explore other relevant insights from the international debt data.

## Tools and Technologies
- SQL (PostgreSQL)
- Jupyter Notebook
- Python (for data manipulation and analysis)

## Data Source
The data used in this project is from [The World Bank](https://www.worldbank.org/en/home)'s international debt statistics database.. 

It contains both national and regional debt statistics for several countries across the globe as recorded from 1970 to 2015.

## Key Findings
1. The dataset contains information on 124 distinct countries.
2. There are 25 different debt indicators in the dataset.
3. The total global debt amounts to approximately $3,079,734.49 million.
4. China has the highest total debt at $285,793,494,734.20.
5. The debt indicator with the highest average debt is "Principal repayments on external debt, long-term (AMT, current US$)" with an average of $5,904,868,401.50.

## Detailed Analysis
1. **Number of Distinct Countries**: We identified 124 unique countries in the dataset.

2. **Distinct Debt Indicators**: The analysis revealed 25 different debt indicators, providing a comprehensive view of various types of international debt.

3. **Total Debt**: The cumulative debt across all countries and indicators is approximately $3.08 trillion.

4. **Country with Highest Debt**: China tops the list with a total debt of about $285.79 billion.

5. **Average Debt by Indicator**: The top debt indicator by average amount is "Principal repayments on external debt, long-term", followed by "Principal repayments on external debt, private nonguaranteed".

## Conclusions
This analysis provides valuable insights into the global debt landscape, highlighting the scale of international debt and identifying key countries and debt categories that dominate the global economic scenario. The findings can be useful for policymakers, economists, and researchers studying global economic trends and debt management strategies.

## Future Work
- Analyze debt trends over time
- Investigate correlations between debt indicators and economic growth
- Explore regional patterns in debt distribution

## How to Run the Analysis
1. Ensure you have PostgreSQL installed and the international_debt database set up.
2. Open the Jupyter Notebook `Analyze International Debt Statistics.ipynb`.
3. Run the cells sequentially to reproduce the analysis.

