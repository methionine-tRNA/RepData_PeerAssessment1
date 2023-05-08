Code for reading in the dataset and/or processing the data

    activity <- read.csv("activity.csv")

Histogram of the total number of steps taken each day

    activitySummarized <- activity %>%
      group_by(date) %>%
      summarize(steps = sum(steps))

    ggplot(activitySummarized, aes(x = ymd(date), y = steps)) +
      geom_bar(stat = "identity") +
      xlab("Date") +
      ylab("Steps")

    ## Warning: Removed 8 rows containing missing values (`position_stack()`).

![](PA1_files/figure-markdown_strict/unnamed-chunk-2-1.png)

Mean and median number of steps taken each day

    mean(na.omit(activitySummarized$steps))

    ## [1] 10766.19

    median(na.omit(activitySummarized$steps))

    ## [1] 10765

Time series plot of the average number of steps taken

    plot(ymd(activitySummarized$date), activitySummarized$steps, type = "l", xlab = "Date", ylab = "Steps")

![](PA1_files/figure-markdown_strict/unnamed-chunk-4-1.png)

The 5-minute interval that, on average, contains the maximum number of
steps

    activitySteps <- activity %>%
      group_by(interval) %>%
      summarize(averageSteps = mean(na.omit(steps)))

    activitySteps[which(activitySteps$averageSteps == max(activitySteps$averageSteps)), ]$interval

    ## [1] 835

Code to describe and show a strategy for imputing missing data

    activityImputed <- activity
    activityImputed$steps[is.na(activityImputed$steps)] <- mean(na.omit((activity$steps)))

Histogram of the total number of steps taken each day after missing
values are imputed

    ggplot(activityImputed, aes(x = ymd(date), y = steps)) +
      geom_bar(stat = "identity") +
      xlab("Date") +
      ylab("Steps")

![](PA1_files/figure-markdown_strict/unnamed-chunk-7-1.png)

Panel plot comparing the average number of steps taken per 5-minute
interval across weekdays and weekends

    activity$weekend <- if_else(wday(activity$date) %in% c(1, 7), "Weekend", "Weekday")

    activityWeekend <- group_split(activity, weekend)[[1]] %>%
      group_by(interval) %>%
      summarize(averageSteps = mean(na.omit(steps)))
    activityWeekend$weekend <- "Weekend"

    activityWeekdays <- group_split(activity, weekend)[[2]] %>%
      group_by(interval) %>%
      summarize(averageSteps = mean(na.omit(steps)))
    activityWeekdays$weekend <- "Weekday"

    activityWeek <- rbind(activityWeekend, activityWeekdays)

    ggplot(activityWeek, aes(x = interval, y = averageSteps)) +
      geom_bar(stat = "identity") +
      facet_wrap(~weekend) +
      xlab("Interval") +
      ylab("Steps")

![](PA1_files/figure-markdown_strict/unnamed-chunk-8-1.png)
