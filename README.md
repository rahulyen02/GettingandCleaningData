---
title: "Getting and Cleaning Data"
author: "Rahul"
date: "September 18, 2018"
output: html_document
keep_md: TRUE
---

```{r Main, echo=FALSE}
library(reshape2)

# Sourcing the labels and features data from the working directory to R
activity_labels <- as.character(read.table("activity_labels.txt")$V2)
features <- as.character(read.table("features.txt")$V2)

# Extracting the features on mean and standard Deviation
meanstd_features <- grep(".*mean.*|.*std.*", features)
meanstd_features.names <- features[meanstd_features]
meanstd_features.names <- gsub("-mean", "Mean", meanstd_features.names)
meanstd_features.names <- gsub("-std", "Std", meanstd_features.names)
meanstd_features.names <- gsub("[-()]", "", meanstd_features.names)

# Getting the training data into R from the working directory
train_X <- read.table("train/X_train.txt")
train_subject <- read.table("train/subject_train.txt")
train_Y <- read.table("train/Y_train.txt")
train_data <- cbind(train_subject, train_Y, train_X)

# Getting the test data into R from the working directory
test_X <- read.table("test/X_test.txt")
test_subject <- read.table("test/subject_test.txt")
test_Y <- read.table("test/Y_test.txt")
test_data <- cbind(test_subject, test_Y, test_X)

# Combining the train and test data
cons_data <- rbind(train_data, test_data)
colnames(cons_data) <- c("Subject", "Activity", meanstd_features.names)

cons_data$Activity <- factor(cons_data$Activity, levels = 1:6, labels = activity_labels)
cons_data$Subject <- as.factor(cons_data$Subject)

# Transforming the data into a lean table with Subject and Activity data as individual columns and all other columns under variable
cons_data_melt <- melt(cons_data, id = c("Subject", "Activity"))

# Calculating the mean
cons_data_mean <- dcast(cons_data_melt, Subject + Activity ~ variable, mean)

# Output is a tidy data in text format
write.table(cons_data_mean, "tidy.txt", row.names = FALSE, quote = FALSE)
```
