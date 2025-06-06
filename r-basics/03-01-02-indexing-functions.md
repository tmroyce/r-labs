# R Basics — Indexing Functions
Michael Royce
2025-06-06

- [1. Three Indexing Functions](#1-three-indexing-functions)
- [2. `which()` Function](#2-which-function)
- [3. `match()` Function](#3-match-function)
- [4. `%in%` Operator](#4-in-operator)
- [Summary](#summary)

Setup

``` r
library(tidyverse)
library(dslabs)

data(murders)

# Calculate murder rate per 100,000 people
murder_rate <- (murders$total / murders$population) * 100000
```

### 1. Three Indexing Functions

three powerful functions that use logical operators for indexing:

- `which()` - gives us the entries of a logical vector that are TRUE
- `match()` - looks for entries in a vector and returns the index needed
  to access them
- `%in%` - tells us whether elements of one vector are present in
  another vector

### 2. `which()` Function

`which()` function returns the indices where a logical vector is TRUE.

``` r
# Create a simple logical vector
x <- c(FALSE, TRUE, FALSE, TRUE, TRUE, FALSE)

# Use which() to find indices where x is TRUE
which(x)
```

    [1] 2 4 5

This returns 2, 4, 5 - the positions where the logical vector is TRUE.
This is useful when you have a long logical vector and only want to
store the actual indices rather than the entire vector of logical
values.

#### Using `which()` to Find a Specific State’s Murder Rate

Let’s find Massachusetts’ murder rate using `which()`:

``` r
# Find the index where state equals "Massachusetts"
index <- which(murders$state == "Massachusetts")
index  # This will show 22 (or whatever Massachusetts' position is)
```

    [1] 22

``` r
# Use the index to get the murder rate
murder_rate[index]  # Returns Massachusetts' murder rate (e.g., 1.8)
```

    [1] 1.802179

While we could use the logical vector directly, using which() creates a
much smaller object when dealing with large datasets.

### 3. `match()` Function

`match()` function looks for entries in a vector and returns the index
needed to access them. It tells us which indices of the second vector
match each entry in the first vector.

#### Using `match()` to Find Multiple States

Suppose we want the murder rates for New York, Florida, and Texas:

``` r
# Use match to find indices for multiple states
index <- match(c("New York", "Florida", "Texas"), murders$state)
index  # Returns something like: 33, 10, 44
```

    [1] 33 10 44

``` r
# Confirm we got the right states
murders$state[index]  # Should return: "New York", "Florida", "Texas"
```

    [1] "New York" "Florida"  "Texas"   

``` r
# Get the murder rates for these states
murder_rate[index]  # Returns the murder rates: e.g., 2.66, 3.4, 3.2
```

    [1] 2.667960 3.398069 3.201360

### 4. `%in%` Operator

The %in% operator tells us whether each element of the first vector is
present in the second vector, returning TRUE or FALSE for each element:

``` r
# Create two simple vectors
x <- c("a", "b", "c", "d", "e")
y <- c("a", "d", "f")

# Check which elements of y are in x
y %in% x
```

    [1]  TRUE  TRUE FALSE

This returns TRUE, TRUE, FALSE because:

- “a” is in x → TRUE
- “d” is in x → TRUE
- “f” is not in x → FALSE

#### Using `%in%` to Check if Places are Valid States

Let’s check whether Boston, Dakota, and Washington are states:

``` r
# Check if these are valid state names
c("Boston", "Dakota", "Washington") %in% murders$state
```

    [1] FALSE FALSE  TRUE

This returns FALSE, FALSE, TRUE because:

- Boston is not a state → FALSE
- Dakota is not a state → FALSE
- Washington is a state → TRUE

### Summary

`which()`:

- “Where is this TRUE?” “Which rows meet this condition?”
- You give it a logical vector (TRUEs and FALSEs), and it tells you the
  positions where it’s TRUE.

`match()`:

- “Where does this value appear?”
- It tells you the position of a value (or values) in another vector.
- If the value isn’t found, it returns NA.

`%in%`:

- “Is this value part of that group?”
- Checks whether elements in one vector exist in another.
- It returns a logical vector (TRUE or FALSE for each element).

Reference:

- [Indexing
  Functions](https://rafalab.dfci.harvard.edu/dsbook-part-1/R/R-basics.html#which)
