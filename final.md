---
title: "Untitled"
author: "Victoria"
date: "12/10/2019"
output: 
  html_document: 
    keep_md: yes
---




## Introduction

The goal of your project is to predict the manner in which they did the exercise. This is the "classe" variable in the training set. You may use any of the other variables to predict with. You should create a report describing how you built your model, how you used cross validation, what you think the expected out of sample error is, and why you made the choices you did. You will also use your prediction model to predict 20 different test cases.

## Model built
The expacted outcome variable is classe, a 5 level of factor variable. 
In this dataset, participants were asked to perform one set of 10 repetitions of the Unilateral Dumbbell Biceps Curl in 5 different fashion: Class A - excatly according to the specification, Class B - throwing the elbows to the front, Class c- lifting the dumbbell only halfway, Class D - lowering the dumbbell only halfway, Class E - throwing the hips to the front. 

Class A correspons to the specified execution of the exercise, while the other 4 classes correpond to common mistakes. Decision tree will be used to create the model. After the model have been developed. Cross-validation will be performed. Two set of data will be created, original training data set (75%) and subtesting data set (25%). 


## Load library

```
## Installing package into 'C:/Users/dell/Documents/R/win-library/3.6'
## (as 'lib' is unspecified)
```

```
## package 'caret' successfully unpacked and MD5 sums checked
## 
## The downloaded binary packages are in
## 	C:\Users\dell\AppData\Local\Temp\Rtmp8oSdFq\downloaded_packages
```

```
## Loading required package: lattice
```

```
## Loading required package: ggplot2
```

```
## randomForest 4.6-14
```

```
## Type rfNews() to see new features/changes/bug fixes.
```

```
## 
## Attaching package: 'randomForest'
```

```
## The following object is masked from 'package:ggplot2':
## 
##     margin
```

## Download and loading the Dataset

```r
# Download the dataset 
trainUrl <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
testUrl <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
# Load the dataset into memory
trainingData <- read.csv(url(trainUrl), na.strings = c("NA", "#DIV/0!", ""))
testingData <- read.csv(url(testUrl), na.strings = c("NA", "#DIV/0!", ""))
#
trainingData <- trainingData[, colSums(is.na(trainingData)) == 0]
testingData <- testingData[, colSums(is.na(testingData)) == 0]
# Delete variables that are not related 
trainingData <- trainingData[, -c(1:7)]
testingData <- testingData[, -c(1:7)]
# partioning the training set into two different dataset
traningPartitionData <- createDataPartition(trainingData$classe,  p = 0.7, list = F)
trainingDataSet <- trainingData[traningPartitionData, ]
testingDataSet <- trainingData[-traningPartitionData, ]
dim(trainingData); dim(testingDataSet)
```

```
## [1] 19622    53
```

```
## [1] 5885   53
```

## Prediction model 1 - decision tree


```r
decisionTreeModel <- rpart(classe ~ ., data = trainingDataSet, method = "class")
decisionTreePrediction <- predict(decisionTreeModel, testingDataSet, type = "class")
# Plot Decision Tree
rpart.plot(decisionTreeModel, main = "Decision Tree", under = T, faclen = 0)
```

```
## Warning: labs do not fit even at cex 0.15, there may be some overplotting
```

![](final_files/figure-html/feature decision tree-1.png)<!-- -->

```r
# Using confusion matrix to test results
confusionMatrix(decisionTreePrediction, testingDataSet$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1430  209   20   80   59
##          B   74  700   83   72   71
##          C   81  115  766   64   44
##          D   41   48  145  687   99
##          E   48   67   12   61  809
## 
## Overall Statistics
##                                          
##                Accuracy : 0.7463         
##                  95% CI : (0.735, 0.7574)
##     No Information Rate : 0.2845         
##     P-Value [Acc > NIR] : < 2.2e-16      
##                                          
##                   Kappa : 0.6784         
##                                          
##  Mcnemar's Test P-Value : < 2.2e-16      
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.8542   0.6146   0.7466   0.7127   0.7477
## Specificity            0.9126   0.9368   0.9374   0.9323   0.9609
## Pos Pred Value         0.7953   0.7000   0.7159   0.6735   0.8114
## Neg Pred Value         0.9403   0.9101   0.9460   0.9431   0.9441
## Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839
## Detection Rate         0.2430   0.1189   0.1302   0.1167   0.1375
## Detection Prevalence   0.3055   0.1699   0.1818   0.1733   0.1694
## Balanced Accuracy      0.8834   0.7757   0.8420   0.8225   0.8543
```


## Prediction model 2 - random forest


```r
randomForestModel <- randomForest(classe ~. , data = trainingDataSet, method = "class")
randomForestPrediction <- predict(randomForestModel, testingDataSet, type = "class")
confusionMatrix(randomForestPrediction, testingDataSet$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1669    2    0    0    0
##          B    5 1134    8    0    0
##          C    0    3 1018    8    1
##          D    0    0    0  956    5
##          E    0    0    0    0 1076
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9946          
##                  95% CI : (0.9923, 0.9963)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9931          
##                                           
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9970   0.9956   0.9922   0.9917   0.9945
## Specificity            0.9995   0.9973   0.9975   0.9990   1.0000
## Pos Pred Value         0.9988   0.9887   0.9883   0.9948   1.0000
## Neg Pred Value         0.9988   0.9989   0.9984   0.9984   0.9988
## Prevalence             0.2845   0.1935   0.1743   0.1638   0.1839
## Detection Rate         0.2836   0.1927   0.1730   0.1624   0.1828
## Detection Prevalence   0.2839   0.1949   0.1750   0.1633   0.1828
## Balanced Accuracy      0.9983   0.9964   0.9949   0.9953   0.9972
```


## Prediction model 2 - random forest
From the result, it show Random Forest accuracy is higher than Decision tree which is 0.9915  > 0.6644. Therefore, we will use random forest to answer the assignment. 


```r
predictionFinal <- predict(randomForestModel, testingDataSet, type = "class")
#predictionFinal
```
