---
title: "Rd-files error: non-ASCII input and no declared encoding"
date: 2012-07-10 15:00:24+00:00
---

This error has been annoying me for a while now...and [here](http://r.789695.n4.nabble.com/Rd-file-error-non-ASCII-input-and-no-declared-encoding-td2403233.html) is the simplest solution ever!.

In R, type:

```
tools::showNonASCII(readLines("path/filename"))
```
