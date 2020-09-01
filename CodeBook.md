---
title: "CodeBook"
author: "Mike Holmquest"
date: "8/31/2020"
output:
  html_document: default
  pdf_document: default
---
<div id="instructions">
Project Introduction
</div>

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Here are the data for the project:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip


<div id="instructions">
Review criteria
</div>

1. The submitted data set is tidy.
2. The Github repo contains the required scripts.
3. GitHub contains a code book that modifies and updates the available codebooks with the data to indicate all the variables and summaries calculated, along with units, and any other relevant information.
4. The README that explains the analysis files is clear and understandable.
5. The work submitted for this project is the work of the student who submitted it.

<div id="instructions">
Getting and Cleaning Data Course Project
</div>

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 
1) a tidy data set as described below, 
2) a link to a Github repository with your script for performing the analysis, and 
3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. 
You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.


You should create one R script called run_analysis.R that does the following.

Merges the training and the test sets to create one data set.
Extracts only the measurements on the mean and standard deviation for each measurement.
Uses descriptive activity names to name the activities in the data set
Appropriately labels the data set with descriptive variable names.
From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
Good luck!


## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

```{r cars}
## Load data and graphing programs
'''

```{r , echo = TRUE}
library(dplyr)
```
library(dplyr)

##Download Zip File
if (!file.exists("UCIDataset.zip")){
        fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
        download.file(fileURL, destfile = "UCIDataset.zip" , method="curl")
}  

if (!file.exists("UCIDataset")) { 
        unzip("UCIDataset.zip") 
}

## Load all data frames from project files
features <- read.table("UCI HAR Dataset/features.txt", col.names = c("n","functions"))
activities <- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("code", "activity"))
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subject")
x_test <- read.table("UCI HAR Dataset/test/X_test.txt", col.names = features$functions)
y_test <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "code")
subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "subject")
x_train <- read.table("UCI HAR Dataset/train/X_train.txt", col.names = features$functions)
y_train <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "code")

## Merge all training and test data sets

X <- rbind(x_train, x_test)
Y <- rbind(y_train, y_test)
Subject <- rbind(subject_train, subject_test)
Merge_UCI_Data <- cbind(Subject, Y, X)

## Extract only the mean and standard deviation measurement
TidyextractData <- Merge_UCI_Data %>%
        select(subject, code, contains("mean"), contains("std"))

## Use descriptive activity names in the dataset
TidyextractData$code <- activities[TidyextractData$code, 2]

## Write the Extracted Tidy Data to a file
write.table(TidyextractData, "TidyExtractData.txt", row.name = FALSE)

## Preview current column names
names(TidyextractData)

## Appropriately label the data set with descriptive names

names(TidyextractData)[1] = "SubjectID"
names(TidyextractData)[2] = "ActivityID"
names(TidyextractData) <- gsub ("Acc", "Accelerometer", names(TidyextractData))
names(TidyextractData) <- gsub ("Gyro", "Gyroscope", names(TidyextractData))
names(TidyextractData) <- gsub ("BodyBody", "Body", names(TidyextractData))
names(TidyextractData) <- gsub ("Mag", "Magnitude", names(TidyextractData))
names(TidyextractData) <- gsub ("^t", "Time", names(TidyextractData))
names(TidyextractData)<- gsub("^f", "Frequency", names(TidyextractData))
names(TidyextractData) <- gsub("tBody", "TimeBody", names(TidyextractData))
names(TidyextractData) <- gsub("mean", ".Mean", names(TidyextractData), 
        ignore.case = TRUE)
names(TidyextractData) <- gsub("std", "STD", names(TidyextractData), 
        ignore.case = TRUE)
names(TidyextractData) <- gsub("-freq()", "Frequency", names(TidyextractData), 
        ignore.case = TRUE)
names(TidyextractData) <- gsub("angle", "Angle", names(TidyextractData))
names(TidyextractData) <- gsub("gravity", "Gravity", names(TidyextractData))

## Format data set to create a new data table with the average of each variable
TidySummaryData <- TidyextractData %>%
        group_by(SubjectID, ActivityID) %>%
        summarise_all(funs(mean))

## Write the summary output to a text file             
write.table(TidySummaryData, "TidySummaryData.txt", row.name = FALSE)
        
