# reading-data
Michael Royce Tan
2025-06-05

-   [1. Understanding the Working
    Directory](#understanding-the-working-directory)
-   [2. Recommended Project Structure](#recommended-project-structure)
-   [3. Accessing Example Data (using dslabs
    package)](#accessing-example-data-using-dslabs-package)
-   [4. Copying the Example File to the Working
    Directory](#copying-the-example-file-to-the-working-directory)
-   [5. Reading the Data (Example - after copying the
    file)](#reading-the-data-example---after-copying-the-file)

### 1. Understanding the Working Directory

The working directory is where R looks for files by default.

``` r
# Get the current working directory
getwd()

# Change the working directory (example - replace with your desired path)
setwd("~/path/to/your/directory") 
```

Explanation:

-   `getwd()`: This function displays the current working directory of
    your R session.
-   `setwd()`: This function allows you to set a new working directory.
-   RStudio provides a GUI method via the Session menu.

### 2. Recommended Project Structure

It is recommended to create a directory for each analysis and organize
your raw data within it.

``` r
Project Directory/
├── data/           # Place for raw data files
└── script.R        # Your R script
└── rmarkdown.Rmd   # Your R markdown
```

This structure keeps your project organized, especially when working
with multiple data files. The data folder stores the original, unaltered
data.

### 3. Accessing Example Data (using dslabs package)

`dslabs` package provides an example dataset.

``` r
# Install dslabs package (if not already installed)
if(!require(dslabs)) install.packages("dslabs")

# Load the dslabs package
library(dslabs)

# Find the directory containing the external data files
path_to_extdata <- system.file("extdata", package = "dslabs")
path_to_extdata
```

    [1] "/Library/Frameworks/R.framework/Versions/4.5-arm64/Resources/library/dslabs/extdata"

``` r
# List the files in the extdata
list.files(path_to_extdata)
```

     [1] "2010_bigfive_regents.xls"                               
     [2] "calificaciones.csv"                                     
     [3] "carbon_emissions.csv"                                   
     [4] "fertility-two-countries-example.csv"                    
     [5] "HRlist2.txt"                                            
     [6] "life-expectancy-and-fertility-two-countries-example.csv"
     [7] "murders.csv"                                            
     [8] "olive.csv"                                              
     [9] "RD-Mortality-Report_2015-18-180531.pdf"                 
    [10] "ssa-death-probability.csv"                              

Explanation:

-   `install.packages("dslabs")`: Installs the dslabs package if you
    haven’t already installed it.
-   `library(dslabs)`: Loads the dslabs package, making its functions
    and datasets available.
-   `system.file("extdata", package = "dslabs")`: This function returns
    the path to the “extdata” directory within the dslabs package. The
    path will vary depending on your system.
-   `list.files()`: This function lists all the files within a specified
    directory.

### 4. Copying the Example File to the Working Directory

Move the desired example file to your working directory for easier
access.

``` r
# Define the file name (replace with your desired file)
murders_file <- "murders.csv"

# Construct the full path to the original file
murders_path <- file.path(path_to_extdata, murders_file)
murders_path
```

    [1] "/Library/Frameworks/R.framework/Versions/4.5-arm64/Resources/library/dslabs/extdata/murders.csv"

``` r
# Create a "data" folder in the current working directory if it doesn't exist
if (!dir.exists("data")) {
  dir.create("data")
}

# Copy the file to the working directory/data
file.copy(murders_path, "data")
```

    [1] FALSE

``` r
# Check if the file is now in the data directory
file.exists(file.path("data", murders_file))
```

    [1] TRUE

``` r
# Copy the file to the working directory instead
# file.copy(murders_path, getwd())

# Check if the file is now in the working directory
# file.exists(murders_file)
```

Explanation:

-   `filename <- "murders.csv"`: Specifies the name of the file you want
    to copy. Replace “murders.csv” with the actual filename you want to
    use from the previous step.
-   `file.path(path_to_extdata, filename)`: This function constructs the
    full path to the original file.
-   `file.path()`is preferred over paste() for creating file paths
    because it automatically handles the correct path separators for
    your operating system (Windows, macOS, Linux).
-   `file.copy(original_path, getwd())`: Copies the file from its
    original location to the current working directory.
-   `file.exists(filename)`: Checks if the file now exists in the
    working directory. Returns TRUE if the file exists, FALSE otherwise.

### 5. Reading the Data (Example - after copying the file)

Now that the file is in the working directory, you can read it using
functions like read.csv(), read.table(), etc., depending on the file
type.

``` r
# Example using read.csv() to read the "murders.csv" file
# Assuming the file is comma-separated
murders_data <- read.csv("data/murders.csv")

# Inspect the data
head(murders_data)  # Display the first few rows
```

           state abb region population total
    1    Alabama  AL  South    4779736   135
    2     Alaska  AK   West     710231    19
    3    Arizona  AZ   West    6392017   232
    4   Arkansas  AR  South    2915918    93
    5 California  CA   West   37253956  1257
    6   Colorado  CO   West    5029196    65

``` r
str(murders_data)   # Display the structure of the data frame
```

    'data.frame':   51 obs. of  5 variables:
     $ state     : chr  "Alabama" "Alaska" "Arizona" "Arkansas" ...
     $ abb       : chr  "AL" "AK" "AZ" "AR" ...
     $ region    : chr  "South" "West" "West" "South" ...
     $ population: int  4779736 710231 6392017 2915918 37253956 5029196 3574097 897934 601723 19687653 ...
     $ total     : int  135 19 232 93 1257 65 97 38 99 669 ...

Explanation:

-   `read.csv("murders.csv")`: Reads the CSV file named “murders.csv”
    into a data frame called murders.
-   `head(murders)`: Displays the first few rows of the murders data
    frame. This lets you quickly see the column names and data.
-   `str(murders)`: Displays the structure of the murders data frame,
    including the data type of each column and the number of rows and
    columns.
