---
title: "FUSE model in RHydro package (part 4: HydroMAD compatibility)"
date: 2014-07-25 12:51:37+00:00
---

This is the fourth of a series of tutorials on the FUSE implementation within the RHydro package. The script for this tutorial is available [here](https://gist.github.com/cvitolo/2248c569319f1f6dae5a). If you are interested in following the discussion related to this post and see how it evolves, join the [R4Hydrology](https://plus.google.com/communities/110355038350933119441) community on Google+!

If you want to know what FUSE is, how to prepare your data and run a simple simulation go to the [first post](http://wp.me/p2yns8-4P) of the series, for a basic calibration example (using 1 model structure) go to the [second post](http://wp.me/p2yns8-55), while for an example of multi-model calibration got to the [third post](http://wp.me/p2yns8-5m).

## RHydro-HydroMAD compatibility

[HydroMAD](http://hydromad.catchment.org/) is an excellent framework for hydrological modelling, optimization, sensitivity analysis and assessment of results. It contains a large set of soil moisture accounting modules and routing functions.

Thanks to Joseph Guillaume (hydromad's maintainer), FUSE-RHydro is now compatible with HydroMAD, therefore using and calibrating FUSE becomes even easier! Joseph provided many of the examples below, many thanks for that too!

In this tutorial I will show how to:

  1. set up FUSE and its parameter ranges using the hydromad approach
  2. run a simulation
  3. calibrate FUSE using one of the hydromad's algorithms

#### Recap from previous posts

Load the package and prepare your default list of models

```
library("hydromad") 
library("RHydro")

data(modlist)
```

Read sample data in:

```
data <- read.csv("dummyData.csv")
```

Convert to date the first column and then convert to zoo object

```
temp<span class="p">[,</span><span class="m">1</span><span class="p">]</span> <span class="o"><-</span> <span class="kp">as.Date</span><span class="p">(</span>temp<span class="p">[,</span><span class="m">1</span><span class="p">],</span>format<span class="o">=</span><span class="s">"%Y-%m-%d"</span><span class="p">) 
    </span>DATA <span class="o"><-</span> read.zoo<span class="p">(</span>temp<span class="p">)</span>
```

### Step 1: set up FUSE and its parameter ranges using the hydromad approach

HydroMAD allows to specify a rainfall-runoff model of choice. This is achieved by using the hydromad() function and specifying the soil moisture accounting model and routing function to use.

Set the parameter ranges using hydromad.options

```
hydromad.options<span class="p">(</span>fusesma <span class="o">= </span>fusesma.ranges<span class="p">(),</span>
                     fuserouting <span class="o">= </span>fuserouting.ranges<span class="p">())</span>
```

Set up the model

```
modspec <span class="o"><-</span> hydromad<span class="p">(</span>DATA<span class="p">,
    </span>                    sma <span class="o">=</span> <span class="s">"fusesma"</span><span class="p">, 
    </span>                    routing <span class="o">=</span> <span class="s">"fuserouting"</span><span class="p">, 
    </span>                    mid <span class="o">=</span> <span class="m">1</span><span class="o">:</span><span class="m">1248</span><span class="p">, 
    </span>                    modlist <span class="o">=</span> modlist<span class="p">)</span>
```

# Randomly generate 1 parameter set

```
myNewParameterSet <span class="o"><-</span> parameterSets<span class="p">( </span>coef<span class="p">(</span>modspec<span class="p">, </span>warn<span class="o">=</span><span class="kc">FALSE</span><span class="p">),</span> 
    <span class="m">                                    1</span><span class="p">, 
    </span>                                    method<span class="o">=</span><span class="s">"random" </span><span class="p">)</span>
```

### Step 2: run a single simulation

Run a simulation using the parameter set generated above

```
modx <- update(modspec, newpars = myNewParameterSet)
```

Generate a summary of the result

```
summary(modx)
```

The instantaneous runoff is 

```
U <- modx$U
```

The routed discharge is

```
Qrout <- modx$fitted.values
```

Plot the Observed vs Simulated value

```
hydromad:::xyplot.hydromad(modx)
```

Add the precipitation to the above plot 

```
hydromad:::xyplot.hydromad(modx, with.P=TRUE)
```    

### Step 3: calibrate FUSE using one of hydromad's algorithms

Hydromad provide the "fitBy" method to calibrate using a specified algorithm. As an example, the Shuffled Complex Evolution method can be used as shown below. Please note that the procedure is likely to take a LONG time.

```
modfit <- fitBySCE(modspec)
```

Get a summary of the result

```   
summary(modfit)
```    

If you want to use the latest version of fuse, the above steps can be adapted as follows:

```
library(hydromad) 
library(fuse)
data(modlist)

# Load data
temp <span class="o"><-</span> read.csv<span class="p">(</span><span class="s">"dummyData.csv"</span><span class="p">) 
    </span>temp<span class="p">[,</span><span class="m">1</span><span class="p">]</span> <span class="o"><-</span> <span class="kp">as.Date</span><span class="p">(</span>temp<span class="p">[,</span><span class="m">1</span><span class="p">],</span>format<span class="o">=</span><span class="s">"%Y-%m-%d"</span><span class="p">) 
    </span>DATA <span class="o"><-</span> read.zoo<span class="p">(</span>temp<span class="p">)
    
    </span># Set the parameter ranges using hydromad.options
    hydromad.options(fusesma = fusesma.ranges(),
                     fuserouting = fuserouting.ranges())
    
    # Set up the model
    modspec <- hydromad(DATA,
                        sma = "fusesma", 
                        routing = "fuserouting",
                        mid = 1:1248,
                        deltim = 1/24)
    
    # Calibrate FUSE using one of hydromad’s algorithms
    modfit <- fitBySCE(modspec)
    
    # Get a summary of the result
    summary(modfit)
```

## What's next?

This tutorials was just a brief introduction to a topic that can be explored in many different directions. From a technical point of view, it could be worth to invest some effort into code optimization and/or parallelisation. This would also have an impact on the scientific side facilitating experiments on sensitivity analysis, regionalisation of catchment characteristics and model structure variability.

Some of those ideas are already on their way, some others are just random thoughts. Therefore...watch this space!
