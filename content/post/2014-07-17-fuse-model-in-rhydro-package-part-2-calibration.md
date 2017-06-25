---
title: "FUSE model in RHydro package (part 2: calibration)"
date: 2014-07-17 08:17:12+00:00
---

This is the second of a series of tutorials on the FUSE implementation within the RHydro package. The script for this tutorial is available [here](https://gist.github.com/cvitolo/814c3007443a3026d4b6). If you are interested in following the discussion related to this post and see how it evolves, join the [R4Hydrology](https://plus.google.com/communities/110355038350933119441) community on Google+!

If you want to know what FUSE is, how to prepare your data and run a simple simulation go to my [previous post](http://wp.me/p2yns8-4P).

### Recap from previous post

Load the package, prepare your data (dummyData.csv is available [here](https://drive.google.com/file/d/0B8i_8AV-TwKTMXBnbmpUcUh6QVk/edit?usp=sharing)) and select a model structure:

```
library(RHydro)
temp <- read.csv("dummyData.csv") 
DATA <- zooreg(temp[,2:4], order.by=temp[,1])
myDELTIM <- 1
myMID <- 60
```

## Sample parameter space using the Latin Hypercube Sampling method

The default parameter ranges are suggested in [Clark et al. (2008)](http://onlinelibrary.wiley.com/doi/10.1029/2007WR006735/abstract) and are used to sample parameter sets.

Define sample domain (min and max for each parameter)

```
DefaultRanges <- data.frame(rbind(rferr_add = c(0,0), # additive rainfall error (mm)
                                  rferr_mlt = c(1,1), # multiplicative rainfall error (-)
                                  maxwatr_1 = c(25,500), # depth of the upper soil layer (mm)
                                  maxwatr_2 = c(50,5000), # depth of the lower soil layer (mm)
                                  fracten = c(0.05,0.95), # fraction total storage in tension storage (-)
                                  frchzne = c(0.05,0.95), # fraction tension storage in recharge zone (-)
                                  fprimqb = c(0.05,0.95), # fraction storage in 1st baseflow reservoir (-)
                                  rtfrac1 = c(0.05,0.95), # fraction of roots in the upper layer (-)
                                  percrte = c(0.01,1000), # percolation rate (mm day-1) 
                                  percexp = c(1,20), # percolation exponent (-)
                                  sacpmlt = c(1,250), # SAC model percltn mult for dry soil layer (-)
                                  sacpexp = c(1,5), # SAC model percltn exp for dry soil layer (-)
                                  percfrac = c(0.05,0.95), # fraction of percltn to tension storage (-)
                                  iflwrte = c(0.01,1000), # interflow rate (mm day-1) 
                                  baserte = c(0.001,1000), # baseflow rate (mm day-1) 
                                  qb_powr = c(1,10), # baseflow exponent (-)
                                  qb_prms = c(0.001,0.25), # baseflow depletion rate (day-1) 
                                  qbrate_2a = c(0.001,0.25), # baseflow depletion rate 1st reservoir (day-1) 
                                  qbrate_2b = c(0.001,0.25), # baseflow depletion rate 2nd reservoir (day-1) 
                                  sareamax = c(0.05,0.95), # maximum saturated area (-)
                                  axv_bexp = c(0.001,3), # ARNO/VIC b exponent (-)
                                  loglamb = c(5,10), # mean value of the topographic index (m)
                                  tishape = c(2,5), # shape param for the topo index Gamma dist (-) 
                                  timedelay = c(0.01,5) )) # time delay in runoff (days)

names(DefaultRanges) <- c("Min","Max")
```

Sample from the above ranges using the Latin Hypercube Sampling method.

```    
require(tgp)
nRuns <- 100 # this is the number of samples
parameters <- lhs( nRuns, as.matrix(DefaultRanges) )
parameters <- data.frame(parameters)
names(parameters) <- c("rferr_add","rferr_mlt","maxwatr_1","maxwatr_2","fracten","frchzne","fprimqb","rtfrac1","percrte","percexp","sacpmlt","sacpexp","percfrac","iflwrte","baserte","qb_powr","qb_prms","qbrate_2a","qbrate_2b","sareamax","axv_bexp","loglamb","tishape","timedelay")
```

## Run simulations

Use the Nash-Sutcliffe efficiency as objective function and run the model nRuns times (1 for each parameter set).

```
require(qualV)
indices <- rep(NA,nRuns)
discharges <- matrix(NA,ncol=nRuns,nrow=dim(DATA)[1])

for (pid in 1:nRuns){
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
    
  indices[pid] <- EF(DATA$Q,Qrout)
  discharges[,pid] <- Qrout
}
```

## Plot the best simulation

Deterministically, the best simulation according to the Nash-Sutcliffe efficiency, is the one with the maximum factor.

```    
bestRun <- which(indices == max(indices))[1] # this takes only the first simulation if more than one return the same efficiency value
    
plot(coredata(DATA$Q),type="l",xlab="",ylab="Streamflow [mm/day]", lwd=0.5)
for(pid in 1:nRuns){
  lines(discharges[,pid], col="gray", lwd=3)
}
lines(coredata(DATA$Q),col="black", lwd=1)
lines(discharges[,bestRun],col="red", lwd=1)
```

The plot below shows the observed streamflow in black, all the simulated results in grey and the "best" simulated streamflow in red.

[caption id="attachment_329" align="aligncenter" width="393"][![Results FUSE basic calibration](http://aquaresearcher.files.wordpress.com/2014/07/fuse021.png?w=300)](https://aquaresearcher.files.wordpress.com/2014/07/fuse021.png) Results FUSE basic calibration[/caption]

## What's next?

The real advantage of using FUSE is the possibility to work with an ensemble of multiple models (see an example [here](http://wp.me/p2yns8-5m)). FUSE-RHydro is also compatible with HydroMAD, therefore model calibration and assessment becomes even easier (see an example [here](http://wp.me/p2yns8-5s))!
