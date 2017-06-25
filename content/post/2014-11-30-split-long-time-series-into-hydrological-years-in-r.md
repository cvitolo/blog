---
title: "Split long time series into (hydrological) years in R"
date: 2014-11-30 18:17:51+00:00
---

I have been recently working on a rather basic task: splitting long time series into years. Although this might sound trivial for calendar years, I had to think a bit to find a relatively elegant solution for hydrological years. Below is what I came up with, however if you are aware of a better way, please leave a comment!

For this exercise, we need to load only one library:

    
    # Load library
    library(xts)


Let's generate a dummy time series:

    
    # Generate dummy time series
    from <- as.Date("1950-01-01")
    to <- as.Date("1990-12-31")
    myDates <- seq.Date(from=from,to=to,by="day")
    myTS <- as.xts(runif(length(myDates)),order.by=myDates)


When working with standard calendar years (from Jan to Dec), splitting a time series into years is not too much of a problem:

    
    # Split the time series into calendar years
    myList <- tapply(myTS, format(myDates, "%Y"), c)


The result is a list of 41 time series, each of lenght = 1 year.

Any time series can be accessed, as usual, via its index:

    
    myList[[1]]
    plot( myList[[1]] )


[![Rplot1](https://aquaresearcher.files.wordpress.com/2014/11/rplot1.png?w=300)](https://aquaresearcher.files.wordpress.com/2014/11/rplot1.png)

Things become more interesting with non-standard calendars, such as hydrological years (starting on the 1st October and ending on the following 30th September).

The first step is to calculate the number of hydrological years, this is going to be:

the number of years in which we have records from Jan (index = 0) to September (index = 8) minus 1 (because we cannot count the first year).

    
    # calculate the number of hydrological years
    nHY <- length(split(myTS[.indexmon(myTS) %in% 0:8], f="years"))-1


Then we create an empty list and populate it with a series in which we append (or bind) the records from October to December of a generic year ``counter'', to the records from Jan to Sep of the year ``counter+1''.

    
    # create an empty table , to be populate by a loop
    myList <- list()
    
    for ( counter in 1:nHY ){
     
     oct2dec <- split(myTS[.indexmon(myTS) %in% 9:11], f="years")[[counter]]
     jan2sep <- split(myTS[.indexmon(myTS) %in% 0:8], f="years")[[counter + 1]]
     
     myList[[counter]] <- rbind(oct2dec, jan2sep)
     
    }


Again, any time series can be accessed via its index:

    
    myList[[1]]
    plot( myList[[1]] )


[![Rplot2](https://aquaresearcher.files.wordpress.com/2014/11/rplot2.png?w=300)](https://aquaresearcher.files.wordpress.com/2014/11/rplot2.png)

That's all! The code in this post is also available as public [gist](https://gist.github.com/cvitolo/e2ec4887f376e3b7d912).
