---
title: "High Performance Computing Service - Part 1: Intro"
date: 2012-07-19 17:07:25+00:00
---

Imperial College London, as many other universities, provides an excellent [High Performance Computing Service](http://www3.imperial.ac.uk/ict/services/teachingandresearchservices/highperformancecomputing) for its staff and students. 

It's like a private cloud, with thousands of processors, which allows you to run highly demanding computational jobs. HPC service is particularly suitable for code which can be parallelised. There are many modules and libraries installed and you can use your own routines if written in a common programming/scripting language. For instance I used my R code. 

Here is a short tutorial on how to use HPC service. 

I first contacted the HPC Service Manager to activate an account with login credentials matching the college's ones (e.g. my username is "user"). 

Once the account is active, the system automatically creates a key pair to easily remote access (for more info read my previous post [here](http://wp.me/p2yns8-3u)) and the following file stores become available:

  - $HOME = /home/user (10GB and is intended for storing binaries, source and modest amounts of data)
  - $WORK = /work/user (at least 150GB which is intended for staging files between jobs and for long term data storage)
  - $TMPDIR = /tmp (to use only for temporary results)

Refer to them using environmental variables.

Next steps:
	
  * to prepare the scripts I'll use to run my computations (more info on [Part 2](../../../../2013/07/02/high-performance-computing-service---part-2-get-your-files-ready)).
  * copy on the server the necessary input files, scripts and R packages, if not available online (see [Part 3](../../../../2013/07/03/high-performance-computing-service---part-3-transfer-your-files)).
