# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data
This code will load the input data


```r
data<-read.csv("D:\\CourseraCourses\\ReproducibleResearch\\activity.csv")
str(data)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```




## What is mean total number of steps taken per day?

This code does:
<li> Calculate the total number of steps taken per day
<li> Make a histogram of the total number of steps taken each day
<li> Calculate and report the mean and median of the total number of steps taken per day



```r
manipulated<-aggregate(steps ~ date, data, sum)
hist(manipulated$steps, col="red", xlab="Steps_sum", main="Steps per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

```r
mean_by_date<-mean(manipulated$steps)
median_by_date<-median(manipulated$steps)
mean_by_date
```

```
## [1] 10766.19
```

```r
median_by_date
```

```
## [1] 10765
```



## What is the average daily activity pattern?
This code does:

<li> Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
<li> Tells which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps


```r
manipulated_interval<-aggregate(steps ~ interval, data, mean)
plot(steps~interval, manipulated_interval, type = "l", xlab = "5-min interval", ylab = "Average across all Days", main = "Average number of steps taken", col = "blue")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

```r
manipulated_interval[which.max(manipulated_interval$steps), 1]
```

```
## [1] 835
```



## Imputing missing values
This code does:

<li> Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
<li> Fills in all of the missing values in the dataset, using the mean for that 5-minute interval
<li> Creates a new dataset that is equal to the original dataset but with the missing data filled in.
<li> Make a histogram of the total number of steps taken each day
<li> Calculate and report the mean and median total number of steps taken per day and show the difference in the values from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?



```r
missing_indexes<-which(is.na(data$steps))
number_of_missing_values<-length(missing_indexes)
data_new<-data
for(i in missing_indexes){
  data_new[i,1]<-as.integer(manipulated_interval[which(data[i,3]==manipulated_interval$interval),]$steps)
}
manipulated_new<-aggregate(steps ~ date, data_new, sum)
hist(manipulated_new$steps, col="red", xlab="Steps_sum", main="Steps per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

```r
mean_by_date_new<-mean(manipulated_new$steps)
median_by_date_new<-median(manipulated_new$steps)
mean_by_date - mean_by_date_new
```

```
## [1] 16.41819
```

```r
median_by_date - median_by_date_new
```

```
## [1] 124
```




## Are there differences in activity patterns between weekdays and weekends?

This code does:
<li> Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.
<li> Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 


```r
weekdays <- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")
data$day = as.factor(ifelse(is.element(weekdays(as.Date(data$date)),weekdays), "Weekday", "Weekend"))
manipulated_interval_day <- aggregate(steps ~ interval + day, data, mean)
library(lattice)
xyplot(manipulated_interval_day$steps ~ manipulated_interval_day$interval|manipulated_interval_day$day, main="Average Steps per Day by Interval",xlab="Interval", ylab="Steps",layout=c(1,2), type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 
