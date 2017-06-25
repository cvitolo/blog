---
title: "FUSE model in RHydro package (part 1: simple simulation)"
date: 2014-07-16 15:56:51+00:00
---

This is the first of a series of tutorials on the FUSE implementation within the RHydro package. The script for this tutorial is available [here](https://gist.github.com/cvitolo/acef8b33355765ce7341). If you are interested in following the discussion related to this post and see how it evolves, join the [R4Hydrology](https://plus.google.com/communities/110355038350933119441) community on Google+!

## What's FUSE?

FUSE is an ensemble of numerous conceptual rainfall-runoff models developed by[ Clark et al. (2008)](http://onlinelibrary.wiley.com/doi/10.1029/2007WR006735/abstract) and used to simulate the streamflow discharge for a river catchment when areal averaged time series of precipitation (plus snowmelt) and evapotranspiration are available. The code was originally developed by Martyn Clark in Fortran, but a couple of years ago I implemented a version of the same code in R. The code is part of the [RHydro package](https://r-forge.r-project.org/projects/r-hydro/) available from R-Forge. In this tutorial I'm going to show how to:

  1. install RHydro,
  2. prepare data for FUSE,
  3. run a simulation.

### Step A: install RHydro

RHydro is available from R-Forge and can beinstalled running the following command:

```
install.packages("RHydro", repos = "http://R-Forge.R-project.org")
```

### Step B: prepare your data

Prepare your data so that you have a zooreg object containing valid dates, P (precipitation+snowmelt time series), E (potential evapotranspiration time series) and Q (streamflow discharge time series). If you do not know how to do this, download my dummy data from [here](https://drive.google.com/file/d/0B8i_8AV-TwKTMXBnbmpUcUh6QVk/edit?usp=sharing) and adapt the content.

[caption id="attachment_301" align="aligncenter" width="223"][![Screenshot of dummyData.csv](http://aquaresearcher.files.wordpress.com/2014/07/csv.png?w=223)](https://aquaresearcher.files.wordpress.com/2014/07/csv.png) Screenshot of dummyData.csv[/caption]

Save dummyData.csv in your working directory. Now, load important libraries and create the zooreg object.

```
# Load libraries
library(RHydro)

# Load data into a zooreg object
temp <- read.csv("dummyData.csv")
DATA <- zooreg(temp[,2:4], order.by=temp[,1])
```

### Step C: run a simulation

FUSE contains 1248 different model structures. If you want to use only one of them, you should specify the relative model identification number (mid). Let's assume that:

  1. you want to run the model that corresponds to mid = 60,
  2. you want to use a specific set of parameters,
  3. you data is made of daily time series.

Then, you define the model inputs as in the example below:

```
# (1)
myMID <- 60

# (2)
myParameterSet <- list(rferr_add = 0, 
                       rferr_mlt = 1, 
                       frchzne = 0.5, 
                       fracten = 0.6828, 
                       maxwatr_1 = 106.6406, 
                       percfrac = 0.5, 
                       fprimqb = 0.5, 
                       qbrate_2a = 0.025, 
                       qbrate_2b = 0.01, 
                       qb_prms = 0.1294, 
                       maxwatr_2 = 3839.8, 
                       baserte = 50, 
                       rtfrac1 = 0.75, 
                       percrte = 703.128, 
                       percexp = 4.8594, 
                       sacpmlt = 10, 
                       sacpexp = 5, 
                       iflwrte = 500, 
                       axv_bexp = 0.7039, 
                       sareamax = 0.25, 
                       loglamb = 7.5, 
                       tishape = 3, 
                       qb_powr = 5, 
                       timedelay = 1.3355)

# (3)
myDELTIM <- 1
```

You can calculate the effective rainfall as follows:

```
Qinst <- fusesma.sim (DATA, mid = myMID, modlist, deltim = myDELTIM, 
                      states = FALSE, fluxes = FALSE, 
                      fracstate0 = 0.25,
                      myParameterSet$rferr_add, 
                      myParameterSet$rferr_mlt,
                      myParameterSet$frchzne, 
                      myParameterSet$fracten,
                      myParameterSet$maxwatr_1,
                      myParameterSet$percfrac,
                      myParameterSet$fprimqb, 
                      myParameterSet$qbrate_2a,
                      myParameterSet$qbrate_2b, 
                      myParameterSet$qb_prms,
                      myParameterSet$maxwatr_2, 
                      myParameterSet$baserte,
                      myParameterSet$rtfrac1, 
                      myParameterSet$percrte,
                      myParameterSet$percexp, 
                      myParameterSet$sacpmlt,
                      myParameterSet$sacpexp, 
                      myParameterSet$iflwrte,
                      myParameterSet$axv_bexp, 
                      myParameterSet$sareamax,
                      myParameterSet$loglamb,
                      myParameterSet$tishape,
                      myParameterSet$qb_powr)
```

Now calculate the routed runoff:

```
Qrout <- fuserouting.sim(Qinst, 
                         mid = myMID, 
                         modlist = modlist,
                         timedelay = myParameterSet$timedelay, 
                         deltim = myDELTIM)
```

Plot the routed discharge:

```
plot(Qrout, type = "l")
```

If you are using my dummy data, the result of the last command should be as in the figure below.

[caption id="attachment_304" align="alignnone" width="300"][![Rplot](http://aquaresearcher.files.wordpress.com/2014/07/rplot.png?w=300)](https://aquaresearcher.files.wordpress.com/2014/07/rplot.png) Routed streamflow discharge[/caption]

The input fracstate0 (see fusesma function) refers to the initial conditions. The default value is 0.25 which means that at time = 0 the water storages already contain 25% of their maximum capacity. You can change this value to see how a variation of fracstate0 may affect the result of your simulation.


## What's next?

This tutorial assumes you want to use 1 model structure and 1 parameter set. which is a rather simplistic case. The majority of the time hydrologists might be interested in calibrating one specific model structure (see an example [here](http://wp.me/p2yns8-55)). However, the real advantage of using FUSE is the possibility to work with an ensemble of multiple models (see an example [here](http://wp.me/p2yns8-5m)). FUSE-RHydro is also compatible with HydroMAD, therefore model calibration and assessment becomes even easier (see an example [here](http://wp.me/p2yns8-5s))!
