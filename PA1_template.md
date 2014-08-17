# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data

Loading data into R (file with the data must be in a current working directory):


```r
activity <- read.csv("activity.csv", header=T)
```

```
## Warning: cannot open file 'activity.csv': No such file or directory
```

```
## Error: cannot open the connection
```

## What is mean total number of steps taken per day?

Histogram presenting the total number of steps taken each day.


```r
steps_per_day <- aggregate(steps ~ date, data = activity, sum)
```

```
## Error: object 'activity' not found
```


```r
barplot(steps_per_day$steps, names.arg = steps_per_day$date, xlab = "Date", ylab = "Number of steps per day", main="Total number of steps taken each day")
```

```
## Error: object 'steps_per_day' not found
```

Mean of the total number of stpes taken per day amounts to:


```r
mean(steps_per_day$steps)
```

```
## Error: object 'steps_per_day' not found
```

Median for the total number of stpes taken per day is:


```r
median(steps_per_day$steps)
```

```
## Error: object 'steps_per_day' not found
```

## What is the average daily activity pattern?


```r
steps_interval <- aggregate(steps ~ interval, data = activity, mean)
```

```
## Error: object 'activity' not found
```

```r
plot(steps_interval, type = "l", xlab="Interval", ylab="Average number of steps", main="Average number of steps taken across all days")
```

```
## Error: object 'steps_interval' not found
```

The maximum number of steps is taken in:

```r
steps_interval$interval[which.max(steps_interval$steps)]
```

```
## Error: object 'steps_interval' not found
```
the 5-minute interval.

## Imputing missing values

Total number of rows with missing values amounts to:

```r
sum(is.na(activity))
```

```
## Error: object 'activity' not found
```

Filling all of the missing values:

```r
steps_average <- aggregate(steps ~ interval, data = activity, mean)
```

```
## Error: object 'activity' not found
```

```r
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

```
## Error: object 'activity' not found
```

Creating new dataset with the missing values filled in:

```r
activity_filled <- activity
```

```
## Error: object 'activity' not found
```

```r
activity_filled$steps <- impute_miss
```

```
## Error: object 'activity_filled' not found
```

Histogram presenting the total number of steps taken each day after imputing missing values:

```r
steps_per_day_wo_NAs <- aggregate(steps ~ date, data = activity_filled, sum)
```

```
## Error: object 'activity_filled' not found
```

```r
barplot(steps_per_day_wo_NAs$steps, names.arg = steps_per_day_wo_NAs$date, xlab = "Date", ylab = "Number of steps per day", main="Total number of steps taken each day after imputing NAs")
```

```
## Error: object 'steps_per_day_wo_NAs' not found
```

Mean of the total number of stpes taken per day amounts to:

```r
mean(steps_per_day_wo_NAs$steps)
```

```
## Error: object 'steps_per_day_wo_NAs' not found
```

Median for the total number of stpes taken per day is:

```r
median(steps_per_day_wo_NAs$steps)
```

```
## Error: object 'steps_per_day_wo_NAs' not found
```

## Are the diferences in activity patterns between weekdays and weekends?

Creating factor variable with two levels - "weekday" and "weekend":

```r
activity$date <- as.Date(activity$date, "%Y-%m-%d")
```

```
## Error: object 'activity' not found
```

```r
day <- weekdays(activity$date)
```

```
## Error: object 'activity' not found
```

```r
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
```

```
## Error: object 'activity' not found
```

```r
activity$day_type <- day_type
```

```
## Error: object 'activity' not found
```

```r
activity$day_type <- as.factor(activity$day_type)
```

```
## Error: object 'activity' not found
```

```r
steps_per_day_type <- aggregate(steps ~ interval + day_type, data = activity, mean)
```

```
## Error: object 'activity' not found
```

```r
names(steps_per_day_type) <- c("interval", "daylevel", "steps")
```

```
## Error: object 'steps_per_day_type' not found
```

Creating appropriate plots:

```r
library(lattice)
```

```
## Warning: package 'lattice' was built under R version 3.1.1
```

```r
xyplot(steps ~ interval | day_type, steps_per_day_type, type = "l", layout = c(1, 2), 
       xlab = "Interval", ylab = "Number of steps taken per day")
```

```
## Error: object 'steps_per_day_type' not found
```
