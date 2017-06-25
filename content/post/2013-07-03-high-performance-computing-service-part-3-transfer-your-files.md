---
title: "High Performance Computing Service - Part 3: Transfer your files"
date: 2013-07-03 15:26:04+00:00
---

At this stage, all the files are ready.

Now, let's organize the stores adding subdirectories then transfer the files from my desktop computer to the HPC server.

  1. ON THE HPC: I have organized the stores in this way:

```
mkdir $HOME/R #for custom library 
mkdir $HOME/scripts 
mkdir $HOME/R/scripts/r #for my R scripts  
mkdir $WORK/inputs 
mkdir $WORK/outputs 
mkdir $WORK/outputs/Rdata 
mkdir $WORK/outputs/Rout
```

  2. FROM MY DESKTOP: transfer the files created in [Part 2](http://wp.me/p2yns8-3f) to the HPC server.

```
scp topmodel.tar.gz login.cx1.hpc.ic.ac.uk:$HOME/R         #to install R packages (e.g. fuse) move the tarball to HPC
scp batch.sh login.cx1.hpc.ic.ac.uk:$HOME/                 #script to set up the runs
scp fuse_1.0.tar.gz login.cx1.hpc.ic.ac.uk:$HOME/scripts   #to install R packages (e.g. fuse) move the tarball to HPC
```

  3. INSTALL PACKAGES AND ANY OTHER APPLICATION IT MAY BE NEEDED

```
module load R/2.15 intel-suite/2012
R CMD INSTALL $HOME/R/fuse_1.0.tar.gz #install the package 
# (alternatively, you can download it from cran)
```

  4. ON THE HPC: RUN THE BATCH FILE

```
qsub batch.sh
```

For examples of the above mentioned files, see [PART 2](../../../../2013/07/02/high-performance-computing-service---part-2-get-your-files-ready).

For information on using the HPC service see [http://www.hpc.ic.ac.uk](http://www.hpc.ic.ac.uk)
