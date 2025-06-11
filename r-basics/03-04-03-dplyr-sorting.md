# R Basics — Sorting Data Tables with dplyr
Michael Royce
2025-06-10

- [1. Order States by Population
  Size](#1-order-states-by-population-size)
- [2. Order States by Murder Rate](#2-order-states-by-murder-rate)
- [3. Sort in Descending Order](#3-sort-in-descending-order)
- [4. Handle Ties with Multiple
  Columns](#4-handle-ties-with-multiple-columns)
- [5. View More Rows with `top_n()`](#5-view-more-rows-with-top_n)
- [6. Combine Sorting with `top_n()`](#6-combine-sorting-with-top_n)
- [Summary](#summary)

Setup — Load Libraries and Prepare Data

``` r
# Install packages if needed (uncomment if not already installed)
# install.packages(c("tidyverse", "dslabs"))

# load the required packages
library(tidyverse)   # includes dplyr
library(dslabs)     # contains the murders dataset

# load the murders dataset
data(murders)

# Create a new column with murder rates per 100,000 people
murders <- mutate(murders, rate = (total / population) * 10^5)
```

### 1. Order States by Population Size

When examining a dataset, it’s often convenient to sort the table using
values in different columns. While we know about `order()` and `sort()`
functions, for ordering entire tables, the dplyr function `arrange()` is
most useful.

``` r
# Order the states by population size (ascending order by default)
murders %>% 
  arrange(population) %>% 
  head()
```

                     state abb        region population total       rate
    1              Wyoming  WY          West     563626     5  0.8871131
    2 District of Columbia  DC         South     601723    99 16.4527532
    3              Vermont  VT     Northeast     625741     2  0.3196211
    4         North Dakota  ND North Central     672591     4  0.5947151
    5               Alaska  AK          West     710231    19  2.6751860
    6         South Dakota  SD North Central     814180     8  0.9825837

The `arrange()` function sorts the entire data table by the specified
column. Here we’re sorting by population in ascending order (smallest to
largest). The `head()` function shows only the first few rows.

### 2. Order States by Murder Rate

``` r
# Order the states by murder rate - default is ascending order
murders %>% 
  arrange(rate) %>% 
  head()
```

              state abb        region population total      rate
    1       Vermont  VT     Northeast     625741     2 0.3196211
    2 New Hampshire  NH     Northeast    1316470     5 0.3798036
    3        Hawaii  HI          West    1360301     7 0.5145920
    4  North Dakota  ND North Central     672591     4 0.5947151
    5          Iowa  IA North Central    3046355    21 0.6893484
    6         Idaho  ID          West    1567582    12 0.7655102

This sorts the states from lowest to highest murder rate. The default
behavior of `arrange()` is ascending order.

### 3. Sort in Descending Order

To sort the table in descending order, we use the `desc()` function,
which transforms a vector to be in descending order.

``` r
# Order the states by murder rate in descending order
murders %>% arrange(desc(rate)) %>% head()
```

                     state abb        region population total      rate
    1 District of Columbia  DC         South     601723    99 16.452753
    2            Louisiana  LA         South    4533372   351  7.742581
    3             Missouri  MO North Central    5988927   321  5.359892
    4             Maryland  MD         South    5773552   293  5.074866
    5       South Carolina  SC         South    4625364   207  4.475323
    6             Delaware  DE         South     897934    38  4.231937

The `desc()` function reverses the order, so we see states with the
highest murder rates first.

### 4. Handle Ties with Multiple Columns

If we’re ordering by a column with ties, we can use a second column to
break the ties. Similarly, a third column can break ties between the
first and second columns, and so on.

``` r
# Order by region, then within region by murder rate
murders %>% arrange(region, rate) %>% head()
```

              state abb    region population total      rate
    1       Vermont  VT Northeast     625741     2 0.3196211
    2 New Hampshire  NH Northeast    1316470     5 0.3798036
    3         Maine  ME Northeast    1328361    11 0.8280881
    4  Rhode Island  RI Northeast    1052567    16 1.5200933
    5 Massachusetts  MA Northeast    6547629   118 1.8021791
    6      New York  NY Northeast   19378102   517 2.6679599

This first sorts by region, then within each region, sorts by murder
rate. This creates a nested sorting structure that handles ties
systematically.

### 5. View More Rows with `top_n()`

In the previous code examples, we used `head()` to avoid having a long
list of rows show up. If you want to see a larger proportion, you can
use the `top_n()` function.

``` r
# Return the top 10 states by murder rate
murders %>% top_n(10, rate)
```

                      state abb        region population total      rate
    1               Arizona  AZ          West    6392017   232  3.629527
    2              Delaware  DE         South     897934    38  4.231937
    3  District of Columbia  DC         South     601723    99 16.452753
    4               Georgia  GA         South    9920000   376  3.790323
    5             Louisiana  LA         South    4533372   351  7.742581
    6              Maryland  MD         South    5773552   293  5.074866
    7              Michigan  MI North Central    9883640   413  4.178622
    8           Mississippi  MS         South    2967297   120  4.044085
    9              Missouri  MO North Central    5988927   321  5.359892
    10       South Carolina  SC         South    4625364   207  4.475323

The `top_n()` function picks the n rows with the highest values in the
specified column (murder rate in this case). However, note that the rows
are not sorted by `top_n()`.

### 6. Combine Sorting with `top_n()`

If we want to see the top 10 states ranked by murder rate AND have them
sorted by murder rate, we need to use the `arrange()` function in
combination with `top_n()`.

``` r
# Return the top 10 states ranked by murder rate, sorted by murder rate
murders %>% arrange(desc(rate)) %>% top_n(10)
```

                      state abb        region population total      rate
    1  District of Columbia  DC         South     601723    99 16.452753
    2             Louisiana  LA         South    4533372   351  7.742581
    3              Missouri  MO North Central    5988927   321  5.359892
    4              Maryland  MD         South    5773552   293  5.074866
    5        South Carolina  SC         South    4625364   207  4.475323
    6              Delaware  DE         South     897934    38  4.231937
    7              Michigan  MI North Central    9883640   413  4.178622
    8           Mississippi  MS         South    2967297   120  4.044085
    9               Georgia  GA         South    9920000   376  3.790323
    10              Arizona  AZ          West    6392017   232  3.629527

First, `arrange(desc(rate))` sorts all states by murder rate in
descending order, then `top_n(10)` selects the top 10. This gives us
both the highest murder rate states AND displays them in sorted order.

### Summary

- `arrange()` is the primary function for sorting entire data tables
- Default behavior is ascending order; use `desc()` for descending order
- Multiple columns can be used for nested sorting to handle ties
- `head()` shows the first few rows of any table
- `top_n()` returns the top n rows but doesn’t sort them
- Combining functions like `arrange()` and `top_n()` gives you both
  selection and sorting

Reference:

- [Sorting](https://rafalab.dfci.harvard.edu/dsbook-part-1/R/tidyverse.html#sorting)
