# R Basics — Data Types: Vectors and Data Frames
Michael Royce
2025-06-06

- [1. Understanding Data Types with the class()
  Function](#1-understanding-data-types-with-the-class-function)
- [2. Data Frames](#2-data-frames)
- [3. Exploring Data Frame Structure](#3-exploring-data-frame-structure)
- [4. Accessing Columns with the Dollar Sign
  Accessor](#4-accessing-columns-with-the-dollar-sign-accessor)
- [5. Understanding Vectors and
  Length](#5-understanding-vectors-and-length)
- [6. Working with Character Vectors](#6-working-with-character-vectors)
- [7. Creating and Understanding Logical
  Vectors](#7-creating-and-understanding-logical-vectors)
- [8. Understanding Factors for Categorical
  Data](#8-understanding-factors-for-categorical-data)
- [Summary](#summary)

Required Libraries

``` r
library(tidyverse)
library(dslabs)
```

### 1. Understanding Data Types with the class() Function

``` r
# Define a variable and check its type
a <- 2
class(a)  # Shows "numeric"
```

    [1] "numeric"

``` r
# Check the type of a function
class(ls)  # Shows "function"
```

    [1] "function"

The `class()` function tells us what type of object we’re working with.

### 2. Data Frames

Data frames are the most common way to store datasets in R.

- Rows represent observations
- Columns represent different variables
- Different data types can be combined in one object

``` r
# Load the murders dataset from dslabs
data(murders)

# Check that murders is indeed a data frame
class(murders)  
```

    [1] "data.frame"

### 3. Exploring Data Frame Structure

``` r
# See the structure of the data frame
str(murders)
```

    'data.frame':   51 obs. of  5 variables:
     $ state     : chr  "Alabama" "Alaska" "Arizona" "Arkansas" ...
     $ abb       : chr  "AL" "AK" "AZ" "AR" ...
     $ region    : Factor w/ 4 levels "Northeast","South",..: 2 4 4 2 4 4 1 2 2 2 ...
     $ population: num  4779736 710231 6392017 2915918 37253956 ...
     $ total     : num  135 19 232 93 1257 ...

The str() function (structure) shows us that:

- It’s a data frame with 51 observations (rows) and 5 variables
  (columns)
- Column names are: state, abb, region, population, and total
- Each column’s data type is also displayed

``` r
# Show the first 6 lines of the dataset
head(murders)
```

           state abb region population total
    1    Alabama  AL  South    4779736   135
    2     Alaska  AK   West     710231    19
    3    Arizona  AZ   West    6392017   232
    4   Arkansas  AR  South    2915918    93
    5 California  CA   West   37253956  1257
    6   Colorado  CO   West    5029196    65

head()\` function displays the first six rows, giving us a quick preview
of what the data looks like.

### 4. Accessing Columns with the Dollar Sign Accessor

``` r
# Access the population column
murders$population
```

     [1]  4779736   710231  6392017  2915918 37253956  5029196  3574097   897934
     [9]   601723 19687653  9920000  1360301  1567582 12830632  6483802  3046355
    [17]  2853118  4339367  4533372  1328361  5773552  6547629  9883640  5303925
    [25]  2967297  5988927   989415  1826341  2700551  1316470  8791894  2059179
    [33] 19378102  9535483   672591 11536504  3751351  3831074 12702379  1052567
    [41]  4625364   814180  6346105 25145561  2763885   625741  8001024  6724540
    [49]  1852994  5686986   563626

``` r
# Get all column names
names(murders)
```

    [1] "state"      "abb"        "region"     "population" "total"     

- The \$ symbol (called the accessor) lets us extract specific columns
  from the data frame
- `names()` shows us all available column names so we know what we can
  access
- The order of entries in murders\$population matches the row order in
  the original data frame

### 5. Understanding Vectors and Length

``` r
# Store the population column in a new variable
pop <- murders$population

# Check how many entries are in this vector
length(pop)  # Returns 51
```

    [1] 51

``` r
# Confirm it's a numeric vector
class(pop)  # Returns "numeric"
```

    [1] "numeric"

- When we extract a column, we get a vector (a collection of entries)
- `length()` tells us how many entries are in the vector
- This particular vector contains 51 numbers (one for each state)

### 6. Working with Character Vectors

``` r
# Example of variable vs character string
a <- 1        # Variable a gets value 1
a             # Returns the value 1
```

    [1] 1

``` r
"a"           # Returns the character string "a"
```

    [1] "a"

``` r
# Check the state names column
class(murders$state)  # Returns "character"
```

    [1] "character"

- We use quotes to distinguish between variable names and character
  strings
- The state names column contains character data (text), not numbers
- All entries in a character vector must be characters

### 7. Creating and Understanding Logical Vectors

``` r
# Create a logical vector using a comparison
z <- 3 == 2   # Ask if 3 equals 2
z             # Returns FALSE
```

    [1] FALSE

``` r
class(z)      # Returns "logical"
```

    [1] "logical"

- Logical vectors contain only TRUE or FALSE values
- The double equals == is for comparison (asking “is equal to?”)
- This is different from single equals = which would be for assignment

### 8. Understanding Factors for Categorical Data

``` r
# Check the region column type
class(murders$region)  # Returns "factor"
```

    [1] "factor"

``` r
# See the factor levels
levels(murders$region)  # Shows: "Northeast" "South" "North Central" "West"
```

    [1] "Northeast"     "South"         "North Central" "West"         

- Factors store categorical data (data with specific categories)
- Even though regions look like character strings, they’re stored as
  factors
- `levels()` shows us the distinct categories available
- Factors are more memory efficient because R stores them internally as
  integers

### Summary

- Numeric vectors: Store numbers
- Character vectors: Store text (use quotes to distinguish from
  variables)
- Logical vectors: Store TRUE/FALSE values
- Factors: Store categorical data efficiently
- Data frames: Store datasets as tables with rows (observations) and
  columns (variables)

Key functions covered:

- `class()`: Determine object type
- `str()`: Show structure of complex objects
- `head()`: Preview first few rows
- `names()`: Get column names
- `length()`: Count vector entries
- `levels()`: Show factor categories
- `$`: Access data frame columns

Reference: - [Data
Types](https://rafalab.dfci.harvard.edu/dsbook-part-1/R/R-basics.html#data-types)
