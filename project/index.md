---
title       : Practical Machine Learining Course Project
subtitle    : 
author      : smhamam
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## Practical Machine Learining Course Project
```
First I loaded the Training and Testing sets
```

```r
setwd("~/Coursera/Data Science/predmachlearn/Proj")
tr<-read.csv("pml-training.csv")
ts<-read.csv("pml-testing.csv")
```

--- 

## Exploring Data
```
I made some plots to find out characteristics of the data, the plots show the data to be 
non-leaner.
```

```r
library(ggplot2)
qplot(roll_belt,pitch_belt,col=classe,data=tr)
qplot(roll_belt,yaw_belt,col=classe,data=tr)
qplot(pitch_belt,yaw_belt,col=classe,data=tr)
```

---
## Plot #1
<img class=center src=./assets/img/plot1.png height=500>

---
## Plot #2
<img class=center src=./assets/img/plot2.png height=500>

---
## Plot #3
<img class=center src=./assets/img/plot3.png height=500>

---
## Selecting Learning Algorithm
```
Since the data is non-leaner I choose Random Tree Forst Algorithm since it performs will 
on non-leaner data.
```

---
## Selecting Variables
```
Since the purpose of the project is to learn the training set and predict the class of the test 
set, I Analyzed the test set to find out which variables have data (not completely NA) as 
variables that are completely empty in the trainig set will not add any information to the 
prediction for the test set.
```

```r
naCnt<-summary(sapply(ts,is.na))
```
```
Find variable names that has data.
```

```r
cols<-names(ts)[grepl("FALSE",naCnt[2,])]
```
```
and skip the first 7 columns and the last column as they are not relevent to the learning problem.
```

```r
cols<-cols[8:53]
```

---
## Learining ...
```
USe the randomForest Package to learn from the trainig data, column 160 is the classe variable 
(output), prox=TRUE option for  proximity measures be computed,Proximities are used in replacing 
missing data, locating outliers, and producing illuminating low-dimensional views of the data.
all other option are default, number of trees ntree=500, number of variables selected randomly to 
construct each tree mtry=6.
finally save the model object to disk.
```

```r
library(randomForest)
modFit<-randomForest(tr[,cols], tr[,160], prox=TRUE,data=tr)
save(modFit,file="modFit.Rda")
```

---
## Prediction ...
```
Use the model opject to predict the class of the test data
```

```r
result<-predict(modFit,ts[cols])
```
```
 1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
 B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
 the prediction result is 100% accurate.
```

---
## Cross Validation
```
The randomForest package internally performs cross validation as per the documentation:
"in random forests, there is no need for cross-validation or a separate test set to get an 
unbiased estimate of the test set error. It is estimated internally, during the run, as follows:

Each tree is constructed using a different bootstrap sample from the original data. About 
one-third of the cases are left out of the bootstrap sample and not used in the construction 
of the kth tree.

Put each case left out in the construction of the kth tree down the kth tree to get a classification. 
In this way, a test set classification is obtained for each case in about one-third of the trees. 
At the end of the run, take j to be the class that got most of the votes every time case n was oob. 
The proportion of times that j is not equal to the true class of n averaged over all cases is the oob 
error estimate. This has proven to be unbiased in many tests."

```
[http://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm](http://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm)

---
## Out Of Bag (OOB) Error
```
Out of Bag Error is computed internally by the randomForest Package and for our case the OOB value 
can be retrived as follows.
```

```r
oobError<-modFit$err.rate[500,1]
```
```
The OOB value was = 0.003363572
```

---
## Variable Importance
```
The following command plots the variables with their importance
```

```r
varImpPlot(modFit)
```

---
## Variable Importance
<img class=center src=./assets/img/plot4.png height=500>

---
## Thank you
