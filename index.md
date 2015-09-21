---
title       : Fuel Efficiency Predictor for Cars
subtitle    : Shiny App Based on MTCARS dataset in R
author      : Kalyan S. Mupparaju
job         : Coursera Developing Data Products Course Project
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : zenburn       # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## What does this application do?

This app predicts the mileage(miles per gallon) of a car which has a certain weight, quater mile time and transmission type.

## How to use this application?

* The sliders in the side bar can be used to input the weight(lb/1000) and quater mile time(seconds). 
* The radio buttons can be used to select the type of transmission. 
* Based on these inputs the miles per gallon (mpg) estimate of the car is shown in the Prediction Outcome tab of the main panel.

--- .class #id 

## How was this predictor app built?

### Data Processing

This predictor is trained on the mtcars data set in R. The data set was first partitioned into training and testing data sets. 

### Building The Model

- A *linear regression model* was fit on the training data. 
- Outcome: miles per gallon (mpg)
- Predictors: weight(wt), quater mile time(qsec), type of transmission(am) and  interaction term between type of transmission and weight(am:wt)

### Applying the model on test set and estimating out of sample error

- The linear model built on the training set is applied to the test set. 
- The root mean squared error is used as the error measure.

--- 

## Code showing entire model building and calculation of RMS error on test set.


```r
suppressMessages(library(caret))                    #Loading required libraries
library(datasets)                               
data("mtcars")                                      #Loading mtcars dataset
set.seed(101)                                       #Setting seed for reproducablity
mtTrainId <- createDataPartition(mtcars$mpg,p = 0.7, list = FALSE)
mtTrain <- mtcars[mtTrainId,]                       #Preparing training set
mtTest <- mtcars[-mtTrainId,]                       #Preparing test set
modelfit <- lm(mpg~wt+qsec+am+am:wt,data = mtTrain) #Fitting the linear model
testPred <- predict(modelfit, mtTest)               #Applying the fitted model to test  set
testRMSE <- sqrt(mean((mtTest$mpg-testPred)^2))     #Calculating test set RMSE
```
The test set RMS error is 

```r
testRMSE
```

```
## [1] 2.342093
```

---

## Model Performance on test data

Table below shows that this model came pretty close to the actual mpg values for many cars in the test set. Thus, this model can be very usefull.(Simple data.frame code, which is used below is not shown(echo=F) for brevity.)

```
##                   predictedMPG actualMPG
## Datsun 710            26.54088      22.8
## Hornet Sportabout     17.08204      18.7
## Valiant               20.72267      18.1
## Merc 450SLC           17.16556      15.2
## Honda Civic           31.65851      30.4
## Toyota Corolla        31.62569      33.9
## Toyota Corona         23.55352      21.5
## Maserati Bora         12.64285      15.0
```

### Note
Since this predictor app is based on the mtcars dataset and the mtcars data set was compiled in 1974, this app will give reasonalble results for 1970s model cars. For modern cars it may not work so well.
