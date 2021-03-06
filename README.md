---
title: "Code Book"
output: 
  html_document:
    keep_md: true
---
# Course Project - Getting and Cleaning Data
### The "run_analysis.R" file performs the following tasks to achieve the tidy data output

## Step-1: Loading the required libraries in R

```r
library(reshape2)
```

## Step-2: Sourcing the labels and features data from the working directory to R

```r
activity_labels <- as.character(read.table("activity_labels.txt")$V2)
features <- as.character(read.table("features.txt")$V2)
```

## Step-3: Extracting the features on mean and standard Deviation

```r
meanstd_features <- grep(".*mean.*|.*std.*", features)
meanstd_features.names <- features[meanstd_features]
meanstd_features.names <- gsub("-mean", "Mean", meanstd_features.names)
meanstd_features.names <- gsub("-std", "Std", meanstd_features.names)
meanstd_features.names <- gsub("[-()]", "", meanstd_features.names)
```

## Step-4: Getting the train data into R from the working directory

```r
train_X <- read.table("train/X_train.txt")
train_subject <- read.table("train/subject_train.txt")
train_Y <- read.table("train/Y_train.txt")
train_data <- cbind(train_subject, train_Y, train_X)
```

## Step-5: Getting the test data into R from the working directory

```r
test_X <- read.table("test/X_test.txt")
test_subject <- read.table("test/subject_test.txt")
test_Y <- read.table("test/Y_test.txt")
test_data <- cbind(test_subject, test_Y, test_X)
```

## Step-6: Combining the train and test data and naming the columns

```r
cons_data <- rbind(train_data, test_data)
colnames(cons_data) <- c("Subject", "Activity", meanstd_features.names)
```

## Step-7: Converting the Activity row names into labels

```r
cons_data$Activity <- factor(cons_data$Activity, levels = 1:6, labels = activity_labels)
cons_data$Subject <- as.factor(cons_data$Subject)
```

## Step-8: Transforming the data into a lean table with Subject and Activity data as individual columns and all other columns under variable

```r
cons_data_melt <- melt(cons_data, id = c("Subject", "Activity"))
```
## Step-9: Calculating the mean

```r
cons_data_mean <- dcast(cons_data_melt, Subject + Activity ~ variable, mean)
```

## Step-10: Generating tidy data in text format

```r
write.table(cons_data_mean, "tidy.txt", row.names = FALSE, quote = FALSE)
```
