# R Basics — Wrangling with dplyr
Michael Royce
2025-06-06

- [1. Add a New Column with `mutate()`](#1-add-a-new-column-with-mutate)
- [2. Filter Rows with `filter()`](#2-filter-rows-with-filter)
- [3. Select Specific Columns with
  `select()`](#3-select-specific-columns-with-select)
- [4. Chain Operations with the Pipe Operator, `%>%` or
  `|>`](#4-chain-operations-with-the-pipe-operator--or-)
- [Summary](#summary)

Setup

``` r
# load the required packages
library(tidyverse)   # dplyr package included in tidyverse
library(dslabs)

# load the dataset
data(murders)        # from the dslabs package
```

### 1. Add a New Column with `mutate()`

We want to calculate the murder rate per 100,000 population and add it
as a new column to our data frame.

``` r
# Add murder rate column using mutate
murders <- mutate(murders, rate = (total / population) * 100000)
```

`mutate()` function takes the data frame as the first argument (murders)
and creates a new column called rate. The calculation
`total / population * 100000` computes the murder rate per 100,000
people. Notice that we can directly reference the column names `total`
and `population` without writing murders\$total - mutate knows to look
for these variables in the murders data frame.

### 2. Filter Rows with `filter()`

Now let’s subset the data to show only states with murder rates at or
below 0.71 per 100,000.

``` r
# Filter for states with low murder rates
filter(murders, rate <= 0.71)
```

              state abb        region population total      rate
    1        Hawaii  HI          West    1360301     7 0.5145920
    2          Iowa  IA North Central    3046355    21 0.6893484
    3 New Hampshire  NH     Northeast    1316470     5 0.3798036
    4  North Dakota  ND North Central     672591     4 0.5947151
    5       Vermont  VT     Northeast     625741     2 0.3196211

`filter()` function takes the data table as the first argument and a
conditional statement as the second. It returns only the rows where the
condition is true - in this case, states where the murder rate is 0.71
or lower. Again, we can reference the `rate` column directly without the
`murders$` prefix.

### 3. Select Specific Columns with `select()`

Sometimes we only want to work with certain columns. Let’s create a new
table with just the state, region, and rate columns.

``` r
# Select only specific columns
new_table <- select(murders, state, region, rate)
```

`select()` function creates a new data table containing only the
specified columns. The first argument is the data table (`murders`), and
the remaining arguments are the column names we want to keep (`state`,
`region`, `rate`).

#### Filter the Selected Data

Now let’s filter our new table to show only the low murder rate states.

``` r
# Filter the new table
filter(new_table, rate <= 0.71)
```

              state        region      rate
    1        Hawaii          West 0.5145920
    2          Iowa North Central 0.6893484
    3 New Hampshire     Northeast 0.3798036
    4  North Dakota North Central 0.5947151
    5       Vermont     Northeast 0.3196211

This applies the same filter condition to our reduced table, showing us
just the three columns for states with murder rates at or below 0.71.

### 4. Chain Operations with the Pipe Operator, `%>%` or `|>`

We can chain operations together using the pipe operator (`%>%`) to make
our code more readable. Since R version 4.1.0, you can also use `|>`

``` r
# Chain select and filter operations using the pipe
murders %>%
  select(state, region, rate) %>%
  filter(rate <= 0.71)
```

              state        region      rate
    1        Hawaii          West 0.5145920
    2          Iowa North Central 0.6893484
    3 New Hampshire     Northeast 0.3798036
    4  North Dakota North Central 0.5947151
    5       Vermont     Northeast 0.3196211

The pipe operator (`%>%`) sends the result of one function to the next
function as its first argument. This code:

- Starts with the murders data table
- Pipes it into select() to keep only three columns
- Pipes that result into filter() to show only low murder rate states

When using the pipe, we don’t need to specify the data table argument in
subsequent functions - dplyr assumes that whatever is being piped is
what should be operated on.

# Summary

- `mutate()` - Add or modify columns
- `filter()` - Subset rows based on conditions
- `select()` - Choose specific columns
- `%>%` - Chain operations together for cleaner, more readable code

Reference:

- [Refining data
  frames](https://rafalab.dfci.harvard.edu/dsbook-part-1/R/tidyverse.html#refining-data-frames)
- [The
  pipe](https://rafalab.dfci.harvard.edu/dsbook-part-1/R/tidyverse.html#the-pipe)
