---
title: "High Performance Computing Service - Part 2: Get your files ready"
date: 2013-07-02 15:22:41+00:00
---

To use the HPC service I need:

  1. input files (e.g. data.rda)
  2. some routines (e.g. myroutine1.R, myroutine2.R, myroutine3.R)
  3. a batch script

The input files are:

  * data_C1.rda
  * data_C2.rda
  * data_C3.rda

Each of the above contains the following objects:

  * Classes of Topographic Index (topidxclasses),
  * Delay function (delay),
  * Precipitation time series (rain),
  * Evapotranspiration time series (ET0),
  * Observed streamflow discharge time series (Qobs).

For more info on the above input files, read help file of topmodel package (?topmodel)

Let's prepare the R routines:

# myroutine1.R

```
# This routine runs an hydrological model called "topmodel" 1000 times using parameter sets
# randomly sampled from a uniform distribution, using data from catchment "C1". # The result is a vector containing Nash-Sutcliffe efficiencies.
    
library(Hmisc)
library(topmodel)
load("$WORK/input/data_C1.rda")

runs  <- 10000
vch   <- 1000
dt    <- 1
Srmax <- runif(runs, min=0, max=2)
td    <- runif(runs, min=0, max=3)
k0    <- runif(runs, min=0, max=0.01)
CD    <- runif(runs, min=0, max=5)
qs0   <- runif(runs, min=3e-5, max=5e-5)
lnTe  <- runif(runs, min=-4, max=0)
m     <- runif(runs, min=0, max=0.2)
Sr0   <- runif(runs, min=0, max=0.02)
vr    <- runif(runs, min=100, max=1500)
    
param.set<-cbind(qs0,lnTe,m,Sr0,Srmax,td,vch,vr,k0,CD,dt)

# returns an array of (runs) Nash Sutcliffe efficiencies; one for each parameter set:
eff1<-topmodel(param.set, topidxclasses, delay, rain, ET0, Qobs = Qobs)

```

# myroutine2.R

```    
# This routine runs an hydrological model called "topmodel" 1000 times using parameter sets 
# randomly sampled from a uniform distribution, using data from catchment "C2". # The result is a vector containing Nash-Sutcliffe efficiencies.
    
library(Hmisc)
library(topmodel)
load("$WORK/input/data_C2.rda")
    
runs  <- 10000
vch   <- 1000
dt    <- 1
Srmax <- runif(runs, min=0, max=2)
td    <- runif(runs, min=0, max=3)
k0    <- runif(runs, min=0, max=0.01)
CD    <- runif(runs, min=0, max=5)
qs0   <- runif(runs, min=3e-5, max=5e-5)
lnTe  <- runif(runs, min=-4, max=0)
m     <- runif(runs, min=0, max=0.2)
Sr0   <- runif(runs, min=0, max=0.02)
vr    <- runif(runs, min=100, max=1500)
    
param.set<-cbind(qs0,lnTe,m,Sr0,Srmax,td,vch,vr,k0,CD,dt)

# returns an array of (runs) Nash Sutcliffe efficiencies; one for each parameter set:
eff2 <- topmodel(param.set, topidxclasses, delay, rain, ET0, Qobs = Qobs)
```

# myroutine3.R

```    
# This routine runs an hydrological model called "topmodel" 1000 times using parameter sets 
# randomly sampled from a uniform distribution, using data from catchment "C3". # The result is a vector containing Nash-Sutcliffe efficiencies.
    
library(Hmisc)
library(topmodel)
load("$WORK/input/data_C3.rda")

runs  <- 10000
vch   <- 1000
dt    <- 1
Srmax <- runif(runs, min=0, max=2)
td    <- runif(runs, min=0, max=3)
k0    <- runif(runs, min=0, max=0.01)
CD    <- runif(runs, min=0, max=5)
qs0   <- runif(runs, min=3e-5, max=5e-5)
lnTe  <- runif(runs, min=-4, max=0)
m     <- runif(runs, min=0, max=0.2)
Sr0   <- runif(runs, min=0, max=0.02)
vr    <- runif(runs, min=100, max=1500)
  
param.set<-cbind(qs0,lnTe,m,Sr0,Srmax,td,vch,vr,k0,CD,dt)
# returns an array of (runs) Nash Sutcliffe efficiencies; one for each parameter set:
eff3 <- topmodel(param.set, topidxclasses, delay, rain, ET0, Qobs = Qobs)
```

Let's prepare the batch file:

The batch file contains the following important information:

  * the maximum expected running time (walltime, on the second line)
  * the number of cpus to utilize for each job (cpus, on the third line)
  * the memory to allocate for the job (mem, on the third line)
  * the module to use (I'll use the module that access the R version 2.15, fourth line)
  * the list of routines to call and files where to read any printed message (e.g. error messages)

# batch.sh

```
#!/bin/sh 
#PBS -l walltime=50:00:00 
#PBS -l select=1:ncpus=1:mem=600mb
  
module load R/2.15 intel-suite/2012
R CMD BATCH --slave $HOME/myroutine_C1.R $WORK/outputs/Rout/C1.Rout
R CMD BATCH --slave $HOME/myroutine_C2.R $WORK/outputs/Rout/C2.Rout
R CMD BATCH --slave $HOME/myroutine_C3.R $WORK/outputs/Rout/C3.Rout
```

Now go to [Part 3](../../../../2013/07/03/high-performance-computing-service---part-3-transfer-your-files) to learn how to transfer your files and run jobs.
