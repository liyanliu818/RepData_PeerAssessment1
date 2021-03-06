# This is a markdown file for Coursera Reproducible Research Assignment 1

##set global statement

```r
library(knitr)
opts_chunk$set(echo = TRUE, results = 'hold')
```

##Load required libraries and read in the data

```r
library(data.table)
library(ggplot2) 
#setwd("c:/Users/User/Documents/coursera")
opts_knit$set(base.dir = "c:/Users/User/Documents/coursera")
activity<-read.csv("activity.csv")
head(activity)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```

##Process the data and do a histogram on total number of steps

```r
totalSteps <- aggregate(steps ~ date, data = activity, sum, na.rm = TRUE)
hist(totalSteps$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

##Mean and median number of steps

```r
mean(totalSteps$steps)
median(totalSteps$steps)
```

```
## [1] 10766.19
## [1] 10765
```
* The mean total number of steps taken per day is 10766 steps.
* The median total number of steps taken per day is 10765 steps.

##Average daily activity pattern

```r
stepsInterval <- aggregate(steps ~ interval, data = activity, mean, na.rm = TRUE)
plot(steps ~ interval, data = stepsInterval, type = "l")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

##Which 5 minutes interval contains the maximam number of steps

```r
stepsInterval[which.max(stepsInterval$steps), ]$interval
```

```
## [1] 835
```
* it's the 835th interval

##Impute missing values

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```
* there are 2304 rows of missing data

##I filled in all of the missing values with the mean for that 5-minute interval. 

```r
interval2steps <- function(interval) {
    stepsInterval[stepsInterval$interval == interval, ]$steps
}
activityFilled <- activity  # Make a new dataset with the original data
count = 0  # Count the number of data filled in
for (i in 1:nrow(activityFilled)) {
    if (is.na(activityFilled[i, ]$steps)) {
        activityFilled[i, ]$steps <- interval2steps(activityFilled[i, ]$interval)
        count = count + 1
    }
}
cat("Total ", count, "NA values were filled.\n\r")
```

```
## Total  2304 NA values were filled.
## 
```
* Total  2304 NA values were filled.

## Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day.

```r
totalSteps2 <- aggregate(steps ~ date, data = activityFilled, sum)
hist(totalSteps2$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png) 

## mean / median number of steps per day

```r
mean(totalSteps2$steps)
median(totalSteps2$steps)
```

```
## [1] 10766.19
## [1] 10766.19
```
* The mean total number of steps taken per day is 10766 steps.
* The median total number of steps taken per day is 10766 steps.

##Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

* The mean value is the same as the value before imputing missing data because we put the mean value for that particular 5-min interval. The median value shows a little difference : but it depends on where the missing values are.

##Are there differences in activity patterns between weekdays and weekends?

```r
activityFilled$day = ifelse(as.POSIXlt(as.Date(activityFilled$date))$wday%%6 == 
    0, "weekend", "weekday")
activityFilled$day = factor(activityFilled$day, levels = c("weekday", "weekend"))
```

##Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:

```r
stepsInterval2 = aggregate(steps ~ interval + day, activityFilled, mean)
library(lattice)
xyplot(steps ~ interval | factor(day), data = stepsInterval2, aspect = 1/2, 
    type = "l")
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png) 
