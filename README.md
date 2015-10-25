# GettingAndCleaningData
I describe in the file run_analysis.R a lot of step but i copy here the same code

##############################################
# Step 1 - Merges the training and the test sets to create one data set.
##############################################

# Load and Merges the datase training and the test sets to create one data set.

x_train <- read.table('./UCI HAR Dataset/train/X_train.txt')
x_test <- read.table('./UCI HAR Dataset/test/X_test.txt')
x <- rbind(x_train, x_test)

subject_train <- read.table('./UCI HAR Dataset/train/subject_train.txt')
subject_test <- read.table('./UCI HAR Dataset/test/subject_test.txt')
subject <- rbind(subject_train, subject_test)

y_train <- read.table('./UCI HAR Dataset/train/y_train.txt')
y_test <- read.table('./UCI HAR Dataset/test/y_test.txt')
y <- rbind(y_train, y_test)


# Read the name of columns for feature
featureNames <- read.table("./UCI HAR Dataset/features.txt")


# Put the name of columns of the dataset

colnames(x) <- t(featureNames[2])
colnames(y) <- 'activity'
colnames(subject) <- 'subject'

# Create the dataset for the exam 
esameData <- cbind(x,y,subject)


##############################################
# Step 2 - Extracts only the measurements on the mean and standard deviation for each measurement
##############################################


# Select the column Mean Std and Activity and Subject 

mean_sd_act_sub_cols <- grep(".*Mean.*|.*Std.*|activity|subject", names(esameData),ignore.case=TRUE)

step2Data <- esameData[,mean_sd_act_sub_cols]


##############################################
# Step 3 - Uses descriptive activity names to name the activities in the data set
##############################################

#Load the activity names

activityLabels <- read.table("./UCI HAR Dataset/activity_labels.txt")

#Decode the id_activity with the description label 

step3Data<-mutate(step2Data,activity=activityLabels[activity,2])


##############################################
# Step 4 - Appropriately labels the data set with descriptive variable names
##############################################


step4data<-step3Data


#the fist caratter t can be replaced with Time
names(step4data)<-gsub("^t", "time-", names(step4data))
#the fist caratter f can be replaced with frequency_
names(step4data)<-gsub("^f", "frequency-", names(step4data))
#put the name in lowercase
names(step4data) <- tolower(names(step4data))
#remove this caracter ( )
names(step4data) <- gsub("\\(|\\)", "", names(step4data))
#replace , with -
names(step4data) <- gsub(",","-", names(step4data))



##############################################
# Step 5 - From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
##############################################

#make the mean of ariable for each activity and each subject

step5data <- aggregate(. ~activity+subject , step4data, mean)

write.table(step5data, 'tidydataaverage.txt', row.names = F)


