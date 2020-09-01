# gettingandcleaningdataproject
Getting and Cleaning Data Project - Week 4 Course Project

This is the course project for the Getting and Cleaning Data Coursera course. The R script, run_analysis.R, does the following:

1. The files now need to be downloaded from the internet from a zip file and loaded into the files folder.
2. Once all the files are downloaded they need to be loaded into the environment to use in R Studio.
3. The first task is to merge the data sets together. To do this you either need to column bind first and then row bind, or row bind first, and then column bind. I chose to row bind first. and then create a new file named "Merged_UCI_Data" that had all of the needed raw data in it.
4. Once the raw data is merged you need to then extract the identification data (subject and activity) and also any variables that have mean, or standard deviation data. 
5. The next task was to change the code variable to more accurately describe the activity that it represents. by beginning to "tidy" the data with accurate labels it makes it easier to understand and analyze.
6. Now that we have the data within the columns tidy, our next task is to tidy the labels for the columns. I decided to first loook at the current names of the columns to find out what I could change. I found a lot of abbreviations were being used so I tried to relabel what I could to clarify the variables. 
7. The now that our data is Tidy, our next task is to create a new data table that has the summary statistics for mean and standard deviation. I am going to create a new data table called "TidySummaryData" that has only the means of the mean and standard devation data. I will then write this file so that I can hand it in for the assignment. 

Final Summary File and code to upload it into your R program
===================================================================
fileURL <- "https://raw.githubusercontent.com/mhomeslice/gettingandcleaningdataproject/master/TidySummaryData.txt"
download.file(fileURL, destfile = "TidySummaryData.txt" , method="curl")
TidySummaryData <- read.table("TidySummaryData.txt")

Here is the code with descriptions of what the code is doing (for more details see the codeboook markdown document)
=======================================================================

https://raw.githubusercontent.com/mhomeslice/gettingandcleaningdataproject/master/CodeBook.rmd

## Load data and graphing programs
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
names(TidyextractData) <- gsub("mean", "Mean", names(TidyextractData), 
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
        


Getting and Cleaning Data Course Project Directions and Guidance
===================================================================

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 

1) a tidy data set as described below.

2) a link to a Github repository with your script for performing the analysis.

3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. 

You should also include a README.md in the repo with your scripts. 

This repo explains how all of the scripts work and how they are connected.

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Here are the data for the project:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

You should create one R script called run_analysis.R that does the following.

Merges the training and the test sets to create one data set.
Extracts only the measurements on the mean and standard deviation for each measurement.
Uses descriptive activity names to name the activities in the data set
Appropriately labels the data set with descriptive variable names.
From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
Good luck!


Data Description and Details - UCI HAR Dataset
=============================================================

The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data. 

The dataset includes the following files:
=========================================

- 'README.txt'

- 'features_info.txt': Shows information about the variables used on the feature vector.

- 'features.txt': List of all features.

- 'activity_labels.txt': Links the class labels with their activity name.

- 'train/X_train.txt': Training set.

- 'train/y_train.txt': Training labels.

- 'test/X_test.txt': Test set.

- 'test/y_test.txt': Test labels.

The following files are available for the train and test data. Their descriptions are equivalent. 

- 'train/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30. 

- 'train/Inertial Signals/total_acc_x_train.txt': The acceleration signal from the smartphone accelerometer X axis in standard gravity units 'g'. Every row shows a 128 element vector. The same description applies for the 'total_acc_x_train.txt' and 'total_acc_z_train.txt' files for the Y and Z axis. 

- 'train/Inertial Signals/body_acc_x_train.txt': The body acceleration signal obtained by subtracting the gravity from the total acceleration. 

- 'train/Inertial Signals/body_gyro_x_train.txt': The angular velocity vector measured by the gyroscope for each window sample. The units are radians/second. 

Notes: 
======
- Features are normalized and bounded within [-1,1].
- Each feature vector is a row on the text file.

For more information about this dataset contact: activityrecognition@smartlab.ws
