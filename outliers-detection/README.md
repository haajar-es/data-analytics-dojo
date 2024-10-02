# Outliers Detection Healthcare Services Co.


- [Outliers Detection Healthcare Services Co.](#outliers-detection-healthcare-services-co)
  - [Project Overview](#project-overview)
  - [Project Description](#project-description)
  - [Installation](#installation)
  - [Usage](#usage)
  - [Data](#data)
  - [Methodology](#methodology)
  - [Results](#results)
  - [Acknowledge](#acknowledge)

## Project Overview

This repository contains a data analysis project focused on developing an algorithm to detect outliers in healthcare data for healthcare services company. The primary goal is to identify anomalies within one or multiple columns of the dataset, providing valuable insights for healthcare management and decision-making.


## Project Description

In this project, we develop and implement an outlier detection algorithm tailored for healthcare data analysis. The main objectives are:

- Analyze healthcare-related data provided by healthcare services company
- Implement robust outlier detection methods for single and multiple columns
- Visualize and interpret the results to provide actionable insights
- Create a flexible and scalable solution for ongoing data analysis

## Installation

To set up this project locally, follow these steps:

1. Clone the repository:
   git clone https://github.com/haajar-es/outliers-detection.git

2. Navigate to the project directory:
   cd outliers-detection

3. Install required dependencies (it's recommended to use a virtual environment):
   pip install -r requirements.txt

## Usage

To run the outlier detection analysis:

1. Open the Jupyter notebook [outliers-detection.ipynb](notebook/outliers-detection.ipynb)
2. Follow the step-by-step instructions in the notebook to load data, perform analysis, and visualize results

## Data

The project uses a sample dataset [`sfr_test.csv`](data/sfr_test.csv). This file contains anonymized healthcare data from healthcare services company. Due to privacy concerns, the full dataset is not included in the repository. Please contact healthcare services company for access to the complete dataset if required.

## Methodology

Our outlier detection approach includes:

1. Data preprocessing and cleaning
2. Exploratory Data Analysis (EDA)
3. Implementation of various outlier detection algorithms, such as:
   - Z-score method
   - Interquartile Range (IQR) method
   - Isolation Forest
   - Local Outlier Factor (LOF)
4. Comparison and evaluation of different methods
5. Visualization of detected outliers

## Results

The main findings and results of the outlier detection analysis are documented in the Jupyter notebook. Key visualizations and summary statistics are provided to help interpret the results.

## Acknowledge

This project was completed as part of personal learning journey on data analytics including data exploration, outlier detection approaches, machine learning, results, and interpretation.
