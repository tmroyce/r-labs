# Missing Data, Dates, and For Loops
Michael Royce
2025-06-21

- [Prerequisites](#prerequisites)
- [<span class="toc-section-number">1</span> Understanding Missing Data
  in R](#understanding-missing-data-in-r)
  - [<span class="toc-section-number">1.1</span> Recognizing Missing
    Values](#recognizing-missing-values)
  - [<span class="toc-section-number">1.2</span> Checking for Missing
    Values](#checking-for-missing-values)
  - [<span class="toc-section-number">1.3</span> Handling Missing Data
    in Calculations](#handling-missing-data-in-calculations)
  - [<span class="toc-section-number">1.4</span> Removing Missing
    Data](#removing-missing-data)
- [<span class="toc-section-number">2</span> Working with Date
  Objects](#working-with-date-objects)
  - [<span class="toc-section-number">2.1</span> Date Formatting and
    Display](#date-formatting-and-display)
  - [<span class="toc-section-number">2.2</span> Calculating Ages from
    Dates](#calculating-ages-from-dates)
- [<span class="toc-section-number">3</span> Automating with For
  Loops](#automating-with-for-loops)
  - [<span class="toc-section-number">3.1</span> For Loop with
    Calculations](#for-loop-with-calculations)
  - [<span class="toc-section-number">3.2</span> Practical For Loop
    Application](#practical-for-loop-application)
- [Summary](#summary)

Handling missing data, working with date variables, and automating
repetitive tasks with for loops

# Prerequisites

``` r
# Load required libraries
library(dplyr)      # Data manipulation
library(lubridate)  # Date handling (optional enhancement)
library(here)       # Reproducible file paths
library(tidyr)      # For drop_na() and data tidying functions

# Create sample data for examples
sample_data <- data.frame(
  participant_id = 1:10,
  age = c(25, 30, NA, 45, 22, NA, 38, 42, 29, 35),
  birth_date = c("1998-05-15", "1993-03-10", "1995-07-22", "1978-12-03",
                 "2001-09-08", "1989-11-30", "1985-04-17", "1981-01-25",
                 "1994-06-12", "1988-08-20"),
  survey_score = c(8.5, 7.2, NA, 9.1, 6.8, 8.9, NA, 7.5, 8.0, 9.3))
```

# Understanding Missing Data in R

## Recognizing Missing Values

In R, missing values are represented with the symbol NA (in capital
letters)

``` r
# View our sample data with missing values
print(sample_data)
```

       participant_id age birth_date survey_score
    1               1  25 1998-05-15          8.5
    2               2  30 1993-03-10          7.2
    3               3  NA 1995-07-22           NA
    4               4  45 1978-12-03          9.1
    5               5  22 2001-09-08          6.8
    6               6  NA 1989-11-30          8.9
    7               7  38 1985-04-17           NA
    8               8  42 1981-01-25          7.5
    9               9  29 1994-06-12          8.0
    10             10  35 1988-08-20          9.3

The presence of missing values interrupts many functions. If you try to
calculate the mean of a variable that has missing values, you’ll get an
error:

``` r
# This will return NA (not what we want)
mean(sample_data$age)
```

    [1] NA

## Checking for Missing Values

We can systematically check for missing values using the is.na()
function, which asks “are there any missing values in this object?” The
result is a logical vector (TRUE/FALSE) indicating missing positions.

Identifies which observations contain missing data using logical
functions.

``` r
# Check which age values are missing
is.na(sample_data$age)
```

     [1] FALSE FALSE  TRUE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE

``` r
# Check which values are NOT missing using the "not" operator (!)
!is.na(sample_data$age)
```

     [1]  TRUE  TRUE FALSE  TRUE  TRUE FALSE  TRUE  TRUE  TRUE  TRUE

``` r
# Count how many values are missing
sum(is.na(sample_data$age))
```

    [1] 2

``` r
# Get a summary of missing values across all columns
sample_data |>
  summarise(across(everything(), ~sum(is.na(.))))
```

      participant_id age birth_date survey_score
    1              0   2          0            2

## Handling Missing Data in Calculations

We can override default settings and tell functions to ignore missing
values. Different functions use different notation, but they produce
identical results:

``` r
# Option 1: na.rm = TRUE (remove missing values)
mean(sample_data$age, na.rm = TRUE)
```

    [1] 33.25

``` r
# Option 2: Calculate mean only for complete cases
mean(sample_data$age[!is.na(sample_data$age)])
```

    [1] 33.25

``` r
# For other functions, check the help documentation
# Some use na.omit = TRUE or use = "complete.obs"
```

## Removing Missing Data

Sometimes we need to drop observations with missing values for key
variables. This should be done with caution as it may affect the
generalizability of findings. You have several options for removing rows
with missing data:

``` r
# Modern tidyverse approach: Remove rows with ANY missing values
complete_data <- sample_data |>
  drop_na()

# Remove rows missing specific variables only
complete_age_data <- sample_data |>
  drop_na(age)

# Remove rows missing multiple specific variables
key_variables_complete <- sample_data |>
  drop_na(age, survey_score)

# Base R approach: Remove rows with ANY missing values
complete_data_base <- na.omit(sample_data)

# Filter approach: Remove rows where age is missing
complete_age_filter <- sample_data |>
  dplyr::filter(!is.na(age))

# Remove rows missing specific key variables
key_variables_complete <- sample_data |>
  dplyr::filter(!is.na(age), !is.na(survey_score))
```

# Working with Date Objects

Dates are a specialized type of object in R. Each software picks a
subjective starting date (called the epoch). R uses January 1, 1970 as
day 0, meaning all dates are calculated as the number of days relative
to this starting point

``` r
# Convert character strings to date objects
sample_data$birth_date_formatted <- as.Date(sample_data$birth_date)

# View how R stores dates internally (days since 1970-01-01)
as.numeric(as.Date("1970-01-01"))  # Day 0
```

    [1] 0

``` r
as.numeric(as.Date("2023-01-01"))  # Positive number (days after)
```

    [1] 19358

``` r
as.numeric(as.Date("1969-01-01"))  # Negative number (days before)
```

    [1] -365

## Date Formatting and Display

R expects dates in YYYY-MM-DD format, but we can change the display
format using percent signs for different date components:

``` r
# Current date and time functions
current_date <- Sys.Date()
current_time <- Sys.time()

# Format dates using percent notation
sample_data$birth_date_formatted |>
  format("%d")    # Day number
```

     [1] "15" "10" "22" "03" "08" "30" "17" "25" "12" "20"

``` r
sample_data$birth_date_formatted |>
  format("%m")    # Month number
```

     [1] "05" "03" "07" "12" "09" "11" "04" "01" "06" "08"

``` r
sample_data$birth_date_formatted |>
  format("%y")    # Two-digit year
```

     [1] "98" "93" "95" "78" "01" "89" "85" "81" "94" "88"

``` r
sample_data$birth_date_formatted |>
  format("%Y")    # Four-digit year
```

     [1] "1998" "1993" "1995" "1978" "2001" "1989" "1985" "1981" "1994" "1988"

``` r
# Combine formats for custom display
sample_data$birth_date_formatted |>
  format("%B %d, %Y")  # Example: "January 15, 1998"
```

     [1] "May 15, 1998"       "March 10, 1993"     "July 22, 1995"     
     [4] "December 03, 1978"  "September 08, 2001" "November 30, 1989" 
     [7] "April 17, 1985"     "January 25, 1981"   "June 12, 1994"     
    [10] "August 20, 1988"   

## Calculating Ages from Dates

Shows how to calculate participant ages using date arithmetic and
accounting for leap years.

Since dates are stored as numbers of days, we can perform calculations.
To calculate current age, we subtract birth date from today’s date and
convert to years:

``` r
# Calculate current age in years
sample_data <- sample_data |>
  mutate(
    birth_date_formatted = as.Date(birth_date),
    current_age = as.numeric(Sys.Date() - birth_date_formatted) / 365.25
  )

# View the calculated ages
sample_data |>
  dplyr::select(participant_id, birth_date, current_age)
```

       participant_id birth_date current_age
    1               1 1998-05-15    27.10198
    2               2 1993-03-10    32.28200
    3               3 1995-07-22    29.91650
    4               4 1978-12-03    46.54894
    5               5 2001-09-08    23.78371
    6               6 1989-11-30    35.55647
    7               7 1985-04-17    40.17796
    8               8 1981-01-25    44.40246
    9               9 1994-06-12    31.02533
    10             10 1988-08-20    36.83504

# Automating with For Loops

When there are occasions where we need to perform the same action many
times, a for loop may be a good option. R follows this decision cycle:
check if we’ve reached the last number in the sequence → if not, run the
loop body → repeat until reaching the end

``` r
# Basic for loop structure demonstration
for (value in sequence) {
  statement
}
```

``` r
# Simple example: print numbers 1 through 5
for (i in 1:5) {
  print(i)
}
```

    [1] 1
    [1] 2
    [1] 3
    [1] 4
    [1] 5

## For Loop with Calculations

loops can perform repetitive calculations

``` r
# Print squares of numbers 1 through 10
for (i in 1:10) {
  print(paste("The square of", i, "is", i^2))
}
```

    [1] "The square of 1 is 1"
    [1] "The square of 2 is 4"
    [1] "The square of 3 is 9"
    [1] "The square of 4 is 16"
    [1] "The square of 5 is 25"
    [1] "The square of 6 is 36"
    [1] "The square of 7 is 49"
    [1] "The square of 8 is 64"
    [1] "The square of 9 is 81"
    [1] "The square of 10 is 100"

``` r
# Store results in a vector instead of just printing
squares <- numeric(10)  # Pre-allocate vector
for (i in 1:10) {
  squares[i] <- i^2
}
print(squares)
```

     [1]   1   4   9  16  25  36  49  64  81 100

## Practical For Loop Application

For loops can cut down on errors from repetitive coding. Here’s a
practical example for our missing data analysis:

``` r
# Calculate summary statistics for multiple variables
variables_to_check <- c("age", "survey_score")
results <- list()

for (var in variables_to_check) {
  results[[var]] <- list(
    total_observations = nrow(sample_data),
    missing_count = sum(is.na(sample_data[[var]])),
    complete_count = sum(!is.na(sample_data[[var]])),
    mean_value = mean(sample_data[[var]], na.rm = TRUE)
  )
}

# View results
print(results)
```

    $age
    $age$total_observations
    [1] 10

    $age$missing_count
    [1] 2

    $age$complete_count
    [1] 8

    $age$mean_value
    [1] 33.25


    $survey_score
    $survey_score$total_observations
    [1] 10

    $survey_score$missing_count
    [1] 2

    $survey_score$complete_count
    [1] 8

    $survey_score$mean_value
    [1] 8.1625

# Summary

Missing Data Management:

- Missing values are designated with NA in R
- Functions offer options like na.rm = TRUE to handle missing data
- Choose between dropping observations, imputing values, or using
  methods that accommodate missing data
- The choice depends on whether participants with missing values differ
  systematically from those without

Date Object Handling:

- Dates are stored as days since January 1, 1970 (day 0)
- Use as.Date() to convert character strings to date objects
- Format dates using percent notation (%d, %m, %y, %Y)
- Calculate ages by subtracting birth dates from current date and
  dividing by 365.25

For Loop Implementation:

- Use for loops when performing the same action repeatedly
- Structure: for (value in sequence) { statement }
- Can reduce coding errors and increase efficiency

Reference:

- 
