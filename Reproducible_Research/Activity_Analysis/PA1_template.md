Analysis of Activity from Monitoring Devices
========================================================

Introduction
--------------------------------------------------------

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a [Fitbit](http://www.fitbit.com/de), [Nike Fuelband](http://www.nike.com/us/en_us/c/nikeplus-fuelband), or [Jawbone Up](https://jawbone.com/up). These type of devices are part of the "quantified self" movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.



Data
--------------------------------------------------------

Dataset: [Activity monitoring data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip) [52K]

The variables included in this dataset are:

* **steps**: Number of steps taking in a 5-minute interval (missing values are coded as <span style="color: red">`NA`</span>)
* **date**: The date on which the measurement was taken in YYYY-MM-DD format
* **interval**: Identifier for the 5-minute interval in which measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

Assingment
--------------------------------------------------------

This assignment will be described in multiple parts. You will need to write a report that answers the questions detailed below. Ultimately, you will need to complete the entire assignment in a single R markdown document that can be processed by knitr and be transformed into an HTML file.

Throughout your report make sure you always include the code that you used to generate the output you present. When writing code chunks in the R markdown document, always use <span style="color: red">`echo = TRUE`</span> so that someone else will be able to read the code. This assignment will be evaluated via peer assessment so it is essential that your peer evaluators be able to review the code for your analysis.

For the plotting aspects of this assignment, feel free to use any plotting system in R (i.e., base, lattice, ggplot2)

Fork/clone the GitHub repository created for this assignment. You will submit this assignment by pushing your completed files into your forked repository on GitHub. The assignment submission will consist of the URL to your GitHub repository and the SHA-1 commit ID for your repository state.

NOTE: The GitHub repository also contains the dataset for the assignment so you do not have to download the data separately.

Analysis
--------------------------------------------------------

### Step 1: Load Libraries and dataset

Load the data (i.e. <span style="color: red">`read.csv()`</span>).


```r
#### 1 Load libraries and dataset

# load libraries
library(ggplot2)  # plotting data
library(plyr)  # transform data

# to print big numbers
options(scipen = 20)

# read data
data <- read.csv(file = "activity.csv", header = TRUE, stringsAsFactors = FALSE)

# display structure of the data
str(data)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : chr  "2012-10-01" "2012-10-01" "2012-10-01" "2012-10-01" ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```



### Step 2: Pre-Process / Transform the data

Process/transform the data (if necessary) into a format suitable for your analysis.


```r
#### 2 Pre-Process / Transform the data

# transform to Date data type
data$date <- as.Date(data$date, format = "%Y-%m-%d")

# display structure of the data
str(data)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Date, format: "2012-10-01" "2012-10-01" ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```


### Step 3: Answer questions

#### What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1. Make a histogram of the total number of steps taken each day

2. Calculate and report the mean and median total number of steps taken per day


```r
#### 3 Answer questions

#### What is mean total number of steps taken per day?

# summary of the data
summary(data)
```

```
##      steps            date               interval   
##  Min.   :  0.0   Min.   :2012-10-01   Min.   :   0  
##  1st Qu.:  0.0   1st Qu.:2012-10-16   1st Qu.: 589  
##  Median :  0.0   Median :2012-10-31   Median :1178  
##  Mean   : 37.4   Mean   :2012-10-31   Mean   :1178  
##  3rd Qu.: 12.0   3rd Qu.:2012-11-15   3rd Qu.:1766  
##  Max.   :806.0   Max.   :2012-11-30   Max.   :2355  
##  NA's   :2304
```

```r
# mean = 37.38 median = 0

# calculate the mean and the median we will use them to plot vertical lines
# in the histogram
(mean.data <- mean(data$steps, na.rm = TRUE))
```

```
## [1] 37.38
```

```r
(median.data <- median(data$steps, na.rm = TRUE))
```

```
## [1] 0
```

```r

# plot histogram of steps adding vertical lines for mean and median
ggplot(data, aes(x = steps)) + geom_histogram(colour = "darkgreen", fill = "white") + 
    geom_vline(xintercept = mean.data, col = "red", show_guide = TRUE, linetype = "longdash") + 
    geom_vline(xintercept = median.data, col = "green", show_guide = TRUE, linetype = "longdash") + 
    annotate("text", x = median.data + 1, y = 13000, label = paste(c("Median="), 
        round(median.data, 0), sep = ""), hjust = 0, col = "green") + annotate("text", 
    x = mean.data + 1, y = 10000, label = paste(c("Mean="), round(mean.data, 
        0), sep = ""), hjust = 0, col = "red")
```

```
## stat_bin: binwidth defaulted to range/30. Use 'binwidth = x' to adjust this.
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 



#### What is the average daily activity pattern?

1. Make a time series plot (i.e. span style="color: red">`type = "l"`</span>) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
#### What is the average daily activity pattern?

# average the number of steps by interval for all days
agg.data <- ddply(data, .(interval), summarize, mean = mean(steps, na.rm = TRUE))

# get the interval where the maximum mean value is and the value of the
# average number of steps
interval.max.mean <- agg.data$interval[which(agg.data$mean == max(agg.data$mean))]
value.max.mean <- round(max(agg.data$mean), 2)

# plot average number of step by interval for all days with vertical line
# showing the maximum value
ggplot(agg.data, aes(x = interval, y = mean)) + geom_line(type = "l") + geom_vline(xintercept = interval.max.mean, 
    col = "red", show_guide = TRUE, linetype = "longdash") + annotate("text", 
    x = interval.max.mean + 1, y = 200, label = paste(c("Interval="), round(interval.max.mean, 
        0), " ; Mean=", value.max.mean, sep = ""), hjust = 0, col = "red")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 


#### Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as <span style="color: red">`NA`</span>). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
# check how many observations are missing
sum(is.na(data$steps))
```

```
## [1] 2304
```

```r
# get the indexes where the NA's are
index.na <- which(is.na(data.wo.na$steps))
```

```
## Error: object 'data.wo.na' not found
```

```r
# check in which dates are the missing values
table(data.wo.na$date[index.na])
```

```
## Error: object 'data.wo.na' not found
```

```r
# check in which intervals are the missing values
table(data.wo.na$interval[index.na])
```

```
## Error: object 'data.wo.na' not found
```



2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


```r
# mean values for each interval we will use it to fill in the missing values
mean.interval <- ddply(data, .(interval), summarize, mean = mean(steps, na.rm = TRUE))
```


3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
# copy data to a new data frame where we fill the missing values with the
# mean value for each interval
data.wo.na <- data

# for each NA fill the missing value with the mean for that interval
for (i in index.na) {
    # print(i)
    data.wo.na$steps[i] <- mean.interval$mean[mean.interval$interval == data.wo.na$interval[i]]
}
```

```
## Error: object 'index.na' not found
```

```r
# check how many observations are missing
sum(is.na(data.wo.na$steps))  # 0 missing values!
```

```
## [1] 2304
```


4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
#### 3 Answer questions

#### What is mean total number of steps taken per day?

# summary of the data
summary(data.wo.na)
```

```
##      steps            date               interval   
##  Min.   :  0.0   Min.   :2012-10-01   Min.   :   0  
##  1st Qu.:  0.0   1st Qu.:2012-10-16   1st Qu.: 589  
##  Median :  0.0   Median :2012-10-31   Median :1178  
##  Mean   : 37.4   Mean   :2012-10-31   Mean   :1178  
##  3rd Qu.: 12.0   3rd Qu.:2012-11-15   3rd Qu.:1766  
##  Max.   :806.0   Max.   :2012-11-30   Max.   :2355  
##  NA's   :2304
```

```r
# mean = 37.38 median = 0

# calculate the mean and the median we will use them to plot vertical lines
# in the histogram
(mean.data <- mean(data.wo.na$steps, na.rm = TRUE))
```

```
## [1] 37.38
```

```r
(median.data <- median(data.wo.na$steps, na.rm = TRUE))
```

```
## [1] 0
```

```r

# plot histogram of steps adding vertical lines for mean and median
ggplot(data.wo.na, aes(x = steps)) + geom_histogram(colour = "darkgreen", fill = "white") + 
    geom_vline(xintercept = mean.data, col = "red", show_guide = TRUE, linetype = "longdash") + 
    geom_vline(xintercept = median.data, col = "green", show_guide = TRUE, linetype = "longdash") + 
    annotate("text", x = median.data + 1, y = 13000, label = paste(c("Median="), 
        round(median.data, 0), sep = ""), hjust = 0, col = "green") + annotate("text", 
    x = mean.data + 1, y = 10000, label = paste(c("Mean="), round(mean.data, 
        0), sep = ""), hjust = 0, col = "red")
```

```
## stat_bin: binwidth defaulted to range/30. Use 'binwidth = x' to adjust this.
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 


The values do not differ as the missing values are spread evenly across all the intervals, so replacing mising values with the mean for the interval does not change the results in this case.



#### Are there differences in activity patterns between weekdays and weekends?

For this part the <span style="color: red">`weekdays()`</span>) function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels – "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
# get the weekday for the given data
data.wo.na$weekday <- weekdays(data.wo.na$date, abbreviate = TRUE)
# create a factor variable 'weekday' / 'weekend'
data.wo.na$datetype <- ifelse(data.wo.na$weekday %in% c("Sat", "Sun"), "weekend", 
    "weekday")
data.wo.na$datetype <- as.factor(data.wo.na$datetype)
# display structure of the data
str(data.wo.na)
```

```
## 'data.frame':	17568 obs. of  5 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Date, format: "2012-10-01" "2012-10-01" ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
##  $ weekday : chr  "Mon" "Mon" "Mon" "Mon" ...
##  $ datetype: Factor w/ 2 levels "weekday","weekend": 1 1 1 1 1 1 1 1 1 1 ...
```



2. Make a panel plot containing a time series plot (i.e. <span style="color: red">`type = "l"`</span>) ) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 


```r
# average the number of steps by interval for all days
agg.data <- ddply(data.wo.na, .(interval, datetype), summarize, mean = mean(steps, 
    na.rm = TRUE))
# plot average number of steps by interval and weekday/weekend
ggplot(agg.data, aes(x = interval, y = mean)) + geom_line(type = "l") + facet_grid(datetype ~ 
    .)
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-101.png) 

```r
ggplot(agg.data, aes(x = interval, y = mean, col = datetype)) + geom_line(type = "l")
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-102.png) 


We can clearly see the differences in activity between weekdays and weekends:

1. Interval between `[0,500]` (aprox. 00h-08h) there is not too much difference as it is sleeping time 
2. Interval between `[500-900]` (aprox. 08h-15h) there is more activity during weekday, as it is work time, in weekends seems we need more time to activate as we sleep longer.
3. After interval `1000` (aprox 16h) there is more activity on weekends.

