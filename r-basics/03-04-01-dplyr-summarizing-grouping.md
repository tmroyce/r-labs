# Summarizing and Grouping Data with dplyr
Michael Royce
2025-06-06

- [1. Create Basic Summary
  Statistics](#1-create-basic-summary-statistics)
- [2. Calculate Population-Weighted Murder
  Rate](#2-calculate-population-weighted-murder-rate)
- [3. Use Functions That Return Multiple
  Values](#3-use-functions-that-return-multiple-values)
- [4. Create Custom Function for Column
  Output](#4-create-custom-function-for-column-output)
- [5. Group Data Before Summarizing](#5-group-data-before-summarizing)
- [6. Combine Grouping with
  Summarizing](#6-combine-grouping-with-summarizing)

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

### 1. Create Basic Summary Statistics

Now we’ll use the `summarize()` function to compute minimum, median, and
maximum murder rates for states in the Western region.

``` r
# Compute summary statistics for Western states
s <- murders %>%
  filter(region == "West") %>%
  summarize(minimum = min(rate),
            median = median(rate),
            maximum = max(rate))

# View the results
s
```

       minimum   median  maximum
    1 0.514592 1.292453 3.629527

The `summarize()` function creates a new table with our chosen summary
statistics. We filter to only Western states first, then compute three
statistics and name the resulting columns.

``` r
# Access specific values from our summary
s$median
```

    [1] 1.292453

``` r
s$maximum
```

    [1] 3.629527

This shows how to extract specific values from your summarized results.
The `$` operator lets you access individual columns by name.

### 2. Calculate Population-Weighted Murder Rate

When computing rates for the entire country, we need to weight by
population rather than simply averaging state rates.

``` r
# Incorrect approach - simple average (don't do this)
mean(murders$rate)
```

    [1] 2.779125

``` r
# Correct approach - population-weighted rate
us_murder_rate <- murders %>%
  summarize(rate = sum(total) / sum(population) * 10^5)

us_murder_rate
```

          rate
    1 3.034555

This demonstrates why simple averages can be misleading. The correct
approach sums all murders and divides by total population, properly
weighting by state populations.

### 3. Use Functions That Return Multiple Values

The `quantile()` function can compute multiple summary statistics at
once, but requires special handling in `summarize()`.

``` r
# Using quantile function - this creates multiple rows
murders %>%
  filter(region == "West") %>%
  summarize(range = quantile(rate, c(0, 0.5, 1)))
```

         range
    1 0.514592
    2 1.292453
    3 3.629527

When you use a function that returns multiple values, `summarize()`
creates one row for each returned value. The quantile function with
`c(0, 0.5, 1)` returns minimum (0th percentile), median (50th
percentile), and maximum (100th percentile).

### 4. Create Custom Function for Column Output

To get multiple values as columns instead of rows, we need to create a
custom function that returns a data frame.

``` r
# Define a custom function that returns a data frame
my_quantile <- function(x){
  r <- quantile(x, c(0, 0.5, 1))
  data.frame(minimum = r[1], median = r[2], maximum = r[3])
}

# Use the custom function
murders %>%
  filter(region == "West") %>%
  summarize(my_quantile(rate))
```

       minimum   median  maximum
    1 0.514592 1.292453 3.629527

The custom function takes the quantile results and packages them into a
data frame with named columns. This gives us the same layout as our
earlier approach with separate `min()`, `median()`, and `max()` calls.

### 5. Group Data Before Summarizing

The `group_by()` function creates groups within your data, allowing you
to compute summaries for each group separately.

``` r
# Create grouped data
murders %>% group_by(region)
```

    # A tibble: 51 × 6
    # Groups:   region [4]
       state                abb   region    population total  rate
       <chr>                <chr> <fct>          <dbl> <dbl> <dbl>
     1 Alabama              AL    South        4779736   135  2.82
     2 Alaska               AK    West          710231    19  2.68
     3 Arizona              AZ    West         6392017   232  3.63
     4 Arkansas             AR    South        2915918    93  3.19
     5 California           CA    West        37253956  1257  3.37
     6 Colorado             CO    West         5029196    65  1.29
     7 Connecticut          CT    Northeast    3574097    97  2.71
     8 Delaware             DE    South         897934    38  4.23
     9 District of Columbia DC    South         601723    99 16.5 
    10 Florida              FL    South       19687653   669  3.40
    # ℹ 41 more rows

This creates a special “grouped data frame” where subsequent operations
will be applied to each group separately. Notice the output shows
“Groups: region \[4\]” indicating four regional groups.

### 6. Combine Grouping with Summarizing

group data first, then summarize each group.

``` r
# Group by region and compute median murder rate for each
murders %>%
  group_by(region) %>%
  summarize(median = median(rate))
```

    # A tibble: 4 × 2
      region        median
      <fct>          <dbl>
    1 Northeast       1.80
    2 South           3.40
    3 North Central   1.97
    4 West            1.29

This computes the median murder rate separately for each region. The
`group_by()` followed by `summarize()` pattern is one of the most
commonly used operations in data analysis, allowing you to compare
summary statistics across different categories in your data.

Reference:

- [Summarizing
  data](https://rafalab.dfci.harvard.edu/dsbook-part-1/R/tidyverse.html#summarizing-data)
