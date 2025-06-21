# Filter, Order, and Bivariate Descriptive Statistics
Michael Royce
2025-06-20

- [Prerequisites](#prerequisites)
- [1. Ordering Data by Column Values](#1-ordering-data-by-column-values)
- [2. Filtering Participants by Specific
  Criteria](#2-filtering-participants-by-specific-criteria)
- [3. Calculating Bivariate Descriptive Statistics with
  `group_by()`](#3-calculating-bivariate-descriptive-statistics-with-group_by)
- [4. Creating Professional Bivariate Tables with table
  summary](#4-creating-professional-bivariate-tables-with-table-summary)
- [5. Adding Statistical Tests to Compare
  Groups](#5-adding-statistical-tests-to-compare-groups)
- [Summary](#summary)

How to filter and order datasets, and calculate bivariate descriptive
statistics to compare distributions of variables across different
groups.

### Prerequisites

``` r
# Load required libraries
library(tidyverse)  # dplyr, For data manipulation (arrange, filter, group_by)
library(here)       # For file path management
library(gtsummary)  # For table summary functions
```

``` r
# Load the epl25 dataset
epl25 <- readRDS(here("data", "raw", "epl_2024_25.rds"))  # Loads the dataset from rdas folder

# check data
glimpse(epl25)
```

    Rows: 702
    Columns: 13
    $ ...1                     <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14…
    $ Squad                    <chr> "Liverpool", "Liverpool", "Liverpool", "Liver…
    $ Player                   <chr> "Mohamed Salah", "Virgil van Dijk", "Ryan Gra…
    $ Nation                   <chr> "EGY", "NED", "NED", "ARG", "FRA", "HUN", "SC…
    $ Pos                      <chr> "FW", "DF", "MF", "MF", "DF", "MF", "DF", "GK…
    $ Age                      <dbl> 32, 33, 22, 25, 25, 23, 30, 31, 27, 25, 25, 2…
    $ Gls                      <dbl> 29, 3, 0, 5, 1, 6, 0, 0, 13, 3, 10, 3, 6, 0, …
    $ Ast                      <dbl> 18, 1, 4, 5, 2, 6, 1, 0, 5, 6, 4, 3, 3, 0, 1,…
    $ MP                       <dbl> 38, 37, 37, 35, 31, 36, 33, 28, 36, 33, 35, 3…
    $ Starts_Playing_Time      <dbl> 38, 37, 37, 30, 30, 29, 29, 28, 28, 28, 23, 1…
    $ Min_Playing_Time         <dbl> 3371, 3330, 3160, 2599, 2560, 2491, 2482, 250…
    $ Mins_Per_90_Playing_Time <dbl> 37.5, 37.0, 35.1, 28.9, 28.4, 27.7, 27.6, 27.…
    $ PlayerURL                <chr> "https://fbref.com/en/players/e342ad68/Mohame…

``` r
head(epl25, 11)
```

    # A tibble: 11 × 13
        ...1 Squad   Player Nation Pos     Age   Gls   Ast    MP Starts_Playing_Time
       <dbl> <chr>   <chr>  <chr>  <chr> <dbl> <dbl> <dbl> <dbl>               <dbl>
     1     1 Liverp… Moham… EGY    FW       32    29    18    38                  38
     2     2 Liverp… Virgi… NED    DF       33     3     1    37                  37
     3     3 Liverp… Ryan … NED    MF       22     0     4    37                  37
     4     4 Liverp… Alexi… ARG    MF       25     5     5    35                  30
     5     5 Liverp… Ibrah… FRA    DF       25     1     2    31                  30
     6     6 Liverp… Domin… HUN    MF       23     6     6    36                  29
     7     7 Liverp… Andre… SCO    DF       30     0     1    33                  29
     8     8 Liverp… Aliss… BRA    GK       31     0     0    28                  28
     9     9 Liverp… Luis … COL    FW       27    13     5    36                  28
    10    10 Liverp… Trent… ENG    DF       25     3     6    33                  28
    11    11 Liverp… Cody … NED    FW       25    10     4    35                  23
    # ℹ 3 more variables: Min_Playing_Time <dbl>, Mins_Per_90_Playing_Time <dbl>,
    #   PlayerURL <chr>

``` r
# Select variables of interest, convert Age to numeric and create age groups as factor directly
epl25 <- epl25 %>%
  select(
    Squad, Player, Nation, Pos, Age, Gls, Ast
  ) %>%
  mutate(
    Age = as.numeric(Age),                 # Convert character to numeric first
    age_group = factor(                    # Create as factor directly
      case_when(
        is.na(Age) ~ "Unknown age",        # Handle any NAs created by conversion
        Age < 23 ~ "Under 23",
        Age >= 23 & Age <= 30 ~ "23-30",
        Age > 30 ~ "30+",
        TRUE ~ "other"                     # Catch-all for any other cases
      ),
      levels = c("Under 23", "23-30", "30+", "Unknown age", "other")  # Set logical order
    )
  )

# Check age group distribution
epl25 %>%
  count(age_group, sort = TRUE)
```

    # A tibble: 4 × 2
      age_group       n
      <fct>       <int>
    1 23-30         340
    2 Under 23      266
    3 30+            89
    4 Unknown age     7

``` r
glimpse(epl25)
```

    Rows: 702
    Columns: 8
    $ Squad     <chr> "Liverpool", "Liverpool", "Liverpool", "Liverpool", "Liverpo…
    $ Player    <chr> "Mohamed Salah", "Virgil van Dijk", "Ryan Gravenberch", "Ale…
    $ Nation    <chr> "EGY", "NED", "NED", "ARG", "FRA", "HUN", "SCO", "BRA", "COL…
    $ Pos       <chr> "FW", "DF", "MF", "MF", "DF", "MF", "DF", "GK", "FW", "DF", …
    $ Age       <dbl> 32, 33, 22, 25, 25, 23, 30, 31, 27, 25, 25, 23, 27, 25, 28, …
    $ Gls       <dbl> 29, 3, 0, 5, 1, 6, 0, 0, 13, 3, 10, 3, 6, 0, 0, 5, 0, 0, 0, …
    $ Ast       <dbl> 18, 1, 4, 5, 2, 6, 1, 0, 5, 6, 4, 3, 3, 0, 1, 2, 2, 0, 0, 2,…
    $ age_group <fct> 30+, 30+, Under 23, 23-30, 23-30, 23-30, 23-30, 30+, 23-30, …

``` r
head(epl25, 11)
```

    # A tibble: 11 × 8
       Squad     Player                 Nation Pos     Age   Gls   Ast age_group
       <chr>     <chr>                  <chr>  <chr> <dbl> <dbl> <dbl> <fct>    
     1 Liverpool Mohamed Salah          EGY    FW       32    29    18 30+      
     2 Liverpool Virgil van Dijk        NED    DF       33     3     1 30+      
     3 Liverpool Ryan Gravenberch       NED    MF       22     0     4 Under 23 
     4 Liverpool Alexis Mac Allister    ARG    MF       25     5     5 23-30    
     5 Liverpool Ibrahima Konaté        FRA    DF       25     1     2 23-30    
     6 Liverpool Dominik Szoboszlai     HUN    MF       23     6     6 23-30    
     7 Liverpool Andrew Robertson       SCO    DF       30     0     1 23-30    
     8 Liverpool Alisson                BRA    GK       31     0     0 30+      
     9 Liverpool Luis Díaz              COL    FW       27    13     5 23-30    
    10 Liverpool Trent Alexander-Arnold ENG    DF       25     3     6 23-30    
    11 Liverpool Cody Gakpo             NED    FW       25    10     4 23-30    

### 1. Ordering Data by Column Values

Arranges the rows of your dataset in ascending or descending order based
on values in selected columns.

``` r
# Order data by ascending age (youngest to oldest) and show first 11 rows
epl25 %>%
  arrange(Age) %>%  # Arranges rows from lowest to highest age
  head(11)          # Shows only the first 11 rows
```

    # A tibble: 11 × 8
       Squad                   Player       Nation Pos     Age   Gls   Ast age_group
       <chr>                   <chr>        <chr>  <chr> <dbl> <dbl> <dbl> <fct>    
     1 Brighton & Hove Albion  Harry Howell ENG    FW       15     0     0 Under 23 
     2 Tottenham Hotspur       Luca Willia… ENG    FW       15    NA    NA Under 23 
     3 Arsenal                 Jack Porter  ENG    GK       16    NA    NA Under 23 
     4 Chelsea                 Shumaira Mh… ENG    MF       16     0     0 Under 23 
     5 Brighton & Hove Albion  Freddie Sim… ENG    DF       16    NA    NA Under 23 
     6 Manchester United       Chidozie Ob… DEN    FW       16     0     0 Under 23 
     7 Manchester United       Godwill Kuk… ENG    DF       16    NA    NA Under 23 
     8 Wolverhampton Wanderers Wesley Okod… ENG    FW       16    NA    NA Under 23 
     9 Tottenham Hotspur       Mikey Moore  ENG    FW       16     0     1 Under 23 
    10 Tottenham Hotspur       Malachi Har… ENG    DF       16    NA    NA Under 23 
    11 Liverpool               Amara Nallo  ENG    DF       17    NA    NA Under 23 

To reverse the order and see top goalscorers first:

``` r
# Order data by descending goals (highest to lowest) and show first 11 rows
epl25 %>%
  arrange(desc(Gls)) %>%  # Arranges rows from highest to lowest goals
  head(11)                # Shows only the first 11 rows
```

    # A tibble: 11 × 8
       Squad                   Player       Nation Pos     Age   Gls   Ast age_group
       <chr>                   <chr>        <chr>  <chr> <dbl> <dbl> <dbl> <fct>    
     1 Liverpool               Mohamed Sal… EGY    FW       32    29    18 30+      
     2 Newcastle United        Alexander I… SWE    FW       24    23     6 23-30    
     3 Manchester City         Erling Haal… NOR    FW       24    22     3 23-30    
     4 Nottingham Forest       Chris Wood   NZL    FW       32    20     3 30+      
     5 Brentford               Bryan Mbeumo CMR    FW       24    20     7 23-30    
     6 Brentford               Yoane Wissa  COD    FW       27    19     4 23-30    
     7 Aston Villa             Ollie Watki… ENG    FW       28    16     8 23-30    
     8 Chelsea                 Cole Palmer  ENG    MF,FW    22    15     8 Under 23 
     9 Wolverhampton Wanderers Matheus Cun… BRA    MF,FW    25    15     6 23-30    
    10 Crystal Palace          Jean-Philip… FRA    FW       27    14     2 23-30    
    11 Wolverhampton Wanderers Jørgen Stra… NOR    FW       24    14     4 23-30    

`arrange()` orders rows, and `desc()` reverses the order to descending.

### 2. Filtering Participants by Specific Criteria

Creates a subset of your dataset by selecting only rows that match
certain criteria, like players from a specific position or team.

``` r
# Filter to keep only forward players
forwards <- epl25 %>%
  filter(Pos == "FW")  # Keeps only rows where position equals "FW"

# Now you can calculate statistics on just the forwards
forwards %>%
  summarize(mean_goals = mean(Gls, na.rm = TRUE))
```

    # A tibble: 1 × 1
      mean_goals
           <dbl>
    1       5.32

`filter()` selects rows based on logical conditions. The == operator
tests for equality.

### 3. Calculating Bivariate Descriptive Statistics with `group_by()`

Compares the distribution of one variable (like goals) across different
levels of another variable (like age groups) all in one step.

The group_by() approach has three essential steps:

- Open grouping with `group_by()`
- Specify calculations with `summarize()`
- Close grouping with `ungroup()`

``` r
# Calculate goal statistics grouped by age groups
epl25 %>%
  group_by(age_group) %>%            # Split data by age group variable
  summarize(
    minimum = min(Gls, na.rm = TRUE),    # Calculate minimum goals per age group
    mean = mean(Gls, na.rm = TRUE),      # Calculate mean goals per age group
    maximum = max(Gls, na.rm = TRUE)     # Calculate maximum goals per age group
  ) %>%
  ungroup()                          # Close the grouping
```

    # A tibble: 4 × 4
      age_group   minimum  mean maximum
      <fct>         <dbl> <dbl>   <dbl>
    1 Under 23          0  1.27      15
    2 23-30             0  2.27      23
    3 30+               0  1.71      29
    4 Unknown age       0  0          0

This produces a table with one row for each age group, showing goal
statistics for each group.

`group_by()` splits data into groups, `summarize()` calculates
statistics for each group, and `ungroup()` closes the grouping
operation.

### 4. Creating Professional Bivariate Tables with table summary

Produces a professional, reproducible table showing distributions of
multiple variables split by a grouping variable.

``` r
# Create a comprehensive bivariate table with custom formatting
epl25 %>%
  select(Squad, Gls, Ast, age_group) %>%     # Select only variables we want in the table
  tbl_summary(by = age_group,                # Split table by age group (creates columns)
              label = list(                  # Custom labels
                Gls ~ "Goals",
                Ast ~ "Assists"),
              statistic = list(              # Customize statistics shown
                all_continuous() ~ "{median} ({p25}-{p75})",
                all_categorical() ~ "{n} ({p}%)"),           # Count and percentage for categories
              missing = "always") %>%        # Always show missing values for transparency
  as_kable()                               # Convert to kable format for markdown display
```

| **Characteristic** | **Under 23** N = 266 | **23-30** N = 340 | **30+** N = 89 | **Unknown age** N = 7 | **other** N = 0 |
|:---|:--:|:--:|:--:|:--:|:--:|
| Squad |  |  |  |  |  |
| Arsenal | 15 (5.6%) | 20 (5.9%) | 3 (3.4%) | 0 (0%) | 0 (NA%) |
| Aston Villa | 13 (4.9%) | 16 (4.7%) | 5 (5.6%) | 0 (0%) | 0 (NA%) |
| Bournemouth | 18 (6.8%) | 18 (5.3%) | 2 (2.2%) | 0 (0%) | 0 (NA%) |
| Brentford | 18 (6.8%) | 14 (4.1%) | 3 (3.4%) | 0 (0%) | 0 (NA%) |
| Brighton & Hove Albion | 24 (9.0%) | 13 (3.8%) | 5 (5.6%) | 0 (0%) | 0 (NA%) |
| Chelsea | 18 (6.8%) | 16 (4.7%) | 1 (1.1%) | 0 (0%) | 0 (NA%) |
| Crystal Palace | 11 (4.1%) | 21 (6.2%) | 5 (5.6%) | 0 (0%) | 0 (NA%) |
| Everton | 14 (5.3%) | 14 (4.1%) | 7 (7.9%) | 0 (0%) | 0 (NA%) |
| Fulham | 4 (1.5%) | 20 (5.9%) | 4 (4.5%) | 0 (0%) | 0 (NA%) |
| Ipswich Town | 7 (2.6%) | 24 (7.1%) | 3 (3.4%) | 1 (14%) | 0 (NA%) |
| Leicester City | 12 (4.5%) | 17 (5.0%) | 6 (6.7%) | 3 (43%) | 0 (NA%) |
| Liverpool | 10 (3.8%) | 15 (4.4%) | 4 (4.5%) | 0 (0%) | 0 (NA%) |
| Manchester City | 17 (6.4%) | 14 (4.1%) | 5 (5.6%) | 0 (0%) | 0 (NA%) |
| Manchester United | 20 (7.5%) | 16 (4.7%) | 5 (5.6%) | 0 (0%) | 0 (NA%) |
| Newcastle United | 7 (2.6%) | 16 (4.7%) | 7 (7.9%) | 0 (0%) | 0 (NA%) |
| Nottingham Forest | 6 (2.3%) | 16 (4.7%) | 5 (5.6%) | 0 (0%) | 0 (NA%) |
| Southampton | 13 (4.9%) | 21 (6.2%) | 2 (2.2%) | 2 (29%) | 0 (NA%) |
| Tottenham Hotspur | 20 (7.5%) | 17 (5.0%) | 3 (3.4%) | 0 (0%) | 0 (NA%) |
| West Ham United | 7 (2.6%) | 14 (4.1%) | 8 (9.0%) | 0 (0%) | 0 (NA%) |
| Wolverhampton Wanderers | 12 (4.5%) | 18 (5.3%) | 6 (6.7%) | 1 (14%) | 0 (NA%) |
| Unknown | 0 | 0 | 0 | 0 | 0 |
| Goals | 0.00 (0.00-2.00) | 1.00 (0.00-3.00) | 0.00 (0.00-1.00) | 0.00 (0.00-0.00) | NA (NA-NA) |
| Unknown | 97 | 19 | 9 | 3 | 0 |
| Assists | 0.00 (0.00-1.00) | 1.00 (0.00-2.00) | 0.00 (0.00-2.00) | 0.00 (0.00-0.00) | NA (NA-NA) |
| Unknown | 97 | 19 | 9 | 3 | 0 |

This creates a customized table with: - Columns for each age group
(Under 23, 23-30, 30+) in logical order - Custom labels for variables
(Goals instead of Gls, Assists instead of Ast) - Median with
interquartile range for continuous variables (appropriate for skewed
data like goals/assists) - Count and percentages for categorical
variables (like Squad) - Missing values always displayed for
transparency

Key arguments: - by = specifies the grouping variable that creates table
columns - label = provides custom variable names for cleaner
presentation - statistic = controls which statistics are displayed and
their format - missing = “always” ensures missing values are always
shown

\*\*(need to check) The “`{median} ({p25}-{p75})`” format shows median
with interquartile range, which is appropriate for skewed data like
goals and assists. For normally distributed data, you could use
“`{median} (±{sd})`” instead.

### 5. Adding Statistical Tests to Compare Groups

Adds a p-value column to test whether differences between groups are
statistically significant.

``` r
# Create summary table with statistical test
epl25 %>%
  select(Gls, Ast, age_group) %>%            # Select only variables we want in the table
  tbl_summary(by = age_group,                # Split table by age group (creates columns)
              label = list(                  # Custom labels
                Gls ~ "Goals",
                Ast ~ "Assists"),
              statistic = list(              # Customize statistics shown
                all_continuous() ~ "{median} ({p25}-{p75})",
                all_categorical() ~ "{n} ({p}%)"),           # Count and percentage for categories
              missing = "always") %>%        # Always show missing values for transparency
  add_p() %>%                              # Add p-value column for group comparisons
  as_kable()                               # Convert to kable format for markdown display
```

| **Characteristic** | **Under 23** N = 266 | **23-30** N = 340 | **30+** N = 89 | **Unknown age** N = 7 | **other** N = 0 | **p-value** |
|:---|:--:|:--:|:--:|:--:|:--:|:--:|
| Goals | 0.00 (0.00-2.00) | 1.00 (0.00-3.00) | 0.00 (0.00-1.00) | 0.00 (0.00-0.00) | NA (NA-NA) | \<0.001 |
| Unknown | 97 | 19 | 9 | 3 | 0 |  |
| Assists | 0.00 (0.00-1.00) | 1.00 (0.00-2.00) | 0.00 (0.00-2.00) | 0.00 (0.00-0.00) | NA (NA-NA) | \<0.001 |
| Unknown | 97 | 19 | 9 | 3 | 0 |  |

The add_p() function automatically selects appropriate statistical tests
based on your variable types and adds a p-value column to the right side
of your table.

- `add_p()` adds statistical tests to compare groups in your summary
  table.
- `as_kable()` preserves gtsummary formatting while making it
  markdown-compatible

Use `as_kable()` instead of `kable()` for gtsummary tables `as_kable()`
is specifically designed for gtsummary objects and handles the
conversion better

### Summary

- Chain operations efficiently using the pipe operator %\>% to combine
  multiple data manipulation steps
- Convert data types with `as.numeric()` to ensure proper comparisons
  for categorical grouping
- Create categorical variables with `mutate()` and `case_when()` to
  group continuous variables like age, including a catch-all condition
  with `TRUE ~`
- Create factors directly by wrapping `case_when()` with `factor()` and
  specifying levels = to control ordering in one step
- Order data with `arrange()` to organize rows by column values (like
  ranking players by goals)
- Filter data with `filter()` to create subsets based on criteria (like
  selecting only forwards)
- Calculate bivariate statistics using `group_by()` and `summarize()` to
  compare distributions across groups (like goal stats by age group)
- Create professional tables with `tbl_summary()` including custom
  labels, statistics appropriate for data distribution, and missing
  value handling
- Add statistical tests with `add_p()` to compare differences between
  groups (like testing if age groups differ significantly in
  performance)

Reference:

- HMP 669
