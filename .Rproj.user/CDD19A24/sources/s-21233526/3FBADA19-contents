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
        

