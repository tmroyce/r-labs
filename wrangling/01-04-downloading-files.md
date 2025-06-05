# Import Data — Downloading Files from the Internet
Michael Royce
2025-06-05

- [1. Reading Files Directly from
  URLs](#1-reading-files-directly-from-urls)
- [2. Downloading Files for Local
  Storage](#2-downloading-files-for-local-storage)
- [3. Understanding Temporary File
  Functions](#3-understanding-temporary-file-functions)

Demonstrates how to download and work with data files from the internet
using R, following techniques for both direct reading and local file
management.

### 1. Reading Files Directly from URLs

The most straightforward approach is to read data files directly from
their web location without downloading them first.

``` r
# Define the URL of the data file
url <- "https://raw.githubusercontent.com/rafalab/dslabs/master/inst/extdata/murders.csv"

# Read the CSV file directly from the URL
murders_data_url <- read_csv(url)
murders_data_url
```

    # A tibble: 51 × 5
       state                abb   region    population total
       <chr>                <chr> <chr>          <dbl> <dbl>
     1 Alabama              AL    South        4779736   135
     2 Alaska               AK    West          710231    19
     3 Arizona              AZ    West         6392017   232
     4 Arkansas             AR    South        2915918    93
     5 California           CA    West        37253956  1257
     6 Colorado             CO    West         5029196    65
     7 Connecticut          CT    Northeast    3574097    97
     8 Delaware             DE    South         897934    38
     9 District of Columbia DC    South         601723    99
    10 Florida              FL    South       19687653   669
    # ℹ 41 more rows

- `read_csv()` function can accept a URL instead of a local file path,
  allowing you to import data directly from the internet. This is
  convenient when you just need to analyze the data without keeping a
  local copy.

### 2. Downloading Files for Local Storage

When you want to keep a local copy of the file, use the download.file()
function.

``` r
# Download the file and save it locally as "murders.csv"
download.file(url, "murders-data-url.csv")
```

- `download.file()` function takes two arguments - the URL source and
  the destination filename. This creates a permanent local copy in your
  working directory.

### 3. Understanding Temporary File Functions

R provides helpful functions for creating temporary files and
directories that won’t conflict with existing files.

``` r
# creates a unique file path (as a character string) inside that temporary directory, or another one you specify
tempfile()
```

- `tempdir()` returns the path to a temporary directory that is unique
  to your R session.
- `tempfile()` creates a unique file path (as a character string) inside
  that temporary directory, or another one you specify.

#### Example

``` r
# Generate a temporary filename
tmp_filename <- tempfile()

# Download the file to the temporary location
download.file(url, tmp_filename)

# Read the data from the temporary file
dat <- read_csv(tmp_filename)

# Remove the temporary file to clean up
file.remove(tmp_filename)
```

- Generate a unique temporary filename with tempfile()
- Download the file to that temporary location
- Read the data into R
- Clean up by removing the temporary file with file.remove()

This approach keeps your workspace clean while still allowing you to
work with internet-hosted data files.
