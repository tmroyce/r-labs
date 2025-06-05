# Import Data — Reading data with readr and readxl
Michael Royce
2025-06-05

- [1. Setup and Package Installation](#1-setup-and-package-installation)
- [2. File Format Inspection](#2-file-format-inspection)
- [3. Reading CSV Files with readr](#3-reading-csv-files-with-readr)
- [4. Working with Excel Files](#4-working-with-excel-files)
- [5. readr Function Reference](#5-readr-function-reference)
- [More examples](#more-examples)
- [Summary](#summary)

Demonstrates how to read data from various file formats into R using the
readr and readxl packages.

### 1. Setup and Package Installation

Loads the essential packages for data reading and provides access to
sample datasets.

``` r
# Install packages if needed (uncomment the lines below)
# install.packages(c("tidyverse", "readxl", "dslabs"))
```

``` r
# Get sample data path and list available files
extdata_path <- system.file("extdata", package = "dslabs")
extdata_files <- list.files(extdata_path)
print("Available datasets:")
```

    [1] "Available datasets:"

``` r
print(extdata_files)
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

### 2. File Format Inspection

Identify file types and examine file structure before reading data.

``` r
# Inspect the first few lines of a CSV file to understand its structure
murders_filename <- "murders.csv"
murders_path <- file.path(extdata_path, murders_filename)

# Preview first 3 lines to check delimiter and header
read_lines(murders_path, n_max = 3)
```

    [1] "state,abb,region,population,total" "Alabama,AL,South,4779736,135"     
    [3] "Alaska,AK,West,710231,19"         

The `read_lines()` function reveals that this file is comma-delimited
with a header row, confirming we should use read_csv().

### 3. Reading CSV Files with readr

Read CSV files using tidyverse functions.

``` r
# Read CSV file using readr (preferred tidyverse method)
murders_data <- read_csv(murders_path)

# Display basic information about the dataset
print(murders_data)
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

``` r
# Show first 6 rows to verify data was read correctly
head(murders_data)
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
# Check data structure
glimpse(murders_data)
```

    Rows: 51
    Columns: 5
    $ state      <chr> "Alabama", "Alaska", "Arizona", "Arkansas", "California", "…
    $ abb        <chr> "AL", "AK", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FL",…
    $ region     <chr> "South", "West", "West", "South", "West", "West", "Northeas…
    $ population <dbl> 4779736, 710231, 6392017, 2915918, 37253956, 5029196, 35740…
    $ total      <dbl> 135, 19, 232, 93, 1257, 65, 97, 38, 99, 669, 376, 7, 12, 36…

The `read_csv()` function automatically detects column types and creates
a tibble (modern data frame) with improved printing and handling.

### 4. Working with Excel Files

Handle Excel files, including multiple sheets.

``` r
# Example of working with Excel files (hypothetical example)
excel_file <- "sample_data.xlsx"

# List all sheets in an Excel file
sheet_names <- excel_sheets(excel_file)
print(sheet_names)

# Read specific sheet by name
data_sheet1 <- read_excel(excel_file, sheet = "Sheet1")

# Read specific sheet by position
data_sheet2 <- read_excel(excel_file, sheet = 2)

# Auto-detect Excel format
data_auto <- read_excel(excel_file)
```

Example with `2010_bigfive_regents.xls`

``` r
bigfive_filename <- "2010_bigfive_regents.xls"
bigfive_path <- file.path(extdata_path, bigfive_filename)
bigfive_path
```

    [1] "/Library/Frameworks/R.framework/Versions/4.5-arm64/Resources/library/dslabs/extdata/2010_bigfive_regents.xls"

``` r
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
bigfive_sheetnames <- excel_sheets(bigfive_path)
print(bigfive_sheetnames)
```

    [1] "Sheet1" "Sheet2" "Sheet3"

``` r
bigfive_sheet1 <- read_excel(bigfive_path, sheet = "Sheet1")
bigfive_sheet1
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

``` r
bigfive_sheet2 <- read_excel(bigfive_path, sheet = 2)
bigfive_sheet2
```

    # A tibble: 0 × 0

``` r
bigfive_sheet3 <- read_excel(bigfive_path, sheet = 3)
bigfive_sheet3
```

    # A tibble: 0 × 0

``` r
bigfive_data <- read_excel(bigfive_path)
bigfive_data
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

### 5. readr Function Reference

Different readr functions for various delimiters:

``` r
# Comma-separated values
data_csv <- read_csv("file.csv")

# Tab-separated values
data_tsv <- read_tsv("file.tsv")

# Space-separated values
data_table <- read_table("file.txt")

# Custom delimiter
data_custom <- read_delim("file.txt", delim = "|")

# European CSV (semicolon-separated)
data_csv2 <- read_csv2("file.csv")
```

### More examples

``` r
# Define filenames for different datasets
filename1 <- "life-expectancy-and-fertility-two-countries-example.csv"
filename2 <- "fertility-two-countries-example.csv"

# Read multiple datasets
life_exp_data <- read_csv(file.path(extdata_path, filename1))
fertility_data <- read_csv(file.path(extdata_path, filename2))
carbon_data <- read_csv(file.path(extdata_path, "carbon_emissions.csv"))


head(life_exp_data)
```

    # A tibble: 2 × 113
      country     `1960_fertility` `1960_life_expectancy` `1961_fertility`
      <chr>                  <dbl>                  <dbl>            <dbl>
    1 Germany                 2.41                   69.3             2.44
    2 South Korea             6.16                   53.0             5.99
    # ℹ 109 more variables: `1961_life_expectancy` <dbl>, `1962_fertility` <dbl>,
    #   `1962_life_expectancy` <dbl>, `1963_fertility` <dbl>,
    #   `1963_life_expectancy` <dbl>, `1964_fertility` <dbl>,
    #   `1964_life_expectancy` <dbl>, `1965_fertility` <dbl>,
    #   `1965_life_expectancy` <dbl>, `1966_fertility` <dbl>,
    #   `1966_life_expectancy` <dbl>, `1967_fertility` <dbl>,
    #   `1967_life_expectancy` <dbl>, `1968_fertility` <dbl>, …

``` r
glimpse(fertility_data)
```

    Rows: 2
    Columns: 57
    $ country <chr> "Germany", "South Korea"
    $ `1960`  <dbl> 2.41, 6.16
    $ `1961`  <dbl> 2.44, 5.99
    $ `1962`  <dbl> 2.47, 5.79
    $ `1963`  <dbl> 2.49, 5.57
    $ `1964`  <dbl> 2.49, 5.36
    $ `1965`  <dbl> 2.48, 5.16
    $ `1966`  <dbl> 2.44, 4.99
    $ `1967`  <dbl> 2.37, 4.85
    $ `1968`  <dbl> 2.28, 4.73
    $ `1969`  <dbl> 2.17, 4.62
    $ `1970`  <dbl> 2.04, 4.53
    $ `1971`  <dbl> 1.92, 4.41
    $ `1972`  <dbl> 1.80, 4.27
    $ `1973`  <dbl> 1.70, 4.09
    $ `1974`  <dbl> 1.62, 3.87
    $ `1975`  <dbl> 1.56, 3.62
    $ `1976`  <dbl> 1.53, 3.36
    $ `1977`  <dbl> 1.50, 3.11
    $ `1978`  <dbl> 1.49, 2.88
    $ `1979`  <dbl> 1.48, 2.69
    $ `1980`  <dbl> 1.47, 2.52
    $ `1981`  <dbl> 1.47, 2.38
    $ `1982`  <dbl> 1.46, 2.24
    $ `1983`  <dbl> 1.46, 2.11
    $ `1984`  <dbl> 1.46, 1.98
    $ `1985`  <dbl> 1.45, 1.86
    $ `1986`  <dbl> 1.44, 1.75
    $ `1987`  <dbl> 1.43, 1.67
    $ `1988`  <dbl> 1.41, 1.63
    $ `1989`  <dbl> 1.38, 1.61
    $ `1990`  <dbl> 1.36, 1.61
    $ `1991`  <dbl> 1.34, 1.63
    $ `1992`  <dbl> 1.32, 1.65
    $ `1993`  <dbl> 1.31, 1.66
    $ `1994`  <dbl> 1.31, 1.65
    $ `1995`  <dbl> 1.31, 1.63
    $ `1996`  <dbl> 1.32, 1.59
    $ `1997`  <dbl> 1.33, 1.54
    $ `1998`  <dbl> 1.34, 1.48
    $ `1999`  <dbl> 1.35, 1.41
    $ `2000`  <dbl> 1.35, 1.35
    $ `2001`  <dbl> 1.35, 1.30
    $ `2002`  <dbl> 1.35, 1.25
    $ `2003`  <dbl> 1.35, 1.22
    $ `2004`  <dbl> 1.35, 1.20
    $ `2005`  <dbl> 1.35, 1.20
    $ `2006`  <dbl> 1.36, 1.20
    $ `2007`  <dbl> 1.36, 1.21
    $ `2008`  <dbl> 1.37, 1.23
    $ `2009`  <dbl> 1.38, 1.25
    $ `2010`  <dbl> 1.39, 1.27
    $ `2011`  <dbl> 1.40, 1.29
    $ `2012`  <dbl> 1.41, 1.30
    $ `2013`  <dbl> 1.42, 1.32
    $ `2014`  <dbl> 1.43, 1.34
    $ `2015`  <dbl> 1.44, 1.36

``` r
summary(carbon_data)
```

          Year     
     Min.   :1751  
     1st Qu.:1817  
     Median :1882  
     Mean   :1882  
     3rd Qu.:1948  
     Max.   :2014  
     Total carbon emissions from fossil fuel consumption and cement production (million metric tons of C)
     Min.   :   3.00                                                                                     
     1st Qu.:  13.75                                                                                     
     Median : 264.00                                                                                     
     Mean   :1522.98                                                                                     
     3rd Qu.:1431.50                                                                                     
     Max.   :9855.00                                                                                     

### Summary

``` r
# Always inspect files first
read_lines("your_file.csv", n_max = 3)

# Use appropriate function based on delimiter
# - read_csv() for comma-separated
# - read_tsv() for tab-separated
# - read_delim("file.txt", delim = "|") for custom delimiters

# For Excel files, check available sheets
excel_sheets("your_file.xlsx")

# Verify data after reading
head(your_data)
glimpse(your_data)

# Use full file paths for reproducibility
full_path <- file.path("data", "your_file.csv")
data <- read_csv(full_path)
```
