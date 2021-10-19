github repo for the record are all within [Hsin Chih Chen’s R Learn
Repo](https://github.com/hsc251/RLearn)

## Introduction

This assignment’s data sources comes from personal activity monitoring
device, the device collects data at 5 mins interval throughout the day.
The data consists of 2 months of data from an annoymous individual
collected between Oct and Nov 2012 while including the number of steps
taken in 5 minutes intervals per day.

The data consists of the following variables: \* Steps: Number of steps
taking in a 5 min interval (missing values are coded as NA) \* date: The
date on which the measurement was taken in YYYY-MM-DD format. \*
interval: Identifier for the 5 mins interval in which measurement was
taken.

The dataset is saved as a CSV file and total of 17,568 observations.
Please refer to the [dataset
link](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip)
to download.

## Critical Steps for the project

-   1: Execute the environment and data gathering setup before deploy
    data download & transformation.

-   2: Calculate total number of steps taken per day, this requires data
    manipulation by filtering desired context while formatting the data
    into the desired condition before calculate the data. Functions such
    as subsetting and lapply is essential for the data table extract.

-   3: Replace the missing values in the dataset, then export into a
    completed csv file before reload into the updated database for
    comparison analysis.

-   4: Transform date structure by arranging the date into POSIXct date
    while add additional factors so weekdays and weekends can be
    separated.

-   5: Plot the respective plots and obtain the desired distribution
    before making valid conclusion.

## Execute the environment and data gathering setup before deploy data download & transformation.

``` r
## Load necessary libraries
library(data.table)
library(ggplot2)

## Set URL and download file into designated directory
PA1URL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(PA1URL, "PA01.zip",method = "curl")
unzip("PA01.zip", exdir = "PA01")

## Read csv file into data.table
activity <- fread(input = "PA01/activity.csv")
```

## Calculate mean total number of steps taken per day?

1.  Calculate total number of steps taken per day?

``` r
## pre-check the data.table structure
str(activity)
```

    ## Classes 'data.table' and 'data.frame':   17568 obs. of  3 variables:
    ##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ date    : IDate, format: "2012-10-01" "2012-10-01" ...
    ##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
    ##  - attr(*, ".internal.selfref")=<externalptr>

``` r
## Use the lapply for the sum function (false inclusive) to calculate the total steps per day. 
Total_S <- activity[,c(lapply(.SD, sum, na.rm = FALSE)), .SDcols = c("steps"), by = .(date)]

## demonstrate the extracted data layout.
head(Total_S,10)
```

    ##           date steps
    ##  1: 2012-10-01    NA
    ##  2: 2012-10-02   126
    ##  3: 2012-10-03 11352
    ##  4: 2012-10-04 12116
    ##  5: 2012-10-05 13294
    ##  6: 2012-10-06 15420
    ##  7: 2012-10-07 11015
    ##  8: 2012-10-08    NA
    ##  9: 2012-10-09 12811
    ## 10: 2012-10-10  9900

1.  Draw the histogram for the particular setup

``` r
## Setup the png file for output display.
png("05_PA1_plot1.png")

## use ggplot to initiate the setup for bar plot
ggplot(Total_S, aes(x = steps)) +
  geom_histogram(fill = "#BB0000", binwidth = 1000) +
  labs(title = "Daily Steps Distribution", xlab = "Steps", y = "Frequency")
```

    ## Warning: Removed 8 rows containing non-finite values (stat_bin).

``` r
## close png write in.
dev.off()
```

    ## png 
    ##   2

1.  Calculate & report the mean and median of total number of steps
    taken per day.

``` r
Total_S[,.(Steps_Avg = mean(steps, na.rm = TRUE), Steps_Med = median(steps, na.rm = TRUE))]
```

    ##    Steps_Avg Steps_Med
    ## 1:  10766.19     10765

## What’s the average daily activity pattern?

1.  Generate a time series plot of the 5 min interval and average steps
    taken, averaged across the entire days

``` r
## pre-check the data.table structure
str(activity)
```

    ## Classes 'data.table' and 'data.frame':   17568 obs. of  3 variables:
    ##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ date    : IDate, format: "2012-10-01" "2012-10-01" ...
    ##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
    ##  - attr(*, ".internal.selfref")=<externalptr>

``` r
## Use the lapply to calculate the total steps per day by interval 
Inter <- activity[,c(lapply(.SD, mean, na.rm = TRUE)), .SDcols = c("steps"), by = .(interval)]

## Setup the png file for output display.
png("05_PA1_plot2.png")

## use ggplot to initiate the setup for line plot.
ggplot(Inter, aes(x = interval, y = steps)) +
  geom_line(color = "#BB0000", size = 1) +
  labs(title = "Average Daily Steps", xlab = "Interval", y = "Avg. Steps per Day")

## close png write in.
dev.off()
```

    ## png 
    ##   2

``` r
## Display the top 10 rows of the setup.
head(Total_S,10)
```

    ##           date steps
    ##  1: 2012-10-01    NA
    ##  2: 2012-10-02   126
    ##  3: 2012-10-03 11352
    ##  4: 2012-10-04 12116
    ##  5: 2012-10-05 13294
    ##  6: 2012-10-06 15420
    ##  7: 2012-10-07 11015
    ##  8: 2012-10-08    NA
    ##  9: 2012-10-09 12811
    ## 10: 2012-10-10  9900

1.  Which 5-mins interval, on average across all the days in the
    dataset, contains the maximum number of steps?

``` r
Inter[steps == max(steps), .(max_inter = interval)]
```

    ##    max_inter
    ## 1:       835

## Imputing missing values

1.  Calculate and report the total number of missing values in the
    dataset (i.e. the total number of rows with NAs)

``` r
## Count amount of rows where the steps are missing in the column.
nrow(activity[is.na(steps),])
```

    ## [1] 2304

1.  Use either the mean or median while deploying the value to replace
    the current missing values

``` r
## Fill the missing values by subsetting the steps' mean value .
activity[is.na(steps), "steps"] <- activity[, c(lapply(.SD, mean, na.rm = TRUE)), .SDcols = c("steps")]
```

    ## Warning in `[<-.data.table`(`*tmp*`, is.na(steps), "steps", value =
    ## structure(list(: 37.382600 (type 'double') at RHS position 1 truncated
    ## (precision lost) when assigning to type 'integer' (column 1 named 'steps')

1.  Generate the new data set which is equivalent to original set except
    with filled missing values.

``` r
## use fwrite to write out the data set and saved as a different format
fwrite(x = activity, file = "PA01/fixed.csv", quote = FALSE)
```

1.  Create histogram of total number of steps taken each day and report
    the mean and median total number of steps taken per day. Do these
    values differ from the estimates from the first part of the
    assignment? What is the impact of imputing missing data on the
    estimates of the total daily number of steps?

``` r
## total number of steps per day
Total_S2 <- activity[, c(lapply(.SD, sum)), .SDcols = c("steps"), by = .(date)]

## mean & median total number of steps taken per day based on updated data
Total_S2[,.(Steps_Avg = mean(steps), Steps_Med = median(steps))]
```

    ##    Steps_Avg Steps_Med
    ## 1:  10751.74     10656

``` r
## Setup the png file for output display.
png("05_PA1_plot3.png")

## use ggplot to initiate the setup for bar plot
ggplot(Total_S2, aes(x = steps)) +
  geom_histogram(fill = "#BB0000", binwidth = 1000) +
  labs(title = "Daily Steps Distribution", xlab = "Steps", y = "Frequency")

## close png write in.
dev.off()
```

    ## png 
    ##   2

## Are there differences in activity patterns between weekdays and weekends?

1.  Create new factor variable in dataset with 2 levels to identify
    weekdays and weekends, this requires

``` r
## Read off the original CSV again before manipulate the dataset
active2 <- fread(input = "PA01/activity.csv")

## Check current dataset structure from the actual CSV
str(active2)
```

    ## Classes 'data.table' and 'data.frame':   17568 obs. of  3 variables:
    ##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ date    : IDate, format: "2012-10-01" "2012-10-01" ...
    ##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
    ##  - attr(*, ".internal.selfref")=<externalptr>

``` r
## Reconstruct the data format for designated setup (in this case POSI date)
active2[, date := as.POSIXct(date, format = "%Y-%m-%d")]
active2[, `Day of Week` := weekdays(x = date)]

## Use grepl to search similar pattern strings and categorize them as factors
active2[grepl(pattern = "Monday|Tuesday|Wednesday|Thursday|Friday", x = `Day of Week`), "weekday or weekend"] <- "weekday"
active2[grepl(pattern = "Saturday|Sunday", x = `Day of Week`), "weekday or weekend"] <- "weekend"
active2[, `weekday or weekend` := as.factor(`weekday or weekend`)]
head(active2,10)
```

    ##     steps       date interval Day of Week weekday or weekend
    ##  1:    NA 2012-10-01        0      Monday            weekday
    ##  2:    NA 2012-10-01        5      Monday            weekday
    ##  3:    NA 2012-10-01       10      Monday            weekday
    ##  4:    NA 2012-10-01       15      Monday            weekday
    ##  5:    NA 2012-10-01       20      Monday            weekday
    ##  6:    NA 2012-10-01       25      Monday            weekday
    ##  7:    NA 2012-10-01       30      Monday            weekday
    ##  8:    NA 2012-10-01       35      Monday            weekday
    ##  9:    NA 2012-10-01       40      Monday            weekday
    ## 10:    NA 2012-10-01       45      Monday            weekday

1.  Make plot containing time series plot based on the weekend or
    weekday data.

``` r
## Replace the data with average of steps first
active2[is.na(steps), "steps"] <- active2[, c(lapply(.SD, mean, na.rm = TRUE)), .SDcols = c("steps")]
```

    ## Warning in `[<-.data.table`(`*tmp*`, is.na(steps), "steps", value =
    ## structure(list(: 37.382600 (type 'double') at RHS position 1 truncated
    ## (precision lost) when assigning to type 'integer' (column 1 named 'steps')

``` r
## Execute the interval data while sorting by interval with weekday or weekend
Inter2 <- active2[, c(lapply(.SD, mean, na.rm = TRUE)), .SDcols = c("steps"), by = .(interval, `weekday or weekend`)] 

## Setup the png file for output display.
png("05_PA1_plot4.png")

## Plot
ggplot(Inter2 , aes(x = interval , y = steps, color=`weekday or weekend`)) + geom_line() + labs(title = "Avg. Daily Steps by Week Type", x = "Interval", y = "No. of Steps") + facet_wrap(~`weekday or weekend` , ncol = 1, nrow=2)

## close png write in.
dev.off()
```

    ## png 
    ##   2