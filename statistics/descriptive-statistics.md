# Descriptive Statistics
Michael Royce
2025-06-07

- [1. Numeric Variables: Central Tendency and
  Spread](#1-numeric-variables-central-tendency-and-spread)
- [2. R Functions for Numeric Data](#2-r-functions-for-numeric-data)
- [3. Using `summarize()` to Calculate Multiple
  Statistics](#3-using-summarize-to-calculate-multiple-statistics)
- [4. Character or Factor Variables: Counts and
  Frequencies](#4-character-or-factor-variables-counts-and-frequencies)
- [5. R Functions for Categorical
  Data](#5-r-functions-for-categorical-data)
- [6. Calculating Counts and Percentages with `count()` and
  `mutate()`](#6-calculating-counts-and-percentages-with-count-and-mutate)
- [7. Creating Professional Summary Tables with
  `tbl_summary()`](#7-creating-professional-summary-tables-with-tbl_summary)

Setup — Load libraries

``` r
# Install packages if needed (uncomment if not already installed)
# install.packages(c("tidyverse", "here", "gtsummary"))

# load packages
library(tidyverse)   # includes dplyr
library(here)        # optional, prefer to use `here()`, builds a file path relative to project’s root directory
library(gtsummary)   # tbl_summary()
```

Descriptive statistics allow us to understand the characteristics of
people in a dataset. We can answer questions like: “How old are the
people in my dataset?”, “How many people have a specific disorder?”, or
“What are the levels of a risk factor?”.

Quantitatively describing our study sample helps us understand how
generalizable and unique our study is. We’ll focus on calculating the
central tendency and spread of variables, and creating professional
tables in R.

### 1. Numeric Variables: Central Tendency and Spread

Numeric variables contain only numbers. It’s important to understand
their distribution (symmetric, bimodal, skewed) before hypothesis
testing. We’ll cover central tendency, spread, range, and quantiles.

- **Central Tendency:** Measures the middle of the data (mean, median).
- **Spread:** Measures how wide the data are (standard deviation,
  interquartile range).
- **Range:** The minimum and maximum values.
- **Quantiles:** Values below which a certain percentage of the data
  falls (e.g., 25th, 50th, 75th percentiles).

### 2. R Functions for Numeric Data

provides intuitive functions for calculating these statistics.

- `mean()`: Calculates the mean (average).
- `median()`: Calculates the median.
- `sd()`: Calculates the standard deviation.
- `quantile()`: Calculates quantiles (percentiles). You can specify
  which percentiles to calculate using the `probs` argument (e.g.,
  `quantile(data, probs = c(0.25, 0.75))`).
- `IQR()`: Calculates the interquartile range (75th percentile - 25th
  percentile).
- `summary()`: Calculates quantiles (including min, max, median) and the
  mean.
- `first()`: Returns the first value in an ordered vector.
- `last()`: Returns the last value in an ordered vector.

### 3. Using `summarize()` to Calculate Multiple Statistics

The `summarize()` function from the `dplyr` package is very useful for
creating a data frame containing multiple descriptive statistics.

``` r
# Create a sample tibble data frame
data <- tibble(age = c(25, 30, 35, 40, 45, 50, 55, 60))

# Calculate minimum, mean, and maximum age using summarize
summary_data <- data %>%
  summarize(
    minimum = min(age), # Calculate the minimum age and store it in a column called 'minimum'
    mean = mean(age),   # Calculate the mean age and store it in a column called 'mean'
    maximum = max(age)    # Calculate the maximum age and store it in a column called 'maximum'
  )

summary_data
```

    # A tibble: 1 × 3
      minimum  mean maximum
        <dbl> <dbl>   <dbl>
    1      25  42.5      60

- `data %>% summarize(...)`: Pipes the data data frame into the
  `summarize()` function. This allows us to chain operations together.
- `minimum = min(age)`: Calculates the minimum value of the age variable
  and assigns it to a new column named minimum.
- `mean = mean(age)`: Calculates the mean of the age variable and
  assigns it to a new column named mean.
- `maximum = max(age)`: Calculates the maximum value of the age variable
  and assigns it to a new column named maximum.

This creates a new data frame called `summary_data` with a single row
and three columns: `minimum`, `mean`, and `maximum`. Each column
contains the corresponding descriptive statistic for the age variable.

### 4. Character or Factor Variables: Counts and Frequencies

Character or factor variables contain data in categories. We summarize
these by providing the number (N) or count per group, and the frequency
(percent) of the total in that group.

### 5. R Functions for Categorical Data

Several R functions help calculate counts and frequencies.

- `table()`: Provides the number of observations per group.
- `n()`: Can be used within dplyr verbs to count observations within
  groups.
- `count()`: Provides the number of observations per group.
- `prop.table()`: Provides the proportion of observations per group.

### 6. Calculating Counts and Percentages with `count()` and `mutate()`

calculate the number and percent of observations in each category using
`count()` and `mutate()` from the dplyr package.

``` r
# Create a sample data frame
data2 <- tibble(education = c("High School", "College", "College", "High School", "Graduate"))

# Calculate the number and percent of observations in each education group
summary_data2 <- data2 %>%
  count(education) %>% # Count the number of observations in each education group
  mutate(
    percent = n / sum(n) * 100 # Calculate the percentage of observations in each group
  )

summary_data2
```

    # A tibble: 3 × 3
      education       n percent
      <chr>       <int>   <dbl>
    1 College         2      40
    2 Graduate        1      20
    3 High School     2      40

- `data2 %>% count(education)`: Counts the number of rows for each
  unique value in the education column. The result is a data frame with
  columns `education` and `n` (the count).

- `mutate(percent = n / sum(n) * 100)`: Creates a new column called
  `percent`. For each education group, it calculates the percentage of
  observations by dividing the count (`n`) by the total number of
  observations (`sum(n)`) and multiplying by 100.

### 7. Creating Professional Summary Tables with `tbl_summary()`

``` r
# Sample Data
data3 <- tibble(
  age = c(25, 30, 35, 40, 45, 50, 55, 60),
  education = c("High School", "College", "College", "High School", "Graduate", "College", "High School", "Graduate"),
  gender = c("Male", "Female", "Male", "Female", "Male", "Female", "Male", "Female")
)

# Select the columns
selected_data <- data3 %>%
  select(age, education, gender)

# Create Summary table using gtsummary
table_summary <- selected_data %>%
  tbl_summary(
    type = list(age ~ "continuous"),  # Force age to be treated as continuous
    label = list(age ~ "Age (years)", 
                 education ~ "Educational Attainment", 
                 gender ~ "Gender"),
    statistic = list(age ~ "{mean} (±{sd})"), 
    digits = list(age ~ 0) 
  )
```

#### Optional: Rendering `tbl_summary()` in .md

``` r
# Convert tbl_summary to plain tibble (no formatting)
# Needed because tbl_summary() shows raw HTML/CSS if rendered in .md
# Requires: tibble (in tidyverse)
table_summary_tibble <- table_summary %>% as_tibble()      

# Convert to kable for HTML/PDF/Markdown output
# Recommended when rendering .qmd/.Rmd to md or PDF (avoids raw HTML dump)
# Requires: knitr
# library(knitr)                      
table_summary_kable <- table_summary %>% as_kable()

# Convert to flextable for Word output
# tbl_summary() won't render properly in Word without this
# Requires: flextable
# library(flextable)                  
# table_summary %>% as_flextable()
```

``` r
table_summary_tibble
```

    # A tibble: 8 × 2
      `**Characteristic**`   `**N = 8**`
      <chr>                  <chr>      
    1 Age (years)            43 (±12)   
    2 Educational Attainment <NA>       
    3 College                3 (38%)    
    4 Graduate               2 (25%)    
    5 High School            3 (38%)    
    6 Gender                 <NA>       
    7 Female                 4 (50%)    
    8 Male                   4 (50%)    

``` r
table_summary_kable
```

| **Characteristic**     | **N = 8** |
|:-----------------------|:---------:|
| Age (years)            | 43 (±12)  |
| Educational Attainment |           |
| College                |  3 (38%)  |
| Graduate               |  2 (25%)  |
| High School            |  3 (38%)  |
| Gender                 |           |
| Female                 |  4 (50%)  |
| Male                   |  4 (50%)  |

`Select Columns`: Selects specific columns to include in the summary
table

- `label`: Allows providing labels for variables.
- `statistic`: Allows custom statistic to be displayed. In this example,
  the mean and standard deviation are calculated and displayed for the
  age variable.
- `digits`: Specify the number of digits to be displayed. Here we set it
  to 0 for age.

Reference:

- HMP 669
