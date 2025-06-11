# Preparing for Data Science Project
Michael Royce
2025-06-10

- [1. Create the Main Projects
  Directory](#1-create-the-main-projects-directory)
- [2. Create the Project-Specific Directory
  Structure](#2-create-the-project-specific-directory-structure)
- [3. Set Up RStudio Project](#3-set-up-rstudio-project)
- [4. Write the Data Download Script](#4-write-the-data-download-script)
- [5. Write the Data Wrangling
  Script](#5-write-the-data-wrangling-script)
- [6. Write the Analysis Script](#6-write-the-analysis-script)
- [7. Create Figures Directory and Save
  Plots](#7-create-figures-directory-and-save-plots)
- [8. Create Project Documentation](#8-create-project-documentation)
- [Project Structure](#project-structure)

Setting up a properly organized data science project directory
structure, following best practices for file organization and code
portability.

### 1. Create the Main Projects Directory

Sets up a central location in your home directory to store all your data
science projects. Open a terminal and navigate to your home directory,
then create the main projects folder:

``` bash

cd ~                   # Move to home directory
mkdir projects         # Create projects directory
```

**Why this matters:** Having a dedicated projects directory keeps your
work organized and makes it easy to find all your data science projects
in one place.

### 2. Create the Project-Specific Directory Structure

Creates a dedicated directory for your specific project (in this case,
“murders”) with organized sub-directories for different types of files.

``` bash

cd projects               # Move into projects directory
mkdir murders             # Create murders project directory
cd murders                # Move into murders directory
mkdir data rdas           # Create data and rdas sub-directories
ls                        # Confirm directories were created
pwd                       # Check current working directory
```

Why this structure:

- data/ holds raw data files
- rdas/ stores processed R data objects
- This separation keeps raw and processed data organized

### 3. Set Up RStudio Project

Connects RStudio to your project directory, making it the default
working directory. Verify your setup in the R console:

``` r
getwd()    # verify working directory ~projects/murders
```

**Why use RStudio projects:** This automatically sets your working
directory to the project root, enabling the use of relative paths
throughout your code.

### 4. Write the Data Download Script

Creates a script to download raw data and save it to the appropriate
directory. Create a new R script file called download-data.R

``` r
# download-data.R
# Downloads raw murders data from online source

# Download data to the data directory using relative path
url <- "https://raw.githubusercontent.com/rafalab/dslabs/master/inst/extdata/murders.csv"
dest_file <- "data/murders.csv"
download.file(url, dest_file)
```

- Uses relative path data/murders.csv instead of full path
- Downloads data directly to the organized data directory
- Relative paths make code portable across different systems

### 5. Write the Data Wrangling Script

Creates a script that reads raw data, processes it, and saves the
processed version for analysis.Create a new R script file called
wrangle-data.R:

``` r
# wrangle-data.R
# Processes raw murders data and saves for analysis

# Load required library
library(dplyr)

# Read the raw data using relative path
murders_raw <- read.csv("data/murders.csv")

# Process the data (example processing)
murders <- murders_raw %>%
  mutate(region = factor(region),
         rate = total / population * 100000)  # Calculate murder rate per 100k

# Save processed data object to rda directory
save(murders, file = "rdas/murders.rda")
```

- Saves R objects to .rda files (R Data format)
- Allows quick loading of processed data without re-running
  time-consuming processing
- `.rda` is preferred over .RData for consistency

#### Alternative approach with `saveRDS()` and `readRDS()`:

While save() lets you save several objects that then get loaded with the
same names used when saving, the function saveRDS() lets you save one
object, without the name. To bring it back you use the readRDS()
function.

``` r
# Save single object without preserving the name
saveRDS(murders, file = "rda/murders.rda")
```

Then read it in another session, using whatever object name you want:

``` r
# Read and assign to any variable name you choose
dat <- readRDS("rda/murders.rda")
# or
my_data <- readRDS("rda/murders.rda")
```

- `save()` preserves object names; `saveRDS()` doesn’t
- `save()` can save multiple objects; `saveRDS()` saves one object
- `load()` restores objects with original names; `readRDS()` lets you
  choose the name

### 6. Write the Analysis Script

Creates the main analysis script that loads processed data and generates
results. Create a new R script file called analysis.R:

``` r
# analysis.R
# Main analysis script using processed data

# Load required libraries
library(ggplot2)
library(dplyr)

# Load processed data from rda directory
load("rdas/murders.rda")

# Perform analysis and create visualization
p <- murders %>%
  mutate(abb = reorder(abb, rate)) %>% 
  ggplot(aes(abb, rate)) +
  geom_bar(width = 0.5, stat = "identity", color = "black") +
  coord_flip()

print(p)
```

**Why separate scripts:** This workflow separates data download,
processing, and analysis, making the project more modular and easier to
maintain.

### 7. Create Figures Directory and Save Plots

Sets up organized storage for generated plots and saves visualizations.
First, create the figures directory in terminal:

``` bash

mkdir figs    # Create directory for saving figures
```

Then modify your analysis.R script to save the plot:

``` r
# analysis.R (updated version)
# Main analysis script with plot saving

# Load required libraries
library(ggplot2)
library(dplyr)

# Load processed data
load("rdas/murders.rda")

# Create visualization
p <- murders %>%
  mutate(abb = reorder(abb, rate)) %>% 
  ggplot(aes(abb, rate)) +
  geom_bar(width = 0.5, stat = "identity", color = "black") +
  coord_flip()

print(p)

# Save plot to figs directory using relative path
ggsave("figs/barplot.png", plot = p, width = 10, height = 8)
```

`ggsave()`: This ggplot2 function saves plots in various formats (PNG,
PDF, etc.) with customizable dimensions.

### 8. Create Project Documentation

Documents your project structure and file purposes for future reference
and collaboration. Create a README.txt file in your project root
directory:

``` plaintext
# Murder Data Analysis Project

## File Structure

### Scripts
- download-data.R: Downloads raw murder data from online source
- wrangle-data.R: Processes raw data and calculates murder rates
- analysis.R: Creates visualizations and performs main analysis

### Directories
- data/: Contains raw data files (murders.csv)
- rdas/: Contains processed R data objects (murders.rda)
- figs/: Contains generated plots and figures (barplot.png)

### Workflow
1. Run download-data.R to get raw data
2. Run wrangle-data.R to process data
3. Run analysis.R to generate results and plots

### Notes
- All file paths use relative paths for portability
- Processed data is saved to avoid re-processing on each analysis run
- Project structure follows data science best practices
```

### Project Structure

``` plaintext
~/projects/murders/
├── README.txt
├── download-data.R
├── wrangle-data.R
├── analysis.R
├── data/
│   └── murders.csv
├── rdas/
│   └── murders.rda
└── figs/
    └── barplot.png
```

This structure creates a professional, reproducible data science project
that others can easily understand and run on their own systems.

- Organized structure separates raw data, processed data, and outputs
- Relative paths make code portable across different systems
- Modular scripts separate data acquisition, processing, and analysis
- Documentation explains project structure and workflow
- Self-contained project includes everything needed to reproduce results

Reference: [Organizing a data science
project](https://rafalab.dfci.harvard.edu/dsbook-part-1/productivity/reproducible-projects.html#sec-organizing)
