# PA1_templateIG
Ismael Gómez  
Sunday, January 10, 2016  
        



```r
        library(dplyr)
        library(ggplot2)
```
        
# Loading and preprocessing the data

```r
rawdata<-read.csv("activity.csv",sep=",",header=T,stringsAsFactors=F)
rawdata[,2]<-as.Date(rawdata$date,"%Y-%m-%d")
j<-0
for (i in 1:length(rawdata[,3]))
{
        rawdata[i,3]<-5*j
        j<-j+1
        if (j==288)
        {
                j<-0        
        }        
}        
```
          
# What is mean total number of steps taken per day?

```r
TotalSteps<-tapply(rawdata$steps,rawdata$date,sum)
hist(TotalSteps,col="red",main="Total steps taken per day")
```

![](Figs/hist-1.png)\

```r
Mean<-mean(TotalSteps,na.rm=T)
Median<-median(TotalSteps,na.rm=T)
```
### The mean of total number of steps taken per day is 1.0766189\times 10^{4}  
### The median of total number of steps taken per day is 10765
          
# What is the average daily activity pattern?

```r
DailyAverage<-tapply(rawdata$steps,rawdata$interval,mean,na.rm=T)
plot(x<-0:1:287,DailyAverage,type="l",xlab="5 minutes representantive daily interval")
```

![](Figs/unnamed-chunk-2-1.png)\

```r
IndexMaxDA<-as.numeric(which.max(DailyAverage))
```
### The 5-minute interval N° 104 contains the maximum number of steps on average across all the days in the dataset.  
### There is more activity in the mornings, roughly at 9:00 am.
          
# Imputing missing values

```r
TotalNA<-sum(is.na(rawdata$steps))
```
### The total number of mssing values is 2304  
          
##Filling the missing values with the mean for each 5-minute interval:

```r
newdata<-rawdata
for (i in 1:length(DailyAverage))
{
        if (is.na(newdata[i,1])==T)
        {
                newdata[i,1] = DailyAverage[i] 
        }     
}
TotalSteps2<-tapply(newdata$steps,newdata$date,sum)
hist(TotalSteps2,col="blue",main="Total steps taken per day (NA filled)")
```

![](Figs/hist2-1.png)\

```r
Mean2<-mean(TotalSteps2,na.rm=T)
Median2<-median(TotalSteps2,na.rm=T)
```
### The mean of total number of steps taken per day, with dataset with the filled-in missing values, is 1.0766189\times 10^{4}  
### The median of total number of steps taken per day, with dataset with the filled-in missing values, is 1.0765594\times 10^{4}  
          
### The results don't have significant changes respect to the dataset with missing values, hence the impact is negligible.  
          
# Are there differences in activity patterns between weekdays and weekends?

```r
newdata<-mutate(newdata,day=weekdays(newdata$date))
newdata[,5]<-ifelse(newdata$day=="domingo","weekend","weekday")
newdata[,5]<-ifelse(newdata$day=="sábado","weekend","weekday")
colnames(newdata)[5]<-"typeday"
DailyAverageWD<-aggregate(newdata$steps ~ newdata$interval * newdata$typeday,FUN="mean")
colnames(DailyAverageWD)[1]<-"V1"
colnames(DailyAverageWD)[2]<-"V2"
colnames(DailyAverageWD)[3]<-"V3"
gg<-qplot(V1,V3,data=DailyAverageWD)
gg + geom_line()+facet_wrap(~ V2,nrow=2,ncol=1)+labs(x="representative minutes of days",y="Mean of steps taken per day (NA filled)")
```

![](Figs/unnamed-chunk-4-1.png)\
  
### There are difference in the activity patterns between weekdays and weekends, specifically at the beginning of the day ( 6:00 am ) where the weekdays have more activity, and afternoon (between 12 and 08 pm), where the weekends have more activity.
          
