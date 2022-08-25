# F1 Race Winner Predictor

## Overview
* **Purpose:** to develop a Machine Learning classification model that predicts the winner of each race of the 2021 season of Formula 1. Ultimately, have a robust model that can beat the odds in the upcoming seasons (despite the regulation changes of 2022)
* **Data source:** scraped from the [Formula 1 website](https://www.formula1.com/) and the [Ergast F1](https://ergast.com/mrd/) API.
* **Modeling:** built a tailor-made scoring function that evalueates predictions race after race and calculates a season-wide precision score. Used that score to compare and tune an SVM, a Random Forest, an XGBoost, and a Logistic Regression.
* **Results:** the model is able to predict the race winner with a 63% presicion score. That translates into right above half the races correctly predicted.

## 1. Data Collection
Data was scraped from the [Formula 1 website](https://www.formula1.com/) and the [Ergast F1](https://ergast.com/mrd/) API.  
Used the Beautiful Soup and Selenium libraries to scrape the 7 dataframes used for this project: `Races`, `Rounds`, `Results`, `Driver Championships`, `Constructors Championships`, `Qualifying`, and `Weather`. They can all be found in the data folder.

## 2. Data Preprocessing


## 3. EDA


## 4. Modeling


## 5. Next Steps
