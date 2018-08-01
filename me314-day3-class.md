Working with RSQLite
================
Kenneth Benoit (w/inspiration from **RSQLite** vignette)

## Why and how to use RSQLite

  - RSQLite is the easiest way to use a database from R because the
    package itself contains [SQLite](https://www.sqlite.org); no
    external software is needed.

  - RSQLite is a DBI-compatible interface which means you primarily use
    functions defined in the DBI package, so you should always start by
    loading DBI, not RSQLite:

<!-- end list -->

``` r
library("DBI")
```

## Creating a new database

To create a new SQLite database, you simply supply the filename to
`dbConnect()`:

``` r
mydb <- dbConnect(RSQLite::SQLite(), "my-db.sqlite")
dbDisconnect(mydb)
unlink("my-db.sqlite")
```

If you just need a temporary database, use either `""` (for an on-disk
database) or `":memory:"` or `"file::memory:"` (for a in-memory
database). This database will be automatically deleted when you
disconnect from it.

``` r
mydb <- dbConnect(RSQLite::SQLite(), "")
dbDisconnect(mydb)
```

## Adding data to the database

1.  Load a table using `read.csv()`

<!-- end list -->

``` r
airports <- read.csv("nycflights13/airports.csv")[, -1]
planes <- read.csv("nycflights13/planes.csv")[, -1]
```

2.  Add to the database using `dbWriteTable()`:

<!-- end list -->

``` r
mydb <- dbConnect(RSQLite::SQLite(), "")
dbWriteTable(mydb, "airports", airports)
dbWriteTable(mydb, "planes", planes)
dbListTables(mydb)
## [1] "airports" "planes"
```

## Queries

Queries in **RSQLite** pass SQL code directly, using `dbGetQuery()`:

``` r
dbGetQuery(mydb, 'SELECT * FROM airports LIMIT 5')
##   faa                          name      lat       lon  alt tz dst
## 1 04G             Lansdowne Airport 41.13047 -80.61958 1044 -5   A
## 2 06A Moton Field Municipal Airport 32.46057 -85.68003  264 -6   A
## 3 06C           Schaumburg Regional 41.98934 -88.10124  801 -6   A
## 4 06N               Randall Airport 41.43191 -74.39156  523 -5   A
## 5 09J         Jekyll Island Airport 31.07447 -81.42778   11 -5   A
##              tzone
## 1 America/New_York
## 2  America/Chicago
## 3  America/Chicago
## 4 America/New_York
## 5 America/New_York
```

``` r
dbGetQuery(mydb, 'SELECT * FROM planes WHERE engines > 2')
##   tailnum year                    type           manufacturer
## 1  N281AT   NA Fixed wing multi engine       AIRBUS INDUSTRIE
## 2  N381AA 1956 Fixed wing multi engine                DOUGLAS
## 3  N670US 1990 Fixed wing multi engine                 BOEING
## 4  N840MQ 1974 Fixed wing multi engine           CANADAIR LTD
## 5  N854NW 2004 Fixed wing multi engine                 AIRBUS
## 6  N856NW 2004 Fixed wing multi engine                 AIRBUS
## 7  N905FJ 1986 Fixed wing multi engine AVIONS MARCEL DASSAULT
##                model engines seats speed        engine
## 1           A340-313       4   375    NA     Turbo-jet
## 2             DC-7BF       4   102   232 Reciprocating
## 3            747-451       4   450    NA     Turbo-jet
## 4              CF-5D       4     2    NA     Turbo-jet
## 5           A330-223       3   379    NA     Turbo-fan
## 6           A330-223       3   379    NA     Turbo-fan
## 7 MYSTERE FALCON 900       3    12    NA     Turbo-fan
```

## Batched queries

If you run a query and the results don’t fit in memory, you can use
`dbSendQuery()`, `dbFetch()` and `dbClearResults()` to retrieve the
results in batches. By default `dbFetch()` will retrieve all available
rows: use `n` to set the maximum number of rows to return.

``` r
rs <- dbSendQuery(mydb, 'SELECT * FROM planes')
while (!dbHasCompleted(rs)) {
    df <- dbFetch(rs, n = 10)
    print(nrow(df))
}
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 10
## [1] 2
dbClearResult(rs)
```
