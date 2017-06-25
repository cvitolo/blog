---
title: "R CMD check tells me 'no visible binding for global variable'"
date: 2012-07-09 15:56:11+00:00
---

Today is definitely a lucky day!

[Here](http://r.789695.n4.nabble.com/R-CMD-check-no-visible-binding-for-global-variable-td3526970.html) is the solution to avoid the NOTE 'no visible binding for global variable' when running R CMD check.

Basically just add to the DESCRIPTION file of your package the following line (for instance after defining the license)

```
LazyData: yes
```

and add a line in the routine which calls the data.frame

e.g.

```
DATA 

P <- DATA[,"P"]
E <- DATA[,"E"]
```

In my case, that made the package checker happy!

[Here](http://r.789695.n4.nabble.com/R-CMD-check-tells-me-no-visible-binding-for-global-variable-what-does-it-mean-td1837236.html) is also an alternative solution.
