---
title: "Writing tables into a PostgreSQL database using R"
date: 2012-07-07 20:14:29+00:00
---

If you are using a PostgreSQL database to store your data and R to process it, then you may want to access and edit your DB directly from R.

This is possible using a package called "RPostgreSQL" available from CRAN.

Start R in a console: 

```
R
```

Then install and load the RPostgreSQL library:

```
install.packages("RPostgreSQL")
library("RPostgreSQL")
```

Choose the driver

```
drv <- dbDriver("PostgreSQL")
```

Connect to your database (I assume the database already exists on the localhost)
```
con <- dbConnect(drv, host="localhost", user= "exampleuser", password="examplepassword", dbname="exampledb")
```

Specify the schema you want to write to (optional, by default it writes in the public shema)

```
dbGetQuery(con, "SET search_path TO exampleschema")
```

Load the table from a csv (1) or an existing R archive (2):

```
# Option 1
x <- data.frame(read.table("/home/user/table.csv",sep=",",header=TRUE))

# Option 2
load("/home/user/table.rda")
x <- data.frame(table)
```

Delete any existing table with the same name:

```
if(dbExistsTable(con,"table1")) {dbRemoveTable(con,"table1")}
```

Finally write a new table:

```
dbWriteTable(con,"table1", x)
```

More details [here](http://code.google.com/p/rpostgresql/).
