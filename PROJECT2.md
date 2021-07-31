## Course Project

**Reproducible Research Course Project 2**

Peer-graded Assignment

-   This course project is available on GitHub

    <a href="https://github.com/gautamvarmadatla/REPRODUCIBLERESEARCH2" target="_top">Reproducible
    Research Course Project 2</a>

## Exploring the U.S. National Oceanic and Atmospheric Administration’s (NOAA) storm database - Health and Economic Impacts

### Synopsis:

Storms and other severe weather events can cause both public health and
economic problems for communities and municipalities. Many severe events
can result in fatalities, injuries, and property damage, and preventing
such outcomes to the extent possible is a key concern.

This project involves exploring the U.S. National Oceanic and
Atmospheric Administration’s (NOAA) storm database. This database tracks
characteristics of major storms and weather events in the United States,
including when and where they occur, as well as estimates of any
fatalities, injuries, and property damage.

The basic goal of this assignment is to explore the NOAA Storm Database
and answer some questions, such as:

1.  Across the United States, which types of events are most harmful
    with respect to population health?

2.  Across the United States, which types of events have the greatest
    economic consequences?

### Loading the data

    library(ggplot2)  

    ## Warning: package 'ggplot2' was built under R version 4.0.5

    library(plyr) 

    ## Warning: package 'plyr' was built under R version 4.0.5

    library(dplyr)

    ## Warning: package 'dplyr' was built under R version 4.0.5

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:plyr':
    ## 
    ##     arrange, count, desc, failwith, id, mutate, rename, summarise,
    ##     summarize

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    #First we load the necessary packages and datasets.

    url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"
    download.file(url, "StormData.csv")
    data <- read.csv("StormData.csv")

    data <- tbl_df(data)

    ## Warning: `tbl_df()` was deprecated in dplyr 1.0.0.
    ## Please use `tibble::as_tibble()` instead.

    dim(data)

    ## [1] 902297     37

    head(data, n = 2)

    ## # A tibble: 2 x 37
    ##   STATE__ BGN_DATE   BGN_TIME TIME_ZONE COUNTY COUNTYNAME STATE EVTYPE BGN_RANGE
    ##     <dbl> <chr>      <chr>    <chr>      <dbl> <chr>      <chr> <chr>      <dbl>
    ## 1       1 4/18/1950~ 0130     CST           97 MOBILE     AL    TORNA~         0
    ## 2       1 4/18/1950~ 0145     CST            3 BALDWIN    AL    TORNA~         0
    ## # ... with 28 more variables: BGN_AZI <chr>, BGN_LOCATI <chr>, END_DATE <chr>,
    ## #   END_TIME <chr>, COUNTY_END <dbl>, COUNTYENDN <lgl>, END_RANGE <dbl>,
    ## #   END_AZI <chr>, END_LOCATI <chr>, LENGTH <dbl>, WIDTH <dbl>, F <int>,
    ## #   MAG <dbl>, FATALITIES <dbl>, INJURIES <dbl>, PROPDMG <dbl>,
    ## #   PROPDMGEXP <chr>, CROPDMG <dbl>, CROPDMGEXP <chr>, WFO <chr>,
    ## #   STATEOFFIC <chr>, ZONENAMES <chr>, LATITUDE <dbl>, LONGITUDE <dbl>,
    ## #   LATITUDE_E <dbl>, LONGITUDE_ <dbl>, REMARKS <chr>, REFNUM <dbl>

There are 902297 rows and 37 columns in total and also once would notice
that the events in the database start in the year 1950 and end in
November 2011.

### Data Processing for analysis:

The dataset we are working with has the following variables.

    names(data)

    ##  [1] "STATE__"    "BGN_DATE"   "BGN_TIME"   "TIME_ZONE"  "COUNTY"    
    ##  [6] "COUNTYNAME" "STATE"      "EVTYPE"     "BGN_RANGE"  "BGN_AZI"   
    ## [11] "BGN_LOCATI" "END_DATE"   "END_TIME"   "COUNTY_END" "COUNTYENDN"
    ## [16] "END_RANGE"  "END_AZI"    "END_LOCATI" "LENGTH"     "WIDTH"     
    ## [21] "F"          "MAG"        "FATALITIES" "INJURIES"   "PROPDMG"   
    ## [26] "PROPDMGEXP" "CROPDMG"    "CROPDMGEXP" "WFO"        "STATEOFFIC"
    ## [31] "ZONENAMES"  "LATITUDE"   "LONGITUDE"  "LATITUDE_E" "LONGITUDE_"
    ## [36] "REMARKS"    "REFNUM"

## Subsetting the features relevant to analysis:

# i) Subsetting the variables:

This analysis takes the following variables into consideration:

# Health Variables:

1.FATALATIES: a variable indicating the number of fatalities caused by
the particular observation, used to determine event types with the most
negative consequences on population health.

2.INJURIES: a variable indicating the number of injuries caused by the
particular observation, used to determine event types with the most
negative consequences on population health.

# Economic variables:

3.BGN\_DATE: a date variable, used to subset the data set for
observations between 1996 and 2011.

4.EVTYPE: a variable indicating the event type of the particular
observation, used to categorise per event type.

5.PROPDMG: a variable indicating the estimated monetary value of damage
to property caused by the particular observation, used to determine
event types with the most negative consequences on the economy, rounded
to three significant digits, in United States dollars.

6.PROPDMGEXP: a variable indicating the multiplier for `PROPDMG`; can be
“K” for 1,000, “M” for 1,000,000 or “B” for 1,000,000,000 as per NWS
Directive 10-1605.

7.CROPDMG: a variable indicating the estimated monetary value of damage
to agricultural property (crops) caused by the particular observation,
used to determine event types with the most negative consequences on the
economy, rounded to three significant digits, in United States dollars.

8.CROPDMGEXP: a variable indicating the multiplier for `CROPDMG`; can be
“K” for 1,000, “M” for 1,000,000 or “B” for 1,000,000,000 as per NWS
Directive 10-1605.

    stormData <- data[, c("BGN_DATE", "EVTYPE", "FATALITIES", "INJURIES", "PROPDMG", "PROPDMGEXP", "CROPDMG", "CROPDMGEXP")]
    stormData

    ## # A tibble: 902,297 x 8
    ##    BGN_DATE     EVTYPE FATALITIES INJURIES PROPDMG PROPDMGEXP CROPDMG CROPDMGEXP
    ##    <chr>        <chr>       <dbl>    <dbl>   <dbl> <chr>        <dbl> <chr>     
    ##  1 4/18/1950 0~ TORNA~          0       15    25   K                0 ""        
    ##  2 4/18/1950 0~ TORNA~          0        0     2.5 K                0 ""        
    ##  3 2/20/1951 0~ TORNA~          0        2    25   K                0 ""        
    ##  4 6/8/1951 0:~ TORNA~          0        2     2.5 K                0 ""        
    ##  5 11/15/1951 ~ TORNA~          0        2     2.5 K                0 ""        
    ##  6 11/15/1951 ~ TORNA~          0        6     2.5 K                0 ""        
    ##  7 11/16/1951 ~ TORNA~          0        1     2.5 K                0 ""        
    ##  8 1/22/1952 0~ TORNA~          0        0     2.5 K                0 ""        
    ##  9 2/13/1952 0~ TORNA~          1       14    25   K                0 ""        
    ## 10 2/13/1952 0~ TORNA~          0        0    25   K                0 ""        
    ## # ... with 902,287 more rows

\#ii) Subsetting the dates:

\#Next we subset the data to only include only events recorded after
1996 (as motivated above).

    stormData$BGN_DATE <- as.Date(as.character(stormData$BGN_DATE), "%m/%d/%Y %H:%M:%S")
    stormData <- subset(stormData, format(stormData$BGN_DATE, "%Y") > 1996 )
    stormData

    ## # A tibble: 621,260 x 8
    ##    BGN_DATE   EVTYPE    FATALITIES INJURIES PROPDMG PROPDMGEXP CROPDMG CROPDMGEXP
    ##    <date>     <chr>          <dbl>    <dbl>   <dbl> <chr>        <dbl> <chr>     
    ##  1 1997-03-03 TSTM WIND          0        0       6 K                0 K         
    ##  2 1997-03-03 TSTM WIND          0        0       5 K                0 K         
    ##  3 1997-03-03 TSTM WIND          0        0       0 K                0 K         
    ##  4 1997-03-05 TSTM WIND          0        0      10 K                0 K         
    ##  5 1997-03-05 TSTM WIND          0        0       5 K                0 K         
    ##  6 1997-03-05 TSTM WIND          0        0      15 K                2 K         
    ##  7 1997-03-05 TSTM WIND          0        0       3 K                0 K         
    ##  8 1997-03-05 TSTM WIND          0        0      10 K                0 K         
    ##  9 1997-03-05 TSTM WIND          0        0       6 K                0 K         
    ## 10 1997-03-05 TSTM WIND          0        0       8 K                0 K         
    ## # ... with 621,250 more rows

## Check Missing values if any:

Check for missing values in health and economic variables

    sum(is.na(stormData$FATALITIES))

    ## [1] 0

    sum(is.na(stormData$INJURIES))

    ## [1] 0

    sum(is.na(stormData$PROPDMG))

    ## [1] 0

    sum(is.na(stormData$CROPDMG))

    ## [1] 0

    sum(is.na(stormData$PROPDMGEXP))

    ## [1] 0

    sum(is.na(stormData$CROPDMGEXP))

    ## [1] 0

We can clearly see that there are no missing values in the dataset.

## Cleaning the dataset:

\#i)

Property and crop damage estimates are entered as actual dollar amounts
(the variable PROPDMG). But they were rounded to three significant
digits, followed by an alphabetical character signifying the magnitude
of the number, for example 1.55B for $1,550,000,000. Alphabetical
characters used to signify magnitude include,

                                    * K or k: thousand dollars (10^3)  
          
                                    * M or m: million dollars (10^6)  

                                    * B or b: billion dollars (10^9)  

    stormData$PROPDMGEXP <- as.character(stormData$PROPDMGEXP)
    stormData$PROPDMGEXP[is.na(stormData$PROPDMGEXP)] <- 0 # NA's considered as dollars
    stormData$PROPDMGEXP[!grepl("K|M|B", stormData$PROPDMGEXP, ignore.case = TRUE)] <- 0 # everything exept K,M,B is dollar
    stormData$PROPDMGEXP[grep("K", stormData$PROPDMGEXP, ignore.case = TRUE)] <- "3"
    stormData$PROPDMGEXP[grep("M", stormData$PROPDMGEXP, ignore.case = TRUE)] <- "6"
    stormData$PROPDMGEXP[grep("B", stormData$PROPDMGEXP, ignore.case = TRUE)] <- "9"
    stormData$PROPDMGEXP <- as.numeric(as.character(stormData$PROPDMGEXP))
    stormData$property.damage <- stormData$PROPDMG * 10^stormData$PROPDMGEXP

    stormData$CROPDMGEXP <- as.character(stormData$CROPDMGEXP)
    stormData$CROPDMGEXP[is.na(stormData$CROPDMGEXP)] <- 0 # NA's considered as dollars
    stormData$CROPDMGEXP[!grepl("K|M|B", stormData$CROPDMGEXP, ignore.case = TRUE)] <- 0 # everything exept K,M,B is dollar
    stormData$CROPDMGEXP[grep("K", stormData$CROPDMGEXP, ignore.case = TRUE)] <- "3"
    stormData$CROPDMGEXP[grep("M", stormData$CROPDMGEXP, ignore.case = TRUE)] <- "6"
    stormData$CROPDMGEXP[grep("B", stormData$CROPDMGEXP, ignore.case = TRUE)] <- "9"
    stormData$CROPDMGEXP <- as.numeric(as.character(stormData$CROPDMGEXP))
    stormData$crop.damage <- stormData$CROPDMG * 10^stormData$CROPDMGEXP
    stormData

    ## # A tibble: 621,260 x 10
    ##    BGN_DATE   EVTYPE    FATALITIES INJURIES PROPDMG PROPDMGEXP CROPDMG CROPDMGEXP
    ##    <date>     <chr>          <dbl>    <dbl>   <dbl>      <dbl>   <dbl>      <dbl>
    ##  1 1997-03-03 TSTM WIND          0        0       6          3       0          3
    ##  2 1997-03-03 TSTM WIND          0        0       5          3       0          3
    ##  3 1997-03-03 TSTM WIND          0        0       0          3       0          3
    ##  4 1997-03-05 TSTM WIND          0        0      10          3       0          3
    ##  5 1997-03-05 TSTM WIND          0        0       5          3       0          3
    ##  6 1997-03-05 TSTM WIND          0        0      15          3       2          3
    ##  7 1997-03-05 TSTM WIND          0        0       3          3       0          3
    ##  8 1997-03-05 TSTM WIND          0        0      10          3       0          3
    ##  9 1997-03-05 TSTM WIND          0        0       6          3       0          3
    ## 10 1997-03-05 TSTM WIND          0        0       8          3       0          3
    ## # ... with 621,250 more rows, and 2 more variables: property.damage <dbl>,
    ## #   crop.damage <dbl>

\#ii)We will now group events likeby using the common keyword . For
example, TUNDERSTORM WIND, TUNDERSTORM WINDS, HIGH WIND, etc.have WIND
in common.New variable EVENTS is the transform variable of EVTYPE that
have 10 different types of events as shown below.

    # create a new variable EVENT to transform variable EVTYPE in groups
    stormData$EVENT <- "OTHER"
    # group by keyword in EVTYPE
    stormData$EVENT[grep("HAIL", stormData$EVTYPE, ignore.case = TRUE)] <- "HAIL"
    stormData$EVENT[grep("HEAT", stormData$EVTYPE, ignore.case = TRUE)] <- "HEAT"
    stormData$EVENT[grep("FLOOD", stormData$EVTYPE, ignore.case = TRUE)] <- "FLOOD"
    stormData$EVENT[grep("WIND", stormData$EVTYPE, ignore.case = TRUE)] <- "WIND"
    stormData$EVENT[grep("STORM", stormData$EVTYPE, ignore.case = TRUE)] <- "STORM"
    stormData$EVENT[grep("SNOW", stormData$EVTYPE, ignore.case = TRUE)] <- "SNOW"
    stormData$EVENT[grep("TORNADO", stormData$EVTYPE, ignore.case = TRUE)] <- "TORNADO"
    stormData$EVENT[grep("WINTER", stormData$EVTYPE, ignore.case = TRUE)] <- "WINTER"
    stormData$EVENT[grep("RAIN", stormData$EVTYPE, ignore.case = TRUE)] <- "RAIN"
    # listing the transformed event types 
    sort(table(stormData$EVENT), decreasing = TRUE)

    ## 
    ##    HAIL    WIND   STORM   FLOOD   OTHER TORNADO  WINTER    SNOW    RAIN    HEAT 
    ##  197476  150747   90396   71944   41563   21916   18975   14446   11429    2368

## Analysis

### Aggregating events for public health variables

Table of public health problems by event type

    # aggregate FATALITIES and INJURIES by type of EVENT

    agg.fatalites.and.injuries <- ddply(stormData, .(EVENT), summarize, Total = sum(FATALITIES + INJURIES,  na.rm = TRUE))

    agg.fatalites.and.injuries$type <- "fatalities and injuries"
      
    # aggregate FATALITIES by type of EVENT


    agg.fatalities <- ddply(stormData, .(EVENT), summarize, Total = sum(FATALITIES, na.rm = TRUE))
    agg.fatalities$type <- "fatalities"


    # aggregate INJURIES by type of EVENT


    agg.injuries <- ddply(stormData, .(EVENT), summarize, Total = sum(INJURIES, na.rm = TRUE))
    agg.injuries$type <- "injuries"


    # combine all

    agg.health <- rbind(agg.fatalities, agg.injuries)
    health.by.event <- join (agg.fatalities, agg.injuries, by="EVENT", type="inner")
    health.by.event

    ##      EVENT Total       type Total     type
    ## 1    FLOOD  1182 fatalities  8377 injuries
    ## 2     HAIL     6 fatalities   641 injuries
    ## 3     HEAT  2000 fatalities  7554 injuries
    ## 4    OTHER  1979 fatalities  9166 injuries
    ## 5     RAIN    91 fatalities   231 injuries
    ## 6     SNOW   104 fatalities   618 injuries
    ## 7    STORM   295 fatalities  2348 injuries
    ## 8  TORNADO  1485 fatalities 19962 injuries
    ## 9     WIND   820 fatalities  4836 injuries
    ## 10  WINTER   228 fatalities  1525 injuries

### Aggregating events for economic variables

    # Aggregate PropDamage and CropDamage by type of event

    agg.propdmg.and.cropdmg <- ddply(stormData, .(EVENT), summarize, Total = sum(property.damage + crop.damage,  na.rm = TRUE))
    agg.propdmg.and.cropdmg$type <- "property and crop damage"


    # Aggregate propDamage by type of event


    agg.prop <- ddply(stormData, .(EVENT), summarize, Total = sum(property.damage, na.rm = TRUE))
    agg.prop$type <- "property"


    # Aggregate injuries by type of event


    agg.crop <- ddply(stormData, .(EVENT), summarize, Total = sum(crop.damage, na.rm = TRUE))
    agg.crop$type <- "crop"


    # Combining property damage and crop damage

    agg.economic <- rbind(agg.prop, agg.crop)
    economic.by.event <- join (agg.prop, agg.crop, by="EVENT", type="inner")
    economic.by.event

    ##      EVENT        Total     type       Total type
    ## 1    FLOOD 157533921520 property  5925179100 crop
    ## 2     HAIL  13982080930 property  2312805600 crop
    ## 3     HEAT      9243700 property   492578500 crop
    ## 4    OTHER  90814145770 property 20753797530 crop
    ## 5     RAIN    551630440 property   738415800 crop
    ## 6     SNOW    548173840 property    70902100 crop
    ## 7    STORM  62364447200 property  1089002000 crop
    ## 8  TORNADO  23897338160 property   270915810 crop
    ## 9     WIND   9457162220 property  1187300450 crop
    ## 10  WINTER   1522656250 property    23301000 crop

\#\#\#Results:

## Across the United States, which types of events are most harmful with respect to population health?

    agg.health$EVENT <- as.factor(agg.health$EVENT)
    # plot FATALITIES and INJURIES by EVENT
    health.plot <- ggplot(agg.health, aes(x = EVENT, y = Total, fill = type)) + geom_bar(stat = "identity") +
      xlab("Event ") + 
      ylab("Total number of health impact") +
      ggtitle("Weather event types impact on public health") +
      theme(plot.title = element_text(hjust = 0.5))
    print(health.plot)  

![](PROJECT2_files/figure-markdown_strict/unnamed-chunk-11-1.png)

\#\#Q2: \#\# Across the United States, which types of events have the
greatest economic consequences?

    agg.economic$EVENT <- as.factor(agg.economic$EVENT)
    economic.plot <- ggplot(agg.economic, aes(x = EVENT, y = Total, fill = type)) + geom_bar(stat = "identity") +
      xlab("Event ") + 
      ylab("Total damage in dollars") +
      ggtitle("Weather event types impact on property and crop damage") +
      theme(plot.title = element_text(hjust = 0.5))
    print(economic.plot) 

![](PROJECT2_files/figure-markdown_strict/unnamed-chunk-12-1.png) \#\#\#
CONCLUSION

The most harmful weather event for health (in number of total fatalites
and injuries) are tornados and The most devastating weather event with
the greatest economic cosequences (to property and crops) is a flood.
