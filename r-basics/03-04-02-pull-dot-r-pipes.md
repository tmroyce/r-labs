# Using pull() and the Dot Placeholder in R Pipes
Michael Royce
2025-06-06

- [1. Understanding the Data Frame
  Problem](#1-understanding-the-data-frame-problem)
- [2. Using pull() to Extract Numeric
  Values](#2-using-pull-to-extract-numeric-values)
- [3. Using pull() for Direct
  Assignment](#3-using-pull-for-direct-assignment)
- [4. Introduction to the Dot
  Placeholder](#4-introduction-to-the-dot-placeholder)
- [Summary](#summary)

Setup — Load Libraries and Prepare Data

``` r
# load the required packages
library(tidyverse)   # includes dplyr
library(dslabs)     # contains the murders dataset

# load the murders dataset
data(murders)

# Create a new column with murder rates per 100,000 people
murders <- mutate(murders, rate = (total / population) * 10^5)
```

### 1. Understanding the Data Frame Problem

First, let’s create a US murder rate calculation using `summarize()` and
examine what type of object it creates.

``` r
# Calculate average murder rate adjusted by population size
us_murder_rate <- murders %>%
  summarize(rate = sum(total) / sum(population) * 10^5)
us_murder_rate
```

          rate
    1 3.034555

``` r
# Check what class of object this creates
class(us_murder_rate)
```

    [1] "data.frame"

The `summarize()`function always returns a data frame, even when the
result is just one number. This can be problematic when you want to use
the result with functions that require a numeric value.

### 2. Using pull() to Extract Numeric Values

The `pull()` function extracts a column from a data frame and returns it
as a vector.

``` r
# Use pull() to extract the rate as a numeric value
us_murder_rate %>% pull(rate)
```

    [1] 3.034555

The `pull()` function takes the rate column from our data frame and
returns it as a numeric value instead of keeping it wrapped in a data
frame.

### 3. Using pull() for Direct Assignment

You can chain `pull()` directly in your pipe to save the numeric result
in one operation.

``` r
# Calculate and save the murder rate as a numeric value in one step
us_murder_rate <- murders %>%
  summarize(rate = sum(total) / sum(population) * 10^5) %>%
  pull(rate)

# Display the result
us_murder_rate
```

    [1] 3.034555

``` r
# Verify it's now stored as a numeric value
class(us_murder_rate)
```

    [1] "numeric"

By adding `pull(rate)` to the end of our pipe, we extract the numeric
value directly and store it as a number rather than a data frame.

### 4. Introduction to the Dot Placeholder

The dot (`.`) serves as a placeholder for the data being passed through
the pipe, giving you access to the entire data frame.

``` r
# Reset the us_murder_rate to demonstrate the dot
us_murder_rate <- murders %>%
  summarize(rate = sum(total) / sum(population) * 10^5)
us_murder_rate
```

          rate
    1 3.034555

#### Using the Dot to Access Columns

You can use the dot with the dollar sign (`$`) accessor to extract
specific columns, similar to `pull()`.

``` r
# Use the dot placeholder to access the rate column
us_murder_rate <- murders %>%
  summarize(rate = sum(total) / sum(population) * 10^5) %>%
  .$rate

# Display the result
us_murder_rate
```

    [1] 3.034555

``` r
# Verify the class
class(us_murder_rate)
```

    [1] "numeric"

The dot (`.`) represents the data frame being passed through the pipe.
When you use `.$rate`, you’re accessing the rate column of that data
frame using the dollar sign accessor, which returns the column as a
numeric vector.

### Summary

- `summarize()` and most dplyr functions return data frames, even for
  single values
- `pull()` extracts columns as vectors/numeric values from piped data
- The dot (`.`) acts as a placeholder for the entire data object in
  pipes
- Both `pull(column_name)` and `.$column_name` can extract numeric
  values from data frames
- The dot placeholder has broader applications beyond simple column
  extraction

Reference:

- [Extracting variables with
  pull](https://rafalab.dfci.harvard.edu/dsbook-part-1/R/tidyverse.html#extracting-variables-with-pull)
