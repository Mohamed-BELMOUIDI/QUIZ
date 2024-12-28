# College Major Income Analysis

This repository contains an analysis of the relationship between college major categories and median income based on a dataset of 173 observations and 19 variables. The analysis was conducted using R, with key steps involving data loading, cleaning, statistical analysis, and visualization.

## Table of Contents

1. [Synopsis](#synopsis)
2. [Required Libraries](#required-libraries)
3. [Data Loading and Initial Exploration](#data-loading-and-initial-exploration)
4. [Data Cleaning and Preparation](#data-cleaning-and-preparation)
5. [Statistical Analysis](#statistical-analysis)
   - 4.1 [Linear Regression Model](#linear-regression-model)
   - 4.2 [Visualization of Results](#visualization-of-results)
6. [Results and Conclusion](#results-and-conclusion)
7. [Saving and Recording Data](#saving-and-recording-data)

## 1. Synopsis

The analysis investigates whether there is a significant association between the major category and median income. Based on statistical results, there is no sufficient evidence to affirm that the major category has a significant impact on income.

## 2. Required Libraries

The following libraries are required to run the analysis:

- `collegeIncome` – For access to the college dataset.
- `tidyverse` – For data manipulation and visualization.
- `magrittr` – For piping operations.
- `ggplot2` – For creating visualizations.
- `kableExtra` – For enhanced table presentation.
- `DT` – For interactive tables.
- `PerformanceAnalytics` – For advanced performance metrics.

Install these libraries using:

```r
install.packages(c("collegeIncome", "tidyverse", "magrittr", "ggplot2", "kableExtra", "DT", "PerformanceAnalytics"))
```

## 3. Data Loading and Initial Exploration

The dataset used in this analysis is loaded from the `collegeIncome` package. It contains data on college majors, income, employment statistics, and more. 

The script explores the following:

- Dataset dimensions (`dim()`).
- Data structure (`str()`).

```r
data("college")
df_college <- college
dim(df_college)
str(df_college)
```

## 4. Data Cleaning and Preparation

The data is cleaned by:

- Removing unnecessary columns such as `rank`, `major_code`, and `sample_size`.
- Converting `major_category` to a factor.
- Removing rows with missing values (`NA`).
- Filtering out non-representative categories such as "Interdisciplinary."

```r
df_tidy <- df_college %>%
    dplyr::select(-c(rank, major_code, major, sample_size, perc_men, perc_unemployed, perc_employed_fulltime, perc_college_jobs)) %>%
    dplyr::mutate(major_category = factor(major_category)) %>%
    tidyr::drop_na() %>%
    dplyr::filter(major_category != "Interdisciplinary")
```

## 5. Statistical Analysis

### 4.1 Linear Regression Model

A linear regression model is fitted to analyze the relationship between median income and major category, including factors such as employment statistics and job types.

```r
model <- lm(median ~ major_category + perc_employed_fulltime_yearround + perc_non_college_jobs + perc_low_wage_jobs - 1, data = df_tidy)
summary(model)
```

### 4.2 Visualization of Results

A boxplot is created to visualize the distribution of median income across different major categories.

```r
ggplot(df_tidy, aes(x = reorder(major_category, median), y = median)) +
    geom_boxplot(aes(fill = major_category)) +
    coord_flip() +
    theme_minimal() +
    labs(
        title = "Distribution of Median Income by Major Category",
        x = "Major Category",
        y = "Median Income ($)",
        fill = "Major Category"
    ) +
    theme(legend.position = "none")
```

## 6. Results and Conclusion

The analysis concludes that there is no significant association between college major category and income. This is supported by:

- High p-values in the regression model for major categories.
- Overlapping income distributions across categories.
- Limited effect size of the major category variable.

## 7. Saving and Recording Data

The final dataset and analysis are saved using the `matahari` package for future reference.

```r
matahari::dance_save("./college_major_analysis.rds")
```
