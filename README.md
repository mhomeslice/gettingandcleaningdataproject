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
fileURL <- "https://github.com/mhomeslice/gettingandcleaningdataproject/blob/master/TidyFinalData.txt")

TidyFinalData <- read.table("TidyFinalData.txt")

Here is the code with descriptions of what the code is doing (for more details see the codeboook markdown document)
=======================================================================

https://github.com/mhomeslice/gettingandcleaningdataproject/blob/master/CodeBook.rmd

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

##Files are in new folder names "UCI HAR Dataset".
## What are the file names that we are interested in?
list.files("UCI HAR Dataset", recursive=TRUE)

## These are all .txt files
## Load all data frames from project files and add in column names
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt")
x_test <- read.table("UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("UCI HAR Dataset/test/y_test.txt")
subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt")
x_train <- read.table("UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("UCI HAR Dataset/train/y_train.txt")

##Now download and look at the features and activity tables.
features <- read.table("UCI HAR Dataset/features.txt")
activities <- read.table("UCI HAR Dataset/activity_labels.txt")
head(features)
head(activities)

## add names to the data frames using the features and activity frames
colnames(subject_train) <- "subjectID"
colnames(x_train) <- features[,2]
colnames(y_train) <- "activityID"

colnames(subject_test) <- "subjectID"
colnames(x_test) <- features[,2]
colnames(y_test) <- "activityID"


## Check dimensions on
dim(y_train)
dim(x_train)

## Now all of the data sets need to be combined
## Merge all training and test data sets

Subject_Data <- rbind(subject_train, subject_test)
X_Data <- rbind(x_train, x_test)
Y_Data <- rbind(y_train, y_test)
Merge_UCI_Data <- cbind(Subject_Data, Y_Data, X_Data)

## The next step extracts some valid information from the data frame
## Extract only the mean and standard deviation measurement
FinalData <- Merge_UCI_Data %>%
        select(subjectID, activityID, contains("mean"), contains("std"))
##Look at data to make sure the right data was collected
head(FinalData)
dim(FinalData)

## Use descriptive activity names in the dataset

## Change the Activity ID codes into descriptive names with the activities data table
FinalData$activityID <- activities[FinalData$activityID, 2]

## What do some of the column names look like?
head(colnames(FinalData), 10)

## Appropriately label the data set with descriptive names
names(FinalData) <- gsub ("Acc", "Accelerometer", names(FinalData))
names(FinalData) <- gsub ("Gyro", "Gyroscope", names(FinalData))
names(FinalData) <- gsub ("BodyBody", "Body", names(FinalData))
names(FinalData) <- gsub ("Mag", "Magnitude", names(FinalData))
names(FinalData) <- gsub ("^t", "Time", names(FinalData))
names(FinalData)<- gsub("^f", "Frequency", names(FinalData))
names(FinalData) <- gsub("tBody", "TimeBody", names(FinalData))
names(FinalData) <- gsub("-mean()", "Mean", names(FinalData),
        ignore.case = TRUE)
names(FinalData) <- gsub("std", "STD", names(FinalData),
        ignore.case = TRUE)
names(FinalData) <- gsub("-freq()", "Frequency", names(FinalData),
        ignore.case = TRUE)
names(FinalData) <- gsub("freq()", "Frequency", names(FinalData),
                         ignore.case = TRUE)
names(FinalData) <- gsub("angle", "Angle", names(FinalData))
names(FinalData) <- gsub("gravity", "Gravity", names(FinalData))

## Format data set to create a new data table with the average of each variable
TidyFinalData <- FinalData %>%
        group_by(subjectID, activityID) %>%
        summarise_all(funs(mean))

## Write the summary output to a text file
write.table(TidyFinalData, "TidyFinalData.txt", row.name = FALSE)
