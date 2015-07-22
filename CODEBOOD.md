CodeBook.md
Ladan Rabbani

July 21, 2015

Getting and cleaning data Course project
The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Here are the data for the project:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

### You should create one R script called run_analysis.R that does the following. 
####1.Merges the training and the test sets to create one data set.
####2.Extracts only the measurements on the mean and standard deviation for each measurement.
#### 3.Uses descriptive activity names to name the activities in the data set 
#### 4.Appropriately labels the data set with descriptive variable names.
#### 5.From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

Description of the Data
The features selected for this database come from the accelerometer and gyroscope 3-axial raw signals tAcc-XYZ and tGyro-XYZ. These time domain signals (prefix ‘t’ to denote time) were captured at a constant rate of 50 Hz. and the acceleration signal was then separated into body and gravity acceleration signals (tBodyAcc-XYZ and tGravityAcc-XYZ) – both using a low pass Butterworth filter.

The body linear acceleration and angular velocity were derived in time to obtain Jerk signals (tBodyAccJerk- XYZ and tBodyGyroJerk-XYZ). Also the magnitude of these three-dimensional signals were calculated using the Euclidean norm (tBodyAccMag, tGravityAccMag, tBodyAccJerkMag, tBodyGyroMag, tBodyGyroJerkMag).

A Fast Fourier Transform (FFT) was applied to some of these signals producing fBodyAcc-XYZ, fBodyAccJerk-XYZ, fBodyGyro-XYZ, fBodyAccJerkMag, fBodyGyroMag, fBodyGyroJerkMag. (Note the ‘f’ to indicate frequency domain signals).

Description of abbreviations of measurements
1. leading t or f is based on time or frequency measurements.

2. Body = related to body movement.

3. Gravity = acceleration of gravity

4. Acc = accelerometer measurement

5. Gyro = gyroscopic measurements

6. Jerk = sudden movement acceleration

7. Mag = magnitude of movement

8. mean and SD are calculated for each subject for each activity for each mean and SD measurements.

The units given are g’s for the accelerometer and rad/sec for the gyro and g/sec and rad/sec/sec for the corresponding jerks.
These signals were used to estimate variables of the feature vector for each pattern:
‘-XYZ’ is used to denote 3-axial signals in the X, Y and Z directions. They total 33 measurements including the 3 dimensions - the X,Y, and Z axes.

tBodyAcc-XYZ tGravityAcc-XYZ tBodyAccJerk-XYZ
tBodyGyro-XYZ
tBodyGyroJerk-XYZ
tBodyAccMag
tGravityAccMag
tBodyAccJerkMag
tBodyGyroMag
tBodyGyroJerkMag
fBodyAcc-XYZ
fBodyAccJerk-XYZ
fBodyGyro-XYZ
fBodyAccMag
fBodyAccJerkMag
fBodyGyroMag
fBodyGyroJerkMag The set of variables that were estimated from thease signals are :
mean() : Mean value
std(): Standard deviation
Data Set Information
The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data.

The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows. From each window, a vector of features was obtained by calculating variables from the time and frequency domain.
Download the Data and unziping

filesPath <- "/Users/LadanMac/Desktop/coursera/geting and cleaning data /data/UCI HAR Dataset"
setwd(filespath)
if(!file.exists("./data")){dir.create("./data")}
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="./data/Dataset.zip",method="curl")
unzip(zipfile = "./data/Dataset.zip",exdir="./data")

load the packages

library(dplyr)
library(data.table)
library(tidyr)
Files in the Dataset Folder that will be used
1.subject files

   * test/subject_test.txt
   * train/subject_train.txt
   
2. ** activity files **

    * test/X_test.txt
    * train/X_train.txt
    
3. data files

   * test/Y_test.txt
   * train/Y_train.txt
   
4. features.txt - Names of column variables in the dataTable

5. activity_labels.txt - Links the class labels with their activity name.

Read the above files and create data tables.

dataSubjectTrain <- tbl_df(read.table(file.path(filesPath, "train", "subject_train.txt")))
dataSubjectTest <- tbl_df(read.table(file.path(filesPath, "test" , "subject_test.txt" )))

dataActivityTrain <- tbl_df(read.table(file.path(filesPath, "train", "Y_train.txt")))
dataActivityTest  <- tbl_df(read.table(file.path(filesPath, "test" , "Y_test.txt" )))

dataTrain <- tbl_df(read.table(file.path(filesPath, "train", "X_train.txt" )))
dataTest  <- tbl_df(read.table(file.path(filesPath, "test" , "X_test.txt" )))

##1. Merges the training and the test sets to create one data set

##merge the training and the test sets by row for Activity and Subject

alldataSubject <- rbind(dataSubjectTrain, dataSubjectTest)

###name the variables

setnames(alldataSubject, "V1", "subject")
alldataActivity<- rbind(dataActivityTrain, dataActivityTest)
setnames(alldataActivity, "V1", "activityNumber")

####combing the Data training and Test files 

dataTable <- rbind(dataTrain, dataTest)

dataFeatures <- tbl_df(read.table(file.path(filesPath, "features.txt")))
setnames(dataFeatures, names(dataFeatures),      c("featureNumber", "featureName"))
colnames(dataTable) <- dataFeatures$featureName

activityLabels<- tbl_df(read.table(file.path(filesPath, "activity_labels.txt")))
setnames(activityLabels, names(activityLabels), c("activityNumber","activityName"))

####merging columns for Subject and Activity


alldataSubjAct<- cbind(alldataSubject, alldataActivity)
dataTable <- cbind(alldataSubjAct, dataTable)


##2. Extracts only the measurements on the mean and standard deviation for each measurement

dataFeaturesMeanStd <- grep("mean\\(\\)|std\\(\\)",dataFeatures$featureName,value=TRUE)

dataFeaturesMeanStd <- union(c("subject","activityNumber"), dataFeaturesMeanStd)

dataTable<- subset(dataTable,select=dataFeaturesMeanStd) 


##3. Uses descriptive activity names to name the activities in the data set

dataTable <- merge(activityLabels, dataTable , by="activityNumber", all.x=TRUE)

dataTable$activityName <- as.character(dataTable$activityName)


dataTable$activityName <- as.character(dataTable$activityName)

dataAggr<- aggregate(. ~ subject - activityName, data = dataTable, mean) 

dataTable<- tbl_df(arrange(dataAggr,subject,activityName))


##4. Appropriately labels the data set with descriptive variable names

1. leading t or f is based on time or frequency measurements.

2. Body = related to body movement.

3. Gravity = acceleration of gravity

4. Acc = accelerometer measurement

5. Gyro = gyroscopic measurements

6. Jerk = sudden movement acceleration

7. Mag = magnitude of movement

8. mean and SD are calculated for each subject for each activity for each mean and SD measurements.

The units given are g’s for the accelerometer and rad/sec for the gyro and g/sec and rad/sec/sec for the corresponding jerks.

###first looked at head of the data to see the name before the change 
head(str(dataTable),2)
###descriptive variable names

names(dataTable)<-gsub("std()", "SD", names(dataTable))

names(dataTable)<-gsub("mean()", "MEAN", names(dataTable))

names(dataTable)<-gsub("^t", "time", names(dataTable))

names(dataTable)<-gsub("^f", "frequency", names(dataTable))

names(dataTable)<-gsub("Acc", "Accelerometer", names(dataTable))

names(dataTable)<-gsub("Gyro", "Gyroscope", names(dataTable))

names(dataTable)<-gsub("Mag", "Magnitude", names(dataTable))

names(dataTable)<-gsub("BodyBody", "Body", names(dataTable))

###then to check again with the new discriptive names 

head(str(dataTable),6)

##5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject

##### creates the .txt file on the direcotory

write.table(dataTable, "TidyData.txt", row.name=FALSE)
