# R Basics — Creating Data Frames and Tibbles
Michael Royce
2025-06-06

- [1. Create a Data Frame](#1-create-a-data-frame)
- [2. Check the Class of Character
  Columns](#2-check-the-class-of-character-columns)
- [3. Tibbles as Modern Data Frames](#3-tibbles-as-modern-data-frames)
- [4. Compare Data Frame vs Tibble Subsetting
  Behavior](#4-compare-data-frame-vs-tibble-subsetting-behavior)
- [5. Tibble Error Handling in
  Action](#5-tibble-error-handling-in-action)
- [6. Create Tibbles](#6-create-tibbles)
- [7. Convert Between Data Frames and
  Tibbles](#7-convert-between-data-frames-and-tibbles)

Setup

``` r
# load the required packages
library(tidyverse)   # Load the tidyverse package for tibble functions
library(dslabs)

# load the dataset
data(murders)        # from the dslabs package
```

### 1. Create a Data Frame

Creates a data frame using the data.frame() function with student names
and exam scores, using base R - no packages needed.

``` r
# Create a data frame with student grades
grades <- data.frame(names = c("John", "Juan", "Jean", "Yao"),
                     exam_1 = c(95, 80, 90, 85),
                     exam_2 = c(90, 85, 85, 90))

# Display the data frame
grades
```

      names exam_1 exam_2
    1  John     95     90
    2  Juan     80     85
    3  Jean     90     85
    4   Yao     85     90

The `data.frame()` function creates a rectangular data structure where
you specify column names followed by the values for each column. Each
column must have the same number of elements.

### 2. Check the Class of Character Columns

Examines what happens to character data when creating data frames

``` r
# Check the class of the names column
class(grades$names)
```

    [1] "character"

Note: In older versions of R (before 4.0), `data.frame()` would
automatically convert character vectors to factors, which could cause
unexpected behavior in analysis.

#### Prevent Character-to-Factor Conversion

Use stringsAsFactors = FALSE to keep characters as characters in older R
versions.

``` r
# Create data frame with explicit stringsAsFactors argument
grades <- data.frame(names = c("John", "Juan", "Jean", "Yao"),
                     exam_1 = c(95, 80, 90, 85),
                     exam_2 = c(90, 85, 85, 90),
                     stringsAsFactors = FALSE)

# Verify that names column is now character type
class(grades$names)
```

    [1] "character"

**Important:** As of R version 4.0+, this argument is no longer
necessary. Characters stay as characters by default when enclosed in
quotes. The `stringsAsFactors = FALSE` argument is still useful in
certain other situations.

### 3. Tibbles as Modern Data Frames

requires the tidyverse/dplyr package.

``` r
# Group data and see what type of object is returned
grouped_murders <- murders |> group_by(region)
print(grouped_murders)
```

    # A tibble: 51 × 5
    # Groups:   region [4]
       state                abb   region    population total
       <chr>                <chr> <fct>          <dbl> <dbl>
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
# Check the class of the grouped object
class(grouped_murders)
```

    [1] "grouped_df" "tbl_df"     "tbl"        "data.frame"

Notice that the output shows “A tibble” and the class includes
“grouped_df”, “tbl_df”, “tbl”, and “data.frame”.

### 4. Compare Data Frame vs Tibble Subsetting Behavior

Tibbles maintain consistent data frame structure when subsetting, unlike
regular data frames.

``` r
# With regular data frame - subsetting one column returns a vector
class(murders[,4])  # Returns "numeric" - not a data frame!
```

    [1] "numeric"

``` r
# Convert to tibble and subset - maintains tibble structure
tibble_murders <- as_tibble(murders)
class(tibble_murders[,4])  # Returns tibble class
```

    [1] "tbl_df"     "tbl"        "data.frame"

``` r
# To get the actual vector from a tibble, use $ accessor
class(tibble_murders$population)  # Returns "numeric"
```

    [1] "numeric"

This consistency is important because many functions expect data frame
input, and tibbles ensure you always get a data frame back.

### 5. Tibble Error Handling in Action

Tibbles provide helpful warnings when accessing non-existent columns.

``` r
# Regular data frame - silent failure
result1 <- murders$Population  # Note: wrong capitalization
print(result1)  # Returns NULL with no warning
```

    NULL

``` r
# Tibble - informative warning
result2 <- as_tibble(murders)$Population  # Same wrong capitalization
```

    Warning: Unknown or uninitialised column: `Population`.

``` r
print(result2)  # Returns NULL but with helpful warning message
```

    NULL

Tibbles help catch typos and coding errors by providing clear warnings
when you try to access columns that don’t exist.

### 6. Create Tibbles

Create tibbles from scratch using the tibble() function.

``` r
# Create a tibble (preferred tidyverse method)
grades_tibble <- tibble(names = c("John", "Juan", "Jean", "Yao"),
                       exam_1 = c(95, 80, 90, 85),
                       exam_2 = c(90, 85, 85, 90))

# Display the tibble
print(grades_tibble)
```

    # A tibble: 4 × 3
      names exam_1 exam_2
      <chr>  <dbl>  <dbl>
    1 John      95     90
    2 Juan      80     85
    3 Jean      90     85
    4 Yao       85     90

The `tibble()` function creates a modern data frame that’s optimized for
the tidyverse ecosystem.

### 7. Convert Between Data Frames and Tibbles

Convert existing data frames to tibbles using as_tibble().

``` r
# Create a regular data frame first
regular_df <- data.frame(names = c("John", "Juan", "Jean", "Yao"),
                        exam_1 = c(95, 80, 90, 85),
                        exam_2 = c(90, 85, 85, 90))

# Convert to tibble
converted_tibble <- as_tibble(regular_df)

# Check the class to confirm conversion
class(converted_tibble)
```

    [1] "tbl_df"     "tbl"        "data.frame"

Tibbles are the preferred data structure in the tidyverse because they
provide better display, consistent behavior, helpful error messages, and
can handle more complex data types than traditional data frames.

Reference:

- [Tibbles versus data
  frames](https://rafalab.dfci.harvard.edu/dsbook-part-1/R/tidyverse.html#tibbles-versus-data-frames)
