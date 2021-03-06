# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data

Loading data into R:


```r
activity <- read.csv("activity.csv", header=T)
```

## What is mean total number of steps taken per day?

Histogram presenting the total number of steps taken each day.


```r
steps_per_day <- aggregate(steps ~ date, data = activity, sum)
```


```r
barplot(steps_per_day$steps, names.arg = steps_per_day$date, xlab = "Date", ylab = "Number of steps per day", main="Total number of steps taken each day")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 

Mean of the total number of stpes taken per day amounts to:


```r
mean(steps_per_day$steps)
```

```
## [1] 10766
```

Median for the total number of stpes taken per day is:


```r
median(steps_per_day$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?


```r
steps_interval <- aggregate(steps ~ interval, data = activity, mean)
```

```r
plot(steps_interval, type = "l", xlab="Interval", ylab="Average number of steps", main="Average number of steps taken across all days")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7.png) 

The maximum number of steps is taken in:

```r
steps_interval$interval[which.max(steps_interval$steps)]
```

```
## [1] 835
```
the 5-minute interval.

## Imputing missing values

Total number of rows with missing values amounts to:

```r
sum(is.na(activity))
```

```
## [1] 2304
```

Filling all of the missing values:

```r
steps_average <- aggregate(steps ~ interval, data = activity, mean)
impute_miss <- numeric()
for (i in 1:nrow(activity)) {
    case <- activity[i, ]
    if (is.na(case$steps)) {
        steps <- subset(steps_average, interval == case$interval)$steps
    } else {
        steps <- case$steps
    }
    impute_miss <- c(impute_miss, steps)
}
```

Creating new dataset with the missing values filled in:

```r
activity_filled <- activity
activity_filled$steps <- impute_miss
```

Histogram presenting the total number of steps taken each day after imputing missing values:

```r
steps_per_day_wo_NAs <- aggregate(steps ~ date, data = activity_filled, sum)
```

```r
barplot(steps_per_day_wo_NAs$steps, names.arg = steps_per_day_wo_NAs$date, xlab = "Date", ylab = "Number of steps per day", main="Total number of steps taken each day after imputing NAs")
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13.png) 

Mean of the total number of stpes taken per day amounts to:

```r
mean(steps_per_day_wo_NAs$steps)
```

```
## [1] 10766
```

Median for the total number of stpes taken per day is:

```r
median(steps_per_day_wo_NAs$steps)
```

```
## [1] 10766
```

## Are the diferences in activity patterns between weekdays and weekends?

Creating factor variable with two levels - "weekday" and "weekend":

```r
activity$date <- as.Date(activity$date, "%Y-%m-%d")
day <- weekdays(activity$date)
day_type <- vector()
for (i in 1:nrow(activity)) {
  if (day[i] == "Saturday") {
    day_type[i] <- "Weekend"
  } else if (day[i] == "Sunday") {
    day_type[i] <- "Weekend"
  } else {
    day_type[i] <- "Weekday"
  }
}
activity$day_type <- day_type
activity$day_type <- factor(activity$day_type)

steps_per_day_type <- aggregate(steps ~ interval + day_type, data = activity, mean)
names(steps_per_day_type) <- c("interval", "day_type", "steps")
```

Creating appropriate plots:

```r
library(lattice)
xyplot(steps ~ interval | day_type, steps_per_day_type, type = "l", layout = c(1, 2), 
       xlab = "Interval", ylab = "Number of steps taken per day")
```

![plot of chunk unnamed-chunk-17](figure/unnamed-chunk-17.png) 
