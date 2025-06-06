# R Basics — Vector Indexing with Logicals
Michael Royce
2025-06-06

- [1. Calculate Murder Rates](#1-calculate-murder-rates)
- [2. Create a Logical Vector for
  Filtering](#2-create-a-logical-vector-for-filtering)
- [3. Examine the Logical Vector](#3-examine-the-logical-vector)
- [4. Use Logical Indexing to Find
  States](#4-use-logical-indexing-to-find-states)
- [5. Count How Many States Meet the
  Condition](#5-count-how-many-states-meet-the-condition)
- [6. Set Up Multiple Conditions](#6-set-up-multiple-conditions)
- [7. Combine Conditions with the AND
  Operator](#7-combine-conditions-with-the-and-operator)
- [8. Find States Meeting Both
  Conditions](#8-find-states-meeting-both-conditions)
- [Summary](#summary)

Required setup

``` r
library(tidyverse)
library(dslabs)

data(murders)
```

### 1. Calculate Murder Rates

Before we can filter by murder rates, we need to calculate the murder
rate per 100,000 people for each state.

``` r
# Calculate murder rate per 100,000 people
murder_rate <- murders$total / murders$population * 100000
murder_rate
```

     [1]  2.8244238  2.6751860  3.6295273  3.1893901  3.3741383  1.2924531
     [7]  2.7139722  4.2319369 16.4527532  3.3980688  3.7903226  0.5145920
    [13]  0.7655102  2.8369608  2.1900730  0.6893484  2.2081106  2.6732010
    [19]  7.7425810  0.8280881  5.0748655  1.8021791  4.1786225  0.9992600
    [25]  4.0440846  5.3598917  1.2128379  1.7521372  3.1104763  0.3798036
    [31]  2.7980319  3.2537239  2.6679599  2.9993237  0.5947151  2.6871225
    [37]  2.9589340  0.9396843  3.5977513  1.5200933  4.4753235  0.9825837
    [43]  3.4509357  3.2013603  0.7959810  0.3196211  3.1246001  1.3829942
    [49]  1.4571013  1.7056487  0.8871131

Creates a vector of murder rates by dividing total murders by population
and scaling to per 100,000 people.

### 2. Create a Logical Vector for Filtering

Suppose you’re moving from Italy (murder rate of 0.71 per 100,000) and
want to find states with similar or lower rates.

``` r
# Create logical vector for states with murder rate <= 0.71
index <- murder_rate <= 0.71
```

Compares each element in murder_rate to 0.71, creating a logical vector
where TRUE means the condition is met and FALSE means it’s not.

### 3. Examine the Logical Vector

``` r
# View the logical vector
index
```

     [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE
    [13] FALSE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
    [25] FALSE FALSE FALSE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE  TRUE FALSE
    [37] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE FALSE FALSE
    [49] FALSE FALSE FALSE

A vector of 51 TRUE/FALSE values, one for each state, indicating which
states have murder rates at or below 0.71.

### 4. Use Logical Indexing to Find States

``` r
# Get the names of states that meet our condition
murders$state[index]
```

    [1] "Hawaii"        "Iowa"          "New Hampshire" "North Dakota" 
    [5] "Vermont"      

Uses the logical vector index to subset the state column, returning only
the state names where index is TRUE. This gives us: Hawaii, Iowa, New
Hampshire, North Dakota, and Vermont.

### 5. Count How Many States Meet the Condition

``` r
# Count the number of TRUE values
sum(index)
```

    [1] 5

Counts the TRUE values by taking advantage of R’s coercion - TRUE
becomes 1 and FALSE becomes 0, so sum() effectively counts the TRUE
entries. This returns 5.

### 6. Set Up Multiple Conditions

Now suppose you want a safe state in the West region (where the
mountains are) with a murder rate of at most 1 per 100,000.

``` r
# Create logical vector for western states
west <- murders$region == "West"
west
```

     [1] FALSE  TRUE  TRUE FALSE  TRUE  TRUE FALSE FALSE FALSE FALSE FALSE  TRUE
    [13]  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
    [25] FALSE FALSE  TRUE FALSE  TRUE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE
    [37] FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE FALSE FALSE  TRUE
    [49] FALSE FALSE  TRUE

``` r
# Create logical vector for safe states (murder rate <= 1)
safe <- murder_rate <= 1
safe
```

     [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE
    [13]  TRUE FALSE FALSE  TRUE FALSE FALSE FALSE  TRUE FALSE FALSE FALSE  TRUE
    [25] FALSE FALSE FALSE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE  TRUE FALSE
    [37] FALSE  TRUE FALSE FALSE FALSE  TRUE FALSE FALSE  TRUE  TRUE FALSE FALSE
    [49] FALSE FALSE  TRUE

Creates two separate logical vectors - one identifying western states
and another identifying states with low murder rates.

### 7. Combine Conditions with the AND Operator

``` r
# Combine both conditions using the & operator
index <- safe & west
index
```

     [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE
    [13]  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
    [25] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
    [37] FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE FALSE FALSE FALSE
    [49] FALSE FALSE  TRUE

Uses the & (AND) operator to create a new logical vector that is TRUE
only when both conditions are met - the state must be both safe AND in
the west.

### 8. Find States Meeting Both Conditions

``` r
# Get states that are both safe and in the west
murders$state[index]
```

    [1] "Hawaii"  "Idaho"   "Oregon"  "Utah"    "Wyoming"

Uses the combined logical vector to find states that satisfy both
conditions. This returns: Hawaii, Idaho, Oregon, Utah, and Wyoming.

### Summary

``` r
# Calculate murder rates
murder_rate <- murders$total / murders$population * 100000

# Find states with murder rate <= 0.71
index <- murder_rate <= 0.71
murders$state[index]
```

    [1] "Hawaii"        "Iowa"          "New Hampshire" "North Dakota" 
    [5] "Vermont"      

``` r
sum(index)
```

    [1] 5

``` r
# Find safe western states (murder rate <= 1 AND in west region)
west <- murders$region == "West"
safe <- murder_rate <= 1
index <- safe & west
murders$state[index]
```

    [1] "Hawaii"  "Idaho"   "Oregon"  "Utah"    "Wyoming"

Reference:

- [Subsetting with
  logicals](https://rafalab.dfci.harvard.edu/dsbook-part-1/R/R-basics.html#subsetting-with-logicals)
