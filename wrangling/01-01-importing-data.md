# Importing data from spreadsheets
Michael Royce
2025-06-07

- [1. Understanding Spreadsheet
  Structure](#1-understanding-spreadsheet-structure)
- [2. Understanding Your Working
  Directory](#2-understanding-your-working-directory)
- [3. Locating Example Data Files](#3-locating-example-data-files)
- [4. Creating Full File Paths](#4-creating-full-file-paths)
- [5. Copying Files to Your Working
  Directory](#5-copying-files-to-your-working-directory)
- [6. Inspecting File Structure Before
  Import](#6-inspecting-file-structure-before-import)
- [7. Importing Data with readr
  Functions](#7-importing-data-with-readr-functions)
- [8. Understanding readr and readxl
  Functions](#8-understanding-readr-and-readxl-functions)
- [9. Comparing with R-base Import
  Functions](#9-comparing-with-r-base-import-functions)
- [10. Downloading Files Directly from the
  Internet](#10-downloading-files-directly-from-the-internet)
- [11. Downloading Files for Local
  Storage](#11-downloading-files-for-local-storage)
- [12. Using Temporary Files for Internet
  Downloads](#12-using-temporary-files-for-internet-downloads)
- [13. Saving and Loading R Objects](#13-saving-and-loading-r-objects)

Setup — Load Libraries

``` r
# Install packages if needed (uncomment if not already installed)
# install.packages(c("tidyverse", "readxl", "dslabs"))

# load the required packages
library(tidyverse)   # includes readr
library(readxl)
library(here)        # optional, prefer to use `here()`, builds a file path relative to project’s root directory
library(dslabs)      # contains the murders dataset
```

### 1. Understanding Spreadsheet Structure

Spreadsheets store data in rows and columns, essentially functioning as
a file version of a data frame. When working with text-based spreadsheet
files:

- Rows are separated by returns (new lines)
- Columns are separated by delimiters such as:
  - Comma (,) - most common
  - Semicolon (;)
  - White space or tab (

Many spreadsheet files include a header row containing column names
rather than data. Most reading functions assume a header exists. Some
formats (like Microsoft Excel .xls files) are proprietary and require
special packages to read

### 2. Understanding Your Working Directory

Set up your file system workspace for data import operations.

The working directory is where R looks for files by default.

``` r
# See your current working directory
getwd()

# Change your working directory (uncomment and modify path as needed)
# setwd("path/to/your/project/directory")
```

- Create a dedicated directory for each analysis project
- Keep raw data files organized in a data sub-directory within your
  project folder
- Use the working directory consistently to avoid path confusion

### 3. Locating Example Data Files

Find and access sample data files provided by the dslabs package.

``` r
# Get the path to example data files in the dslabs package
extdata_path <- system.file("extdata", package = "dslabs")
extdata_path
```

    [1] "/Library/Frameworks/R.framework/Versions/4.5-arm64/Resources/library/dslabs/extdata"

``` r
# List all available data files
list.files(extdata_path)
```

     [1] "2010_bigfive_regents.xls"                               
     [2] "calificaciones.csv"                                     
     [3] "carbon_emissions.csv"                                   
     [4] "fertility-two-countries-example.csv"                    
     [5] "HRlist2.txt"                                            
     [6] "life-expectancy-and-fertility-two-countries-example.csv"
     [7] "murders.csv"                                            
     [8] "olive.csv"                                              
     [9] "RD-Mortality-Report_2015-18-180531.pdf"                 
    [10] "ssa-death-probability.csv"                              

This will show you various data files including `murders.csv`.

### 4. Creating Full File Paths

Generate complete file paths using system-appropriate path separators.

``` r
# Define the filename we want to work with
murders_filename <- "murders.csv"

# Generate a full path using file.path() (recommended)
murders_fullpath <- file.path(extdata_path, murders_filename)
murders_fullpath
```

    [1] "/Library/Frameworks/R.framework/Versions/4.5-arm64/Resources/library/dslabs/extdata/murders.csv"

Note: Don’t use paste() for file paths as it doesn’t handle different
operating systems’ path conventions correctly

Why use `file.path()`: This function automatically uses the correct
slash direction (/ or ) depending on your operating system.

### 5. Copying Files to Your Working Directory

Make a local copy of the data file in your working directory for easier
access.

``` r
# Copy the file from the dslabs package to your working directory, if prefer
file.copy(murders_fullpath, getwd())  

# Verify the file was copied successfully to current working directory, if prefer
file.exists(murders_filename)
```

After copying, you can reference the file by just its name rather than
the full path.

#### Optional: Using `here()` function from here package (my preference)

``` r
# I prefer to use `here()` function from here package to copy file to sub-dir, data/ 
file.copy(murders_fullpath, here("data"))      
```

    [1] FALSE

``` r
# Verify the file was copied successfully, if saved in current working directory
file.exists(here("data", murders_filename))
```

    [1] TRUE

If prefer not to use `here()` function, you can use
`"relative-path/filename.csv"` instead of `here("data", "murders.csv")`

### 6. Inspecting File Structure Before Import

Examine the file structure to understand its format and determine the
appropriate import function.

``` r
# Look at the first 3 lines of the file using `here`
read_lines(here("data", "murders.csv"), n_max = 3)  # or `"relative-path/filename.csv"` if not use `here()`
```

    [1] "state,abb,region,population,total" "Alabama,AL,South,4779736,135"     
    [3] "Alaska,AK,West,710231,19"         

This preview shows you: - What delimiter is used (commas in this case) -
Whether the file has a header row (it does) - The general structure of
the data

### 7. Importing Data with readr Functions

Import the CSV file using tidyverse’s readr package, which creates a
tibble.

``` r
# Read the CSV file using read_csv() from readr
# You can also use the relative path, full path, or filename (wd) if prefer
# murders_raw <- read_csv("murders.csv")
# murders_raw <- read_csv("data/murders.csv")

# Read the CSV file using read_csv() from readr
murders_raw <- read_csv(here("data", "murders.csv"))

# View the first few rows
head(murders_raw)
```

    # A tibble: 6 × 5
      state      abb   region population total
      <chr>      <chr> <chr>       <dbl> <dbl>
    1 Alabama    AL    South     4779736   135
    2 Alaska     AK    West       710231    19
    3 Arizona    AZ    West      6392017   232
    4 Arkansas   AR    South     2915918    93
    5 California CA    West     37253956  1257
    6 Colorado   CO    West      5029196    65

``` r
# Check the structure
class(murders_raw)  # This will show it's a tibble
```

    [1] "spec_tbl_df" "tbl_df"      "tbl"         "data.frame" 

When you run `read_csv()`, you’ll receive a message showing the data
types that were automatically detected for each column.

### 8. Understanding readr and readxl Functions

the different import functions available for various file formats.

readr functions (for text-based files):

- `read_csv()` - comma-separated values
- `read_tsv()` - tab-separated values
- `read_csv2()` - semicolon-separated (European standard)
- `read_delim()` - custom delimiter
- `read_table()` - white space separated

readxl functions (for Excel files):

- `read_excel()` - auto-detects .xls or .xlsx
- `read_xls()` - specifically for .xls files
- `read_xlsx()` - specifically for .xlsx files

``` r
# Example: If you had an Excel file, you could:
excel_sheets("filename.xlsx")  # See available sheets

# List all sheets in an Excel file
excel_sheets("filename.xlsx")

# Read specific sheet by name
read_excel(excel_file, sheet = "Sheet1")

# Read specific sheet by position
read_excel(excel_file, sheet = 2)

# Auto-detect Excel format
read_excel(excel_file)
```

#### Working with Excel Files

``` r
bigfive_filename <- "2010_bigfive_regents.xls"
bigfive_path <- file.path(extdata_path, bigfive_filename)
bigfive_path
```

    [1] "/Library/Frameworks/R.framework/Versions/4.5-arm64/Resources/library/dslabs/extdata/2010_bigfive_regents.xls"

``` r
# Look at the first 3 lines 
read_lines(bigfive_path, n_max = 3)
```

     [1] "\xd0\xcf\021\u0871\032\xe1" "\001"                      
     [3] "\001\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff"   "\001\006"                  
     [5] "\001\002"                   "\001"                      
     [7] "\001"                       "\001"                      
     [9] "\001"                       "\001A"                     
    [11] "\001"                       "\001"                      
    [13] "\001"                       "\001"                      
    [15] "\001C"                      "\001"                      
    [17] "\001"                       "\001"                      
    [19] "\001C"                      ""                          
    [21] "\001"                       "\001"                      
    [23] "\001"                       "\001"                      
    [25] "\n"                         "\001\xff;_(@_)  }\b\x91"  

``` r
# List all sheets in an Excel file
excel_sheets(bigfive_path)
```

    [1] "Sheet1" "Sheet2" "Sheet3"

``` r
# Read excel file into R
bigfive_raw<- read_excel(bigfive_path)
bigfive_raw
```

    # A tibble: 104 × 6
       label     `INTEGRATED ALGEBRA` `GLOBAL HISTORY` `LIVING ENVIRONMENT` ENGLISH
       <chr>                    <dbl>            <dbl>                <dbl>   <dbl>
     1 test_year                 2010             2010                 2010    2010
     2 Scores                  131024           113804               104201  103886
     3 0                           56               55                   66     165
     4 1                           NA                8                    3      69
     5 2                            1                9                    2     237
     6 3                           NA                3                    1     190
     7 4                            3               15                    1     109
     8 5                            2               11                   10     122
     9 6                            4               29                    3     151
    10 7                            1               37                    2     175
    # ℹ 94 more rows
    # ℹ 1 more variable: `U.S. HISTORY` <dbl>

### 9. Comparing with R-base Import Functions

Understand the differences between tidyverse and base R import
functions.

``` r
# Import the same file using read.csv() from base R
m_base <- read.csv(here("data", "murders.csv"))

# Import the same file using read_csv() from readr
m_readr <- read_csv(here("data", "murders.csv"))

# Compare the object types
class(m_base)   # data.frame (from base R)
```

    [1] "data.frame"

``` r
class(m_readr)  # tibble (from readr)
```

    [1] "spec_tbl_df" "tbl_df"      "tbl"         "data.frame" 

``` r
# Check column classes
class(m_base$abb)    # character (as of R 4.0+)
```

    [1] "character"

``` r
class(m_readr$abb)   # character (readr preserves)
```

    [1] "character"

``` r
class(m_base$region) # character (no longer auto-converts to factor)
```

    [1] "character"

``` r
class(m_readr$region) # character 
```

    [1] "character"

- readr functions create tibbles and provide more informative output
- Base R functions create traditional data frames
- Both preserve character data as characters (in modern R versions)

### 10. Downloading Files Directly from the Internet

Import data directly from web URLs without saving local copies.

``` r
# Define a URL to the same data file on GitHub
url <- "https://raw.githubusercontent.com/rafalab/dslabs/master/inst/extdata/murders.csv"

# Read directly from the URL
dat_from_web <- read_csv(url)

# Verify it worked
head(dat_from_web)
```

This approach lets you work with the most current version of online
datasets without managing local files.

### 11. Downloading Files for Local Storage

Download internet files to your local system for offline access or
processing.

``` r
# Define a URL to the same data file on GitHub
url <- "https://raw.githubusercontent.com/rafalab/dslabs/master/inst/extdata/murders.csv"

# Download the file and save it locally
download.file(url, "murders_downloaded.csv")

# Verify the download
file.exists("murders_downloaded.csv")

# Read the downloaded file
dat_downloaded <- read_csv("murders_downloaded.csv")
```

### 12. Using Temporary Files for Internet Downloads

Download and process files temporarily without cluttering your working
directory.

``` r
# Create a temporary filename
tempfile()  # Shows what a temp filename looks like

# Download, process, and clean up in one workflow
tmp_filename <- tempfile()
download.file(url, tmp_filename)
dat_temp <- read_csv(tmp_filename)
file.remove(tmp_filename)  # Clean up the temporary file

# Verify the data was imported correctly
head(dat_temp)
```

When you need to process internet data but don’t want to keep permanent
local copies.

### 13. Saving and Loading R Objects

Save and load processed R data objects for future use.

`save()` and `load()`

- `save()`: Saves one or more R objects to a .rda file, preserving the
  object names.
- `load()`: Loads objects saved with `save()`. The objects are restored
  in your R environment with their original names.

``` r
# save the murders_raw data object to a .rda file 
save(murders_raw, file = here("rdas", "murders_raw.rda"))

# load the data saved with save()
load(here("rdas", "murders_raw.rda"))
```

`saveRDS()` and `readRDS()`

- `saveRDS()`: Saves a single R object to a .rda file, without
  preserving the object name.
- `readRDS()`: Reads a single object saved with `saveRDS()`. You can
  assign it to any variable name you choose.

``` r
# save the murders_raw data object to a .rda file 
saveRDS(murders_raw, file = here("rdas", "murders_raw.rda"))  # or file = "relative-path"

# load the data saved with saveRDS(), assigning it to a new variable name
murders_rds <- readRDS(here("rdas", "murders_raw.rda"))

#Verify the contents
head(murders_rds) # Or any other data inspection command.
```

    # A tibble: 6 × 5
      state      abb   region population total
      <chr>      <chr> <chr>       <dbl> <dbl>
    1 Alabama    AL    South     4779736   135
    2 Alaska     AK    West       710231    19
    3 Arizona    AZ    West      6392017   232
    4 Arkansas   AR    South     2915918    93
    5 California CA    West     37253956  1257
    6 Colorado   CO    West      5029196    65

Reference:

- [Importing
  data](https://rafalab.dfci.harvard.edu/dsbook-part-1/R/importing-data.html)
- [Organizing a data science
  project](https://rafalab.dfci.harvard.edu/dsbook-part-1/productivity/reproducible-projects.html#sec-organizing)
