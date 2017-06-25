---
title: "FUSE model and parameters information"
date: 2015-05-26T13:09:13-06:00
---

A quick post to show how to find what model building decisions, options (name and ID number) and depending parameters are associated with a given FUSE model.

First of all, install/load the fuse package:

    
```r   
devtools::install_github("cvitolo/fuse")
library(fuse)
```

Choose one of FUSE's models (e.g. TOPMODEL which corresponds to mid = 60)and run the function fuseInfo using mid as input:

    
```r
fuseInfo(mid = 60)
```

The result of FUSEinfo is a dataframe containing 32 columns.

```
    rferr arch1 arch2 qsurf qperc esoil qintf q_tdh rferr_add rferr_mlt maxwatr_1 maxwatr_2 fracten
    1    12    21    34    43    51    62    71    82     FALSE      TRUE      TRUE      TRUE    TRUE
      frchzne fprimqb rtfrac1 percrte percexp sacpmlt sacpexp percfrac iflwrte baserte qb_powr qb_prms
    1   FALSE   FALSE   FALSE    TRUE    TRUE   FALSE   FALSE    FALSE   FALSE    TRUE    TRUE   FALSE
      qbrate_2a qbrate_2b sareamax axv_bexp loglamb tishape timedelay
    1     FALSE     FALSE    FALSE    FALSE    TRUE    TRUE      TRUE
```

The first 8 columns contain the model building decisions: rferr (rainfall error), arch1 (upper soil layer), arch2 (lower soil layer), qsurf (runoff mechanism), qperc (percolation), esoil (evaporation), qintfl (interflow) and q_tdh (routing). See the table below for more information:

[![model building options](modelbuildingoptions1.png)](modelbuildingoptions1.png)

The remaining 24 columns list the parameters (see table below). If the value of a parameter is TRUE, it means that parameter is used by the model, FALSE means the parameter is not used.

[![parameters](parameters.png)](parameters.png)