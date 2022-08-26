# F1 Race Winner Predictor

## Overview
* **Purpose:** to develop a Machine Learning classification model that predicts the winner of each race of the 2021 season of Formula 1. Ultimately, have a robust model that can beat the odds in the upcoming seasons (despite the regulation changes of 2022)
* [**Data source:**](https://github.com/felipesanze/F1_Predictor/blob/main/README.md#1-data-collection) scraped from the [Formula 1 website](https://www.formula1.com/) and the [Ergast F1](https://ergast.com/mrd/) API.
* [**Modeling:**](https://github.com/felipesanze/F1_Predictor/blob/main/README.md#4-modeling) built a tailor-made scoring function that evalueates predictions race after race and calculates a season-wide precision score. Used that score to compare and tune an `SVM`, a `Random Forest`, an `XGBoost`, and a `Logistic Regression`. `Neural networks` will be coming soon.
* **Results:** the model is able to predict the race winner with a 63% presicion score. That translates into right above half the races correctly predicted.

## 1. [Data Collection](https://github.com/felipesanze/F1_Predictor/blob/main/1_Collection.ipynb)
Data was scraped from the [Formula 1 website](https://www.formula1.com/) and the [Ergast F1](https://ergast.com/mrd/) API. Scraped seasons from 1950 to 2021.   
Used the Beautiful Soup and Selenium libraries to scrape the 7 dataframes used for this project: `Races`, `Rounds`, `Results`, `Driver Championships`, `Constructors Championships`, `Qualifying`, and `Weather`. They can all be found in the data folder.

## 2. [Data Preprocessing](https://github.com/felipesanze/F1_Predictor/blob/main/2_Preprocessing.ipynb)
*  Merged all DataFrames and dropped visibly redundant colums.
*  Replaced missing values of `qualifying_time' by the average qualifying time for that team for that circuit. Missing values for points and position variables are assumed to be DNFs so they are replaced by 0.
### Feature Engineering
* `driver_age`: since race dates and driver birthdays are useless for our model, I decided to calculate the distant between the two and let driver age be a proxy for driver experience, which does affect likelihood of winning a race.
*  `qualifying_time_delta`: since the absolute qualifying time can vary from circuit to circuit, I calculated a driver's time distance from the pole-sitter. This way quali time becomes comparable across circuits.
*  `reg_era`: I bucketized the `season` variable into the different regulation eras in F1 history, as regulation changes can signify turning points for teams or drivers.
*  `weather_wet`: wet conditions can have a significant impact in race results.

## 3. [EDA](https://github.com/felipesanze/F1_Predictor/blob/main/3_EDA.ipynb)
### Feature Correlations
![](Images/correlation_matrix.png)
### Circuits where qualifying matters the most
These are the 2021 circuits ranked by the correlation of final race position and qualifying position. We can see that even in the lower end of the list, qualifying position has a very significant correlaction with the race result. We can expect a high feature importance.
![](Images/podium-grid_correlation.png)
### Historic performance of 2021 constructors
![](Images/all_time_race_pos_by_constructor_2021.png)
### Average race finish position (wet and dry)
![](Images/overall_driver_performance.png)
### Performance delta of wet conditions
![](Images/performance_delta_of_rain.png)

## 4. [Modeling](https://github.com/felipesanze/F1_Predictor/blob/main/4_Modeling.ipynb)
**Target variable:** `win` (i.e. did the driver win the race or not)
### Main challenge
The main modeling challenge in this case is that the model will not predict the winner of each race if I pass it the entire dataset from 1950 to 2021. What we need it to do is to only see one race at a time and predict the winner. For this I needed a custom made scoring and predicting function that processes race per race in batches.  
This also adds a layer of complexity to the model tunning process the evaluation of my results.
### Prediction and scoring
For a given race:
*  I run a classifier to predict the probability of winning for each driver
*  Sort the predicted values in descending order of win probability
*  Map the top driver to win = 1 and the rest to win = 0
*  Calculate the race score as the 'precision_score` of that race
Model score:
* Model score is the (sum of precision_score of all races) / (number of races in the test season). In other words, the average race precision score.
### Feature importance
* It makes sense that the qualifying result ('grid') be disproportionately more important than other features because it is the closest event to the actual race, specially in circuits where overtaking is more difficult. It is an indication of a driver's performance in 'lab conditions', without attacking, deffending, or managing tyres. 
* The accumulated constructor and driver points are an indication of how likely is this driver to win given its positions in the past races of the season. They contain the year-to-date performance of a driver/constructor in a single number.
* Similarly, the driver's age is a proxy for their tenure or experience in F1 (the two are extremely correlated). It is the all-time hisory of the driver. 
* Weather_wet turned out to be way less important than expected.
![](Images/feature_importance_mdi.png)
### Results
The model is able to predict the race winner with a `63%` presicion score. That translates into right above half the races correctly predicted. I do not recommend to use this model for betting purposes beyond the 2021 season. F1 regulations have suffered significant changes that make it unlikely that this model perform well from 2022 onwards. I will revisit it after the 2022 season ends to evaluate that possibility.

## 5. Next Steps
*  Put it all into production: create an automated web interface where, right after qualifying on Saturday, the user presses a button, the fresh data gets scraped and ran through the model. The user will get an expected winner for the race of that weekend. Just for the fun of it...
*  While estimating the probability of winning, we set teh driver with the higest probability as the winner. What if we calculate the percentage difference between the predicted winner and the predicted P2? This could give us a sense of how certain the model is about its prediction for a given race.
*  Using a GPU, build a Neural Network to hopefully improve the quality of our predictions.
*  Use Variance Inflation Factor (VIF) to check for multicollinearity.
