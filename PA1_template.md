# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

This code loads and prepares the dataset.


```r
activity <- read.csv("activity.csv", stringsAsFactors = FALSE)
activity$date <- as.Date(activity$date)
```



## What is mean total number of steps taken per day?

The histogram below shows the total number of steps taken each day.


```r
stepsPerDay <- aggregate(steps ~ date, data = activity, sum, na.rm = TRUE)
hist(stepsPerDay$steps, breaks = 18)
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 


Below are the mean and median numbers of steps taken per day.


```r
mean(stepsPerDay$steps)
```

```
## [1] 10766
```

```r
median(stepsPerDay$steps)
```

```
## [1] 10765
```



## What is the average daily activity pattern?

This is a time series plot of the average number of steps taken (averaged across all days) versus the 5-minute intervals (Index).


```r
stepsPerInterval <- aggregate(steps ~ interval, data = activity, mean, na.rm = TRUE)
plot(stepsPerInterval$steps, type = "l")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 


This is the maximum average number of steps in a time interval.


```r
stepsPerInterval[which.max(stepsPerInterval$steps), ]
```

```
##     interval steps
## 104      835 206.2
```



## Inputing missing values

### Rows with missing values

There is missing data in the activity data set.  First, let's find the number of missing observations.


```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```


The following code inputs the average number of steps for that time interval where there is missing data.


```r
activity2 <- activity
activity2$steps[is.na(activity2$steps)] <- stepsPerInterval$steps
stepsPerDay2 <- aggregate(steps ~ date, data = activity2, sum, na.rm = TRUE)
```


Finally, this code shows a histogram where the missing data is filled in, plus mean and median for the modified dataset.


```r
hist(stepsPerDay2$steps, breaks = 18)
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 

```r
mean(stepsPerDay2$steps)
```

```
## [1] 10766
```

```r
median(stepsPerDay2$steps)
```

```
## [1] 10766
```




## Are there differences in activity patterns between weekdays and weekends?

The following code adds a new variable to the modified dataset that determines whether the observations fall on a weekday or weekend.


```r
l <- weekdays(activity2$date) == "Lördag" | weekdays(activity2$date) == "Söndag"
activity2$day[l] <- "weekend"
activity2$day[!l] <- "weekday"
activity2$day <- factor(activity2$day)
weekend <- aggregate(steps ~ interval, data = activity2[activity2$day == "weekend", 
    ], mean, na.rm = TRUE)
weekday <- aggregate(steps ~ interval, data = activity2[activity2$day == "weekday", 
    ], mean, na.rm = TRUE)
```


Now let's show that in a panel plot panel plot of the data separated by weekday or weekend.


```r
par(mfrow = c(2, 1))
plot(weekend$steps, type = "l", main = "weekend", ylim = c(0, 250))
plot(weekday$steps, type = "l", main = "weekday", ylim = c(0, 250))
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 


