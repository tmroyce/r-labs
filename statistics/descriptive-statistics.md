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

table_summary
```

<div id="oapmvfdmqk" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>#oapmvfdmqk table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
&#10;#oapmvfdmqk thead, #oapmvfdmqk tbody, #oapmvfdmqk tfoot, #oapmvfdmqk tr, #oapmvfdmqk td, #oapmvfdmqk th {
  border-style: none;
}
&#10;#oapmvfdmqk p {
  margin: 0;
  padding: 0;
}
&#10;#oapmvfdmqk .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}
&#10;#oapmvfdmqk .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#oapmvfdmqk .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}
&#10;#oapmvfdmqk .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}
&#10;#oapmvfdmqk .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#oapmvfdmqk .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#oapmvfdmqk .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#oapmvfdmqk .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}
&#10;#oapmvfdmqk .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}
&#10;#oapmvfdmqk .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#oapmvfdmqk .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#oapmvfdmqk .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}
&#10;#oapmvfdmqk .gt_spanner_row {
  border-bottom-style: hidden;
}
&#10;#oapmvfdmqk .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}
&#10;#oapmvfdmqk .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}
&#10;#oapmvfdmqk .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#oapmvfdmqk .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#oapmvfdmqk .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}
&#10;#oapmvfdmqk .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#oapmvfdmqk .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}
&#10;#oapmvfdmqk .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#oapmvfdmqk .gt_row_group_first th {
  border-top-width: 2px;
}
&#10;#oapmvfdmqk .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#oapmvfdmqk .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#oapmvfdmqk .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#oapmvfdmqk .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#oapmvfdmqk .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#oapmvfdmqk .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#oapmvfdmqk .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}
&#10;#oapmvfdmqk .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#oapmvfdmqk .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#oapmvfdmqk .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#oapmvfdmqk .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#oapmvfdmqk .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#oapmvfdmqk .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#oapmvfdmqk .gt_left {
  text-align: left;
}
&#10;#oapmvfdmqk .gt_center {
  text-align: center;
}
&#10;#oapmvfdmqk .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#oapmvfdmqk .gt_font_normal {
  font-weight: normal;
}
&#10;#oapmvfdmqk .gt_font_bold {
  font-weight: bold;
}
&#10;#oapmvfdmqk .gt_font_italic {
  font-style: italic;
}
&#10;#oapmvfdmqk .gt_super {
  font-size: 65%;
}
&#10;#oapmvfdmqk .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}
&#10;#oapmvfdmqk .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#oapmvfdmqk .gt_indent_1 {
  text-indent: 5px;
}
&#10;#oapmvfdmqk .gt_indent_2 {
  text-indent: 10px;
}
&#10;#oapmvfdmqk .gt_indent_3 {
  text-indent: 15px;
}
&#10;#oapmvfdmqk .gt_indent_4 {
  text-indent: 20px;
}
&#10;#oapmvfdmqk .gt_indent_5 {
  text-indent: 25px;
}
&#10;#oapmvfdmqk .katex-display {
  display: inline-flex !important;
  margin-bottom: 0.75em !important;
}
&#10;#oapmvfdmqk div.Reactable > div.rt-table > div.rt-thead > div.rt-tr.rt-tr-group-header > div.rt-th-group:after {
  height: 0px !important;
}
</style>

<table class="gt_table" data-quarto-postprocess="true"
data-quarto-disable-processing="false" data-quarto-bootstrap="false">
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="gt_col_headings">
<th id="label" class="gt_col_heading gt_columns_bottom_border gt_left"
data-quarto-table-cell-role="th"
scope="col"><strong>Characteristic</strong></th>
<th id="stat_0"
class="gt_col_heading gt_columns_bottom_border gt_center"
data-quarto-table-cell-role="th" scope="col"><strong>N = 8</strong><span
class="gt_footnote_marks"
style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
</tr>
</thead>
<tbody class="gt_table_body">
<tr>
<td class="gt_row gt_left" headers="label">Age (years)</td>
<td class="gt_row gt_center" headers="stat_0">43 (±12)</td>
</tr>
<tr>
<td class="gt_row gt_left" headers="label">Educational Attainment</td>
<td class="gt_row gt_center" headers="stat_0"><br />
</td>
</tr>
<tr>
<td class="gt_row gt_left" headers="label">    College</td>
<td class="gt_row gt_center" headers="stat_0">3 (38%)</td>
</tr>
<tr>
<td class="gt_row gt_left" headers="label">    Graduate</td>
<td class="gt_row gt_center" headers="stat_0">2 (25%)</td>
</tr>
<tr>
<td class="gt_row gt_left" headers="label">    High School</td>
<td class="gt_row gt_center" headers="stat_0">3 (38%)</td>
</tr>
<tr>
<td class="gt_row gt_left" headers="label">Gender</td>
<td class="gt_row gt_center" headers="stat_0"><br />
</td>
</tr>
<tr>
<td class="gt_row gt_left" headers="label">    Female</td>
<td class="gt_row gt_center" headers="stat_0">4 (50%)</td>
</tr>
<tr>
<td class="gt_row gt_left" headers="label">    Male</td>
<td class="gt_row gt_center" headers="stat_0">4 (50%)</td>
</tr>
</tbody><tfoot class="gt_footnotes">
<tr>
<td colspan="2" class="gt_footnote"><span class="gt_footnote_marks"
style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span>
Mean (±SD); n (%)</td>
</tr>
</tfoot>
&#10;</table>

</div>

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
