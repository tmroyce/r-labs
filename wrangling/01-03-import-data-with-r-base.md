# Import Data — Importing Data with R-base
Michael Royce
2025-06-05

- [1. Understanding R-base Import
  Functions](#1-understanding-r-base-import-functions)
- [2. Import Data Using read.csv()](#2-import-data-using-readcsv)
- [3. Check the Data Structure Type](#3-check-the-data-structure-type)
- [5. Understanding String Handling (Historical
  Context)](#5-understanding-string-handling-historical-context)
- [Summary](#summary)

Demonstrates how to import data using R’s built-in base functions and
highlights key differences from tidyverse approaches.

### 1. Understanding R-base Import Functions

R-base provides its own set of import functions that work similarly to
tidyverse functions but with important differences. The main functions
include:

- read.table() - for general delimited files
- read.csv() - for comma-separated values
- read.delim() - for tab-delimited files

### 2. Import Data Using read.csv()

Let’s read our data using the R-base read.csv() function and store it in
a new object called dat2:

``` r
# Check your current working directory
getwd()
```

``` r
# Load the 'here' package, which helps manage file paths in a project
# It always starts paths from the project's root folder
library(here)
library(tidyverse)
```

Alternative ways to define the file path to murders.csv (No need to run
these lines—they’re just for reference)

``` r
# Define the murders_path (alternate approach)
murders_path <- "../data/murders.csv"

# alternatively, using the 'here' function (alternate approach)
murders_path <- here("data", "murders.csv")
```

``` r
# Use read.csv to import the data
# Read the CSV file containing murder data.
# 'here("data", "murders.csv")' tells R to look for the file in the 'data' folder in the root of the project.
murders_data <- read.csv(here("data", "murders.csv"))
```

### 3. Check the Data Structure Type

``` r
# Check what type of object murders_data is
class(murders_data)
```

    [1] "data.frame"

``` r
str(murders_data)
```

    'data.frame':   51 obs. of  5 variables:
     $ state     : chr  "Alabama" "Alaska" "Arizona" "Arkansas" ...
     $ abb       : chr  "AL" "AK" "AZ" "AR" ...
     $ region    : chr  "South" "West" "West" "South" ...
     $ population: int  4779736 710231 6392017 2915918 37253956 5029196 3574097 897934 601723 19687653 ...
     $ total     : int  135 19 232 93 1257 65 97 38 99 669 ...

``` r
# using tidyverse
murders_tidydata <- read_csv(here("data", "murders.csv"))
class(murders_tidydata)
```

    [1] "spec_tbl_df" "tbl_df"      "tbl"         "data.frame" 

``` r
str(murders_tidydata)
```

    spc_tbl_ [51 × 5] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
     $ state     : chr [1:51] "Alabama" "Alaska" "Arizona" "Arkansas" ...
     $ abb       : chr [1:51] "AL" "AK" "AZ" "AR" ...
     $ region    : chr [1:51] "South" "West" "West" "South" ...
     $ population: num [1:51] 4779736 710231 6392017 2915918 37253956 ...
     $ total     : num [1:51] 135 19 232 93 1257 ...
     - attr(*, "spec")=
      .. cols(
      ..   state = col_character(),
      ..   abb = col_character(),
      ..   region = col_character(),
      ..   population = col_double(),
      ..   total = col_double()
      .. )
     - attr(*, "problems")=<externalptr> 

**Important difference:** R-base import functions generate data frames,
not tibbles like tidyverse functions do.

#### Examine Column Classes

``` r
class(murders_data$abb)
```

    [1] "character"

``` r
class(murders_data$region)
```

    [1] "character"

This helps us understand how R-base functions handle different types of
data in each column.

### 5. Understanding String Handling (Historical Context)

**Important note**: As of R 4.0, it’s no longer necessary to use the
argument `stringsAsFactors=FALSE` to prevent characters from being
converted into factors. In older versions of R, strings were
automatically converted to factors unless explicitly prevented.

### Summary

- R-base import functions create data frames, while tidyverse functions
  create tibbles.
- Function names are similar between R-base and tidyverse, but outputs
  differ.
- Modern R (4.0+) no longer automatically converts strings to factors.
  Use class() to verify the structure of your imported data.
