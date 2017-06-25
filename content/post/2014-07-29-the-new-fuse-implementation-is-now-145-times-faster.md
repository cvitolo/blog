---
title: "The new FUSE implementation is now 145 times faster!"
date: 2014-07-29 12:38:48+00:00
---

Four of my previous posts were about the fuse implementation in RHydro. Since I published them I received many emails and requests for more info. It is clear the topic is of interest for many. I thought I would post a short note on a new FUSE implementation which is now available as a separate package called "fuse" on [GitHub](https://github.com/cvitolo/fuse).

```
# install/load dependent libraries
    if</span>(<span class="pl-k">!</span>require(<span class="pl-vo">zoo</span>)) install.packages(<span class="pl-s1"><span class="pl-pds">"</span>zoo<span class="pl-pds">"</span></span>)
    library(<span class="pl-vo">zoo</span>)
    <span class="pl-k">if</span>(<span class="pl-k">!</span>require(<span class="pl-vo">tgp</span>)) install.packages(<span class="pl-s1"><span class="pl-pds">"</span>tgp<span class="pl-pds">"</span></span>)
    library(<span class="pl-vo">tgp</span>)
    <span class="pl-k">if</span>(<span class="pl-k">!</span>require(<span class="pl-vo">qualV</span>)) install.packages(<span class="pl-s1"><span class="pl-pds">"</span>qualV<span class="pl-pds">"</span></span>)
    library(<span class="pl-vo">qualV</span>)
    <span class="kr">if</span><span class="p">(</span><span class="o">!</span><span class="kn">require</span><span class="p">(</span>hydromad<span class="p">))</span> install.packages<span class="p">(</span><span class="s">"hydromad"</span><span class="p">,</span>repos<span class="o">=</span><span class="s">"http://hydromad.catchment.org"</span><span class="p">)
    library(hydromad)
    </span><span class="pl-k">if</span>(<span class="pl-k">!</span>require(devtools)) install.packages(<span class="pl-s1"><span class="pl-pds">"devtools</span><span class="pl-pds">"</span></span>)
    library(devtools)
    
    # install the fuse package directly from GitHub
    install_github(<span class="pl-s1"><span class="pl-pds">"</span>ICHydro/r_fuse<span class="pl-pds">"</span></span>, <span class="pl-v">subdir</span> <span class="pl-k">=</span> <span class="pl-s1"><span class="pl-pds">"</span>fuse<span class="pl-pds">"</span></span>)
    library(<span class="pl-vo">fuse</span>)
```

The functions are named as in RHydro, the only difference is that the list of model structures is now called internally and does not need to be passed as input. It is still compatible with hydromad and below you find few lines to run a test (also available as [gist](https://gist.github.com/cvitolo/15aa013dc72dd9b39130) here).

```    
# Load sample data<span class="p">
d</span>ata<span class="p">(</span>DATA<span class="p">)
    
    #</span> Set the parameter ranges
    hydromad.options<span class="p">(</span>fusesma<span class="o">=</span>fusesma.ranges<span class="p">(),</span>fuserouting<span class="o">=</span>fuserouting.ranges<span class="p">())
    
    </span># Set model
    modspec <span class="o"><-</span> hydromad<span class="p">(</span>DATA<span class="p">, </span>sma <span class="o">=</span> <span class="s">"fusesma"</span><span class="p">, </span>routing <span class="o">=</span> <span class="s">"fuserouting"</span><span class="p">, </span>mid <span class="o">=</span> <span class="m">1</span><span class="o">:</span><span class="m">1248</span><span class="p">, </span>deltim <span class="o">=</span> <span class="m">1</span><span class="p">)
    
    </span># Randomly generate 1 parameter set 
    myNewParameterSet <span class="o"><-</span> parameterSets<span class="p">(</span> coef<span class="p">(</span>modspec<span class="p">,</span> warn<span class="o">=</span><span class="kc">FALSE</span><span class="p">),</span> <span class="m">1</span><span class="p">,</span> method<span class="o">=</span><span class="s">"random"</span><span class="p">)
    
    </span># Run a simulation using the parameter set generated above
    modx <span class="o"><-</span>  update<span class="p">(</span>modspec<span class="p">,</span> newpars <span class="o">=</span> myNewParameterSet<span class="p">)
    
    </span># Generate a summary of the result
    <span class="kp">summary</span><span class="p">(</span>modx<span class="p">) 
    
    </span># Plot results 
    hydromad<span class="o">:::</span>xyplot.hydromad<span class="p">(</span>modx<span class="p">,</span> with.P<span class="o">=</span><span class="kc">TRUE</span><span class="p">)</span>
```

I thought a basic benchmark between RHydro and fuse packages would be interesting ([here the gist](https://gist.github.com/cvitolo/937fcc093e308876c7fa)).

The result is that fuse's functions seem to run over 145 times faster than the corresponding functions in RHydro.

That's a great news if you plan to do anything that requires hundreds/thousands of runs!

[![Plot of benchmark results](http://aquaresearcher.files.wordpress.com/2014/07/benchmark.png?w=300)](https://aquaresearcher.files.wordpress.com/2014/07/benchmark.png)

Here the detailed results of the benchmark:

```
    <span class="GAKS5OJBFCB ace_keyword">> </span><span class="GAKS5OJBIBB ace_keyword">compare
    </span>Unit: seconds
                    expr        min         lq     median        uq        max neval
     f(DATA, parameters) 423.230827 433.070465 446.845983 451.28512 461.818262    10
     g(DATA, parameters)   2.893856   2.988898   3.076531   3.59736   3.713473    10
```

My session info:

```
> sessionInfo() R version 3.1.1 (2014-07-10) Platform: x86_64-pc-linux-gnu (64-bit) locale: [1] LC_CTYPE=en_GB.UTF-8 LC_NUMERIC=C LC_TIME=en_GB.UTF-8 [4] LC_COLLATE=en_GB.UTF-8 LC_MONETARY=en_GB.UTF-8 LC_MESSAGES=en_GB.UTF-8 [7] LC_PAPER=en_GB.UTF-8 LC_NAME=C LC_ADDRESS=C [10] LC_TELEPHONE=C LC_MEASUREMENT=en_GB.UTF-8 LC_IDENTIFICATION=C attached base packages: [1] stats graphics grDevices utils datasets methods base other attached packages: [1] ggplot2_1.0.0 microbenchmark_1.3-0 tgp_2.4-9 fuse_1.1.0 RHydro_2014-04.1 [6] qualV_0.3 KernSmooth_2.23-12 XML_3.98-1.1 deSolve_1.10-9 lhs_0.10 [11] sp_1.0-15 xts_0.9-7 zoo_1.7-11 loaded via a namespace (and not attached): [1] colorspace_1.2-4 digest_0.6.4 grid_3.1.1 gtable_0.1.2 lattice_0.20-29 MASS_7.3-33 [7] munsell_0.4.2 plyr_1.8.1 proto_0.3-10 Rcpp_0.11.2 reshape2_1.4 scales_0.2.4 [13] stringr_0.6.2 tools_3.1.1
```

Image credits to Nick Chill: http://goo.gl/9vWVEb
