---
title: "Generate a Latin Hypercube of parameters for a given FUSE model"
date: 2015-05-26T13:09:13-06:00
---

In the previous post I showed how to get information regarding model building options and used parameters for a given FUSE model. In this post I'll show how to sample (for the given model) a set of 100 parameters uniformly using the Latin Hypercube Sampling method.

Each FUSE model uses different parameters therefore, in order to sample uniformly, we need to remove the unused parameters and then sample. Here is how I achieve this.

Install/load the fuse package:

```r    
devtools::install_github("cvitolo/fuse")
library(fuse)
```

Choose one of FUSE's models (e.g. TOPMODEL which corresponds to mid = 60) and get model/parameters info using the FUSEinfo function described in the previous post:

```r  
x <- fuseInfo(mid = 60)
```

Now a Latin Hypercube of 100 samples for the above model (mid=60) is defined as follows:

```r 
parameters <- generateParameters(NumberOfRuns = 100, 
                                 params2remove = names(x)[which(x==FALSE)])
```
