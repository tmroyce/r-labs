# Creating New Variables in R for Health Data
Michael Royce
2025-06-07

- [Introduction](#introduction)
- [1. Converting numeric objects to categories with
  `ifelse()`](#1-converting-numeric-objects-to-categories-with-ifelse)
- [2. Using `case_when()` for multiple
  conditions](#2-using-case_when-for-multiple-conditions)
- [3. Categorical factors and reference
  Levels](#3-categorical-factors-and-reference-levels)
- [4. Splitting numeric data into categories using
  `cut_interval()`](#4-splitting-numeric-data-into-categories-using-cut_interval)
- [5. Creating Groups Based on Quantiles using
  `cut_number()`](#5-creating-groups-based-on-quantiles-using-cut_number)
- [6. Creating new numeric variables with arithmetic
  operators](#6-creating-new-numeric-variables-with-arithmetic-operators)
- [7. Adding new variables to a DataFrame Using
  `mutate()`](#7-adding-new-variables-to-a-dataframe-using-mutate)
- [8. Using mutate() with the Pipe
  Operator](#8-using-mutate-with-the-pipe-operator)
- [9. Creating Multiple Variables with
  `mutate()`](#9-creating-multiple-variables-with-mutate)
- [Summary](#summary)

Setup — Load Libraries

``` r
# Install packages if needed (uncomment if not already installed)
# install.packages(c("tidyverse", "here"))

# load packages
library(tidyverse)   # includes dplyr
library(here)        # optional, prefer to use `here()`, builds a file path relative to project’s root directory
```

### Introduction

Create new variables in R using existing variables within a dataset.
Allowing you to derive new insights and prepare your data for further
analysis. We will focus on converting numeric objects to categories,
combining numeric objects, and adding these new variables to your
DataFrame.

### 1. Converting numeric objects to categories with `ifelse()`

Often, we want to create categorical variables from numeric data based
on certain conditions. The `ifelse()` function is a simple way to do
this when you have a binary outcome (true or false).

``` r
# using ifelse() to categorize scores
score <- c(65, 75, 80, 60, 90)

# create a new variable 'result'
result <- ifelse(score >= 70, "pass", "fail")

result
```

    [1] "fail" "pass" "pass" "fail" "pass"

- `score <- c(65, 75, 80, 60, 90)`: Creates a vector of sample scores.
- `ifelse(score >= 70, "pass", "fail")`: Checks if each score is greater
  than or equal to 70. If it is, it assigns “pass”; otherwise, it
  assigns “fail”.
- `print(result)`: Prints the resulting vector, showing “pass” or “fail”
  for each score.

Always logic-check your work. Before running the code, think about how
many values you expect in each category. After running, check the actual
counts to ensure they match your expectations.

### 2. Using `case_when()` for multiple conditions

When you have more than two categories, `case_when()` is a powerful
function to use. It allows you to specify multiple conditions and
corresponding outputs. You’ll need the `dplyr` package for this.

``` r
# Example using case_when() to assign grades
score <- c(92, 85, 78, 62, 95, 88, 40, 45)
grade <- case_when(
  score >= 90 ~ "A",
  score >= 80 & score < 90 ~ "B",
  score >= 70 & score < 80 ~ "C",
  score >= 50 ~ "D",
  TRUE ~ "F"  # Default for everything else
)

grade
```

    [1] "A" "B" "C" "D" "A" "B" "F" "F"

- case_when(…): This function evaluates multiple conditions.
- `score >= 90 ~ "A"`: If the score is greater than or equal to 90,
  assign “A”.
- `score >= 80 & score < 90 ~ "B"`: If the score is greater than or
  equal to 80 AND less than 90, assign “B”.
- The other lines follow a similar pattern for grades C and D.
- The `~` symbol is used to specify the output for each condition.

### 3. Categorical factors and reference Levels

When you create categorical variables, R treats them as factors. One
level will be the reference level.

``` r
# Convert grade to a factor
grade <- as.factor(grade)

# Print the levels
levels(grade)
```

    [1] "A" "B" "C" "D" "F"

``` r
# Re-level the factor 
grade <- relevel(grade, ref = "B")

 # Check new reference level
levels(grade) 
```

    [1] "B" "A" "C" "D" "F"

- `grade <- as.factor(grade)`: Converts the grade variable to a factor.
- `levels(grade)`: Shows the levels of the factor, and the first one
  will be the default reference level.
- `grade <- relevel(grade, ref = "B")`: Changes the reference level to
  “B”.

### 4. Splitting numeric data into categories using `cut_interval()`

The `cut_interval()` function automatically divides a numeric variable
into a specified number of groups with equal ranges.

``` r
# using cut_interval() to create age groups
age <- c(20, 25, 30, 35, 40, 45, 50, 55, 60)

# Split into 5 groups
age_group <- cut_interval(age, n = 5) 
age_group
```

    [1] [20,28] [20,28] (28,36] (28,36] (36,44] (44,52] (44,52] (52,60] (52,60]
    Levels: [20,28] (28,36] (36,44] (44,52] (52,60]

- `age <- c(20, 25, 30, 35, 40, 45, 50, 55, 60)`: Creates a vector of
  ages.
- `cut_interval(age, n = 5)`: Divides the age variable into 5 groups of
  equal range.

### 5. Creating Groups Based on Quantiles using `cut_number()`

You can also split data based on quantiles to have an approximately
equal number of observations in each group. This is useful when the data
is not evenly distributed. `cut_number()` from dplyr is useful for this.

``` r
# using cut_number() to create age groups with roughly equal numbers
age_group_quantile <- cut_number(age, n = 5)
age_group_quantile
```

    [1] [20,28] [20,28] (28,36] (28,36] (36,44] (44,52] (44,52] (52,60] (52,60]
    Levels: [20,28] (28,36] (36,44] (44,52] (52,60]

- `cut_number(age, n = 5)`: Divides the age variable into 5 groups with
  approximately the same number of observations in each group.

### 6. Creating new numeric variables with arithmetic operators

Basic arithmetic operations can be used to combine existing numeric
variables to create new numeric variables.

``` r
# Calculating BMI
height_meters_squared <- c(1.75, 1.80, 1.65) ^ 2
weight_kilograms <- c(70, 80, 60)

bmi <- weight_kilograms / height_meters_squared
bmi
```

    [1] 22.85714 24.69136 22.03857

``` r
# Calculating Total Score
test_score1 <- c(80, 90, 75)
test_score2 <- c(70, 85, 80)
total_score <- test_score1 + test_score2
total_score
```

    [1] 150 175 155

### 7. Adding new variables to a DataFrame Using `mutate()`

The `mutate()` function from the dplyr package is used to add new
variables to an existing DataFrame.

``` r
# Create a sample dataframe
df <- data.frame(
  ID = 1:5,
  height = c(1.75, 1.80, 1.65, 1.70, 1.90),
  weight = c(70, 80, 60, 75, 90),
  age = c(25, 30, 35, 40, 45),
  score = c(65, 75, 80, 60, 90)
)

# Add BMI variable
df2 <- mutate(df, bmi = weight / (height^2))
df2
```

      ID height weight age score      bmi
    1  1   1.75     70  25    65 22.85714
    2  2   1.80     80  30    75 24.69136
    3  3   1.65     60  35    80 22.03857
    4  4   1.70     75  40    60 25.95156
    5  5   1.90     90  45    90 24.93075

`mutate(df, bmi = weight / (height^2))`: Adds a new variable called bmi
to the DataFrame df. The values for bmi are calculated using the weight
and height variables.

### 8. Using mutate() with the Pipe Operator

The pipe operator (`%>%` or `|>`) from dplyr makes the code more
readable when using `mutate()`.

``` r
# Add variables using the pipe operator
df3 <- df %>%
  mutate(bmi2 = weight / (height^2))

df3
```

      ID height weight age score     bmi2
    1  1   1.75     70  25    65 22.85714
    2  2   1.80     80  30    75 24.69136
    3  3   1.65     60  35    80 22.03857
    4  4   1.70     75  40    60 25.95156
    5  5   1.90     90  45    90 24.93075

### 9. Creating Multiple Variables with `mutate()`

`mutate()` can create multiple variables at once.

``` r
# Create multiple variables in one mutate call
df4 <- df %>%
  mutate(
    bmi = weight / (height^2),
    age_group = cut_interval(age, n = 5),
    test_result = case_when(
      score >= 70 ~ "pass",
      score < 70 ~ "fail" 
    )
  )

print(df4)
```

      ID height weight age score      bmi age_group test_result
    1  1   1.75     70  25    65 22.85714   [25,29]        fail
    2  2   1.80     80  30    75 24.69136   (29,33]        pass
    3  3   1.65     60  35    80 22.03857   (33,37]        pass
    4  4   1.70     75  40    60 25.95156   (37,41]        fail
    5  5   1.90     90  45    90 24.93075   (41,45]        pass

### Summary

create new variables in R using existing variables. We covered:

- `ifelse()` for binary categorization
- `case_when()` for multiple conditions
- `cut_interval()` for splitting numeric data into categories
- Arithmetic operators for combining numeric variables
- `mutate()` for adding new variables to a DataFrame
- The pipe operator (%\>%) for more readable code

Reference:

- HMP 669
