---
output: 
  html_document: 
    keep_md: yes
---
## Reproducible Research Project 1

Loading and preprocessing the data

```r
setwd("C:/Users/Umang Shah/Documents")
unzip("repdata_data_activity.zip")
d1 <- read.csv("activity.csv")
```

**What is mean total number of steps taken per day?**  
1. Calculate the total number of steps taken per day

```r
d1$date <- as.Date(d1$date, "%Y-%m-%d")
library(dplyr)
```

```
## Warning: package 'dplyr' was built under R version 3.6.1
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
d2 <- summarise(group_by(d1, date), steps = sum(steps, na.rm = TRUE))
View(d2)
```

2. Histogram of the total number of steps taken each day

```r
library(ggplot2)
```

```
## Registered S3 methods overwritten by 'ggplot2':
##   method         from 
##   [.quosures     rlang
##   c.quosures     rlang
##   print.quosures rlang
```

```r
ggplot(data = d2, aes(steps)) + geom_histogram(fill = "blue") + ggtitle("Histogram of Total Steps Taken Each Day")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

3. The mean and median of the total number of steps taken per day.

```r
me <- mean(d2$steps)
md <- median(d2$steps)
```
The Mean is 9354.2295082 and Median is 10395

**What is the average daily activity pattern?**
1. a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis).

```r
d3 <- summarise(group_by(d1, interval), steps = mean(steps, na.rm = TRUE))
with(d3, plot(interval, steps, type = "l", xlab = "Time Intervals", ylab = "Average Number of Steps Taken", main = "Time Series Plot of Time Interval Vs Average Steps Taken", col = "red"))
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
max.time <- d3[which.max(d3$steps),1]
```
The 5 min interval with maximum number of steps averaged across all the days is 835

**Imputing missing values**
1.  The total number of missing values in the dataset

```r
total.na <- sum(is.na(d1$steps))
```
The total NA's are 2304

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated.You could use the mean/median for that day, or the mean for that 5-minute interval, etc.
Ans == Using the ave() function

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
d4 <- d1
d4$steps <- ave(d4$steps, d4$interval, FUN = function(x)
ifelse(is.na(x), mean(x,na.rm=TRUE), x))
View(d4)
```
4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day

```r
d5 <- summarise(group_by(d4, date), steps = sum(steps))
ggplot(data = d5, aes(steps)) + geom_histogram(fill = "blue") + ggtitle("Histogram of Total Steps Taken Each Day Missing Values Imputed")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
men <- mean(d5$steps)
mdn <- median(d5$steps)
```
The Mean = 1.0766189\times 10^{4} and Median = 1.0766189\times 10^{4} after imputing the data.

**Are there differences in activity patterns between weekdays and weekends?**
1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
d4$day <- weekdays(d4$date)
d4$day <- ifelse(d4$day=="Saturday"|d4$day=="Sunday", "Weekend", "Weekday")
d4$day <- as.factor(d4$day)
View(d4)
```
2.Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```r
d6 <- summarise(group_by(d4, day, interval), steps = mean(steps))

ggplot(data = d6, aes(interval, steps)) + geom_line(color = "blue") + facet_grid(day~.)
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->












