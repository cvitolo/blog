---
title: "FUSE model in RHydro package (part 3: ensemble)"
date: 2014-07-18 12:34:23+00:00
---

This is the third of a series of tutorials on the FUSE implementation within the RHydro package. The script for this tutorial is available [here](https://gist.github.com/cvitolo/ce194a31b1fe2106c902). If you are interested in following the discussion related to this post and see how it evolves, join the [R4Hydrology](https://plus.google.com/communities/110355038350933119441) community on Google+!

If you want to know what FUSE is, how to prepare your data and run a simple simulation go to the [first post](http://wp.me/p2yns8-4P) of the series, while for a basic calibration example (using 1 model structure) go to the [second post](http://wp.me/p2yns8-55).

### Recap from previous posts

Load the package and prepare your data:

```
library(RHydro)
temp <- read.csv("dummyData.csv") 
DATA <- zooreg(temp[,2:4], order.by=temp[,1])
myDELTIM <- 1
```

## FUSE ensemble

Very often hydrologists decide to use a particular hydrological model based on code availability, familiarity and experience rather than based on hydrological suitability. The real advantage of using FUSE is the possibility to work with an ensemble of multiple models so that uncertainties related to the model structure can be quantified.

The input that defines the model structure is called mid (model identification number) and its value ranges between 1 and 1248. When the most suitable model structure(s) is not known _a priori_, the mid can be added to the list of parameters and calibrated.

Adding the full mid-range implies the need to increase significantly the sampling space. There are, however, 4 model structures (called parent models) from which all the other model combinations are derived. In this tutorial I will only consider those 4 model structures.

In this tutorial I will show how to:
  
  1. define the update sampling space (parameter + mid ranges)
  2. run a multi-model calibration
  3. compare results

### Step A: define the parameter ranges + mid range

The parent models are as follows:

  - 60 = TOPMODEL
  - 230 = ARNOXVIC
  - 342 = PRMS
  - 426 = SACRAMENTO

Therefore mid can be one of those 4 values:

```
mids <- c(60, 230, 342, 426)
```

The parameter ranges are defined as in the previous post.

```
require(tgp)
DefaultRanges <- data.frame(rbind(rferr_add = c(0,0),
                                  rferr_mlt = c(1,1), 
                                  maxwatr_1 = c(25,500), 
                                  maxwatr_2 = c(50,5000),
                                  fracten = c(0.05,0.95), 
                                  frchzne = c(0.05,0.95),
                                  fprimqb = c(0.05,0.95), 
                                  rtfrac1 = c(0.05,0.95), 
                                  percrte = c(0.01,1000), 
                                  percexp = c(1,20), 
                                  sacpmlt = c(1,250), 
                                  sacpexp = c(1,5), 
                                  percfrac = c(0.05,0.95), 
                                  iflwrte = c(0.01,1000), 
                                  baserte = c(0.001,1000), 
                                  qb_powr = c(1,10), 
                                  qb_prms = c(0.001,0.25), 
                                  qbrate_2a = c(0.001,0.25), 
                                  qbrate_2b = c(0.001,0.25), 
                                  sareamax = c(0.05,0.95), 
                                  axv_bexp = c(0.001,3), 
                                  loglamb = c(5,10), 
                                  tishape = c(2,5), 
                                  timedelay = c(0.01,5)))
        
names(DefaultRanges) <- c("Min","Max")
nRuns <- 100
parameters <- lhs( nRuns, as.matrix(DefaultRanges) )
parameters <- data.frame(parameters)
names(parameters) <- c("rferr_add","rferr_mlt","maxwatr_1","maxwatr_2","fracten","frchzne","fprimqb","rtfrac1","percrte","percexp","sacpmlt","sacpexp","percfrac","iflwrte","baserte","qb_powr","qb_prms","qbrate_2a","qbrate_2b","sareamax","axv_bexp","loglamb","tishape","timedelay")
```

### Step B: run a multi-model calibration

Use the Nash-Sutcliffe efficiency as objective function and run the model 4*nRuns times (for each mid and parameter set).

```
require(qualV)
indices <- rep(NA,4*nRuns)
discharges <- matrix(NA,ncol=4*nRuns,nrow=dim(DATA)[1])
kCounter <- 0
for (m in 1:4){
  myMID <- mids[m]
  for (pid in 1:nRuns){
    kCounter <- kCounter + 1
    ParameterSet <- as.list(parameters[pid,])
    # Run FUSE Soil Moisture Accounting module
    Qinst <- fusesma.sim(DATA,
                         mid = myMID,
                         modlist,
                         deltim = myDELTIM,
                         states = FALSE, fluxes = FALSE, fracstate0 = 0.25,
                         ParameterSet$rferr_add, 
                         ParameterSet$rferr_mlt, 
                         ParameterSet$frchzne, 
                         ParameterSet$fracten, 
                         ParameterSet$maxwatr_1,
                         ParameterSet$percfrac,
                         ParameterSet$fprimqb, 
                         ParameterSet$qbrate_2a, 
                         ParameterSet$qbrate_2b, 
                         ParameterSet$qb_prms, 
                         ParameterSet$maxwatr_2, 
                         ParameterSet$baserte, 
                         ParameterSet$rtfrac1, 
                         ParameterSet$percrte, 
                         ParameterSet$percexp, 
                         ParameterSet$sacpmlt, 
                         ParameterSet$sacpexp, 
                         ParameterSet$iflwrte,  
                         ParameterSet$axv_bexp, 
                         ParameterSet$sareamax, 
                         ParameterSet$loglamb, 
                         ParameterSet$tishape,
                         ParameterSet$qb_powr)
    
    # Run FUSE Routing module
    Qrout <- fuserouting.sim(Qinst, mid = myMID, 
                             modlist = modlist, 
                             timedelay = ParameterSet$timedelay, 
                             deltim = myDELTIM)
    
    indices[kCounter] <- EF(DATA$Q,Qrout)
    discharges[,kCounter] <- Qrout
  }
}
```

## Step C: compare results

Deterministically, the best simulation according to the Nash-Sutcliffe efficiency, is the one with the maximum factor.

```    
bestRun <- which(indices == max(indices))
```    

This corresponds to the model ARNOXVIC, in fact:

``` 
bestModel <- function(runNumber){
  if (runNumber<(nRuns+1)) myBestModel <- "TOPMODEL"
  if (runNumber>(nRuns+1) & runNumber<(2*nRuns+1)) myBestModel <- "ARNOXVIC"
  if (runNumber>(2*nRuns+1) & runNumber<(3*nRuns+1)) myBestModel <- "PRMS"
  if (runNumber>(3*nRuns+1) & runNumber<(4*nRuns+1)) myBestModel <- "SACRAMENTO"
  return(myBestModel)
}
bestModel(bestRun)

plot(coredata(DATA$Q),type="l",xlab="",ylab="Streamflow [mm/day]", lwd=0.5)
for(pid in 1:(4*nRuns)){
  lines(discharges[,pid], col="gray", lwd=3)
}
lines(coredata(DATA$Q),col="black", lwd=1)
lines(discharges[,bestRun],col="red", lwd=1)
```
 
The plot below shows the observed streamflow in black, all the simulated results in grey and the "best" simulated streamflow in red.

As you can see, using multiple model structures inflates the uncertainty.

[caption id="attachment_349" align="aligncenter" width="300"][![FUSE simulations (4 models)](http://aquaresearcher.files.wordpress.com/2014/07/fuse03_4models.png?w=300)](https://aquaresearcher.files.wordpress.com/2014/07/fuse03_4models.png) FUSE simulations (4 models)[/caption]

How the best simulation of each model structure compare to each other?

```
bestRun0060 <- which(indices[1:nRuns] == max(indices[1:nRuns]))
bestRun0230 <- nRuns + which(indices[(nRuns+1):(2*nRuns)] == max(indices[(nRuns+1):(2*nRuns)]))
bestRun0342 <- 2*nRuns + which(indices[(2*nRuns+1):(3*nRuns)] == max(indices[(2*nRuns+1):(3*nRuns)]))
bestRun0426 <- 3*nRuns + which(indices[(3*nRuns+1):(4*nRuns)] == max(indices[(3*nRuns+1):(4*nRuns)]))
    
plot(coredata(DATA$Q),type="l",xlab="",ylab="Streamflow [mm/day]", lwd=1)
lines(discharges[,bestRun0060], col="green", lwd=1)
lines(discharges[,bestRun0230], col="blue", lwd=1)
lines(discharges[,bestRun0342], col="pink", lwd=1)
lines(discharges[,bestRun0426], col="orange", lwd=1)
    
legend("top", 
       c("TOPMODEL", "ARNOXVIC", "PRMS","SACRAMENTO"), 
       col = c("green", "blue", "pink", "orange"),
       lty = c(1, 1, 1, 1))
```
    
[caption id="attachment_350" align="aligncenter" width="412"][![Best simulation for each model structure](http://aquaresearcher.files.wordpress.com/2014/07/fuse03_best4.png?w=300)](https://aquaresearcher.files.wordpress.com/2014/07/fuse03_best4.png) Best simulation for each model structure[/caption]

The plot above shows that TOPMODEL seems the least affected by the initial conditions, while PRMS is the most affected.

## What's next?

FUSE-RHydro is also compatible with HydroMAD, therefore model calibration and assessment becomes even easier (see an example [here](http://wp.me/p2yns8-5s))!
