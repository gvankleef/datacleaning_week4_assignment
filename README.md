---
title: "README"
author: "Guido van Kleef"
date: "19 mei 2019"
output: html_document
---

## Libraries
loading the libraries that are needed for this assignment. 
```
library(downloader)
library(dplyr)
library(tidyr)
```

## Downloading the data
Creating a directory if it's not exists, download the data, unzip the data.

```
if(!dir.exists("./data")){dir.create("./data")}
url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download(url, dest="./data/dataset.zip", mode="wb") 
unzip ("./data/dataset.zip")
list.files("UCI HAR Dataset")
```
## Reading the data
```
##labels
labels_activity <- read.csv("UCI HAR Dataset/activity_labels.txt", sep = "", col.names = c("activityId","activityName"), header = FALSE)

##features
features <- read.csv("UCI HAR Dataset/features.txt", sep = "", col.names = c("featureNr","featureName"), header = FALSE)

##test dataset
test_data <- read.csv("UCI HAR Dataset/test/X_test.txt", sep = "", header = FALSE)
test_subjects <- read.csv("UCI HAR Dataset/test/subject_test.txt", sep = "", header = FALSE)
test_labels <- read.csv("UCI HAR Dataset/test/y_test.txt", sep = "", header = FALSE)


##train dataset
train_data <- read.csv("UCI HAR Dataset/train/X_train.txt", sep="",header = FALSE)
train_subjects <- read.csv("UCI HAR Dataset/train/subject_train.txt", sep="", header = FALSE)
train_labels <- read.csv("UCI HAR Dataset/train/y_train.txt", sep="", header = FALSE)
```

## Setting the column names
```
#set the column names for the test dataset
colnames(test_data) = features[,2]
colnames(test_labels) = "activityId"
colnames(test_subjects) = "subjectId"

#set the column names for the train dataset
colnames(train_data) = features[,2]
colnames(train_labels) = "activityId"
colnames(train_subjects) = "subjectId
```

## Join the train & test data sets - task 1
```
##merge the test dataset
test_complete <- cbind(test_data,test_labels,test_subjects)

##merge the train dataset
train_complete <- cbind(train_data,train_labels,train_subjects)

##join the test and train dataset - task 1
data_complete <- rbind(test_complete,train_complete)
```

## Create a subset of the data by selecting the colomn names of all the means and standard deviations - task 2

```
##all availables column names
colNames = colnames(data_complete)

#create a subset of all the mean and standards and the correspondongin activityID and subjectID 
mean_and_std_colnames = (grepl("activityId" , colNames) | grepl("subjectId" , colNames) | grepl("mean.." , colNames) | grepl("std.." , colNames))

##A subtset has to be created to get the required dataset - task 2
mean_and_std_data <- data_complete[ , mean_and_std_colnames == TRUE]
```

##Uses descriptive activity names to name the activities in the data set - task 3
```
data_activity_names <- merge(mean_and_std_data,labels_activity, by = "activityId", all.x = TRUE)

```

##From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject - task 4
```
tidy_data <- aggregate(. ~ subjectId + activityName, data = data_activity_names, mean)
```

## Write the tidy data table to a txt file
```
write.table(tidy_data, "tidy_data.txt", row.name=FALSE)
```



