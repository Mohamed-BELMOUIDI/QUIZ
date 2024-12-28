## Synopsis

Based on an analysis involving 173 observations and 19 variables, there
is no sufficient evidence to affirm that the major category has a
significant association with income.

## 1. Required Libraries

    library(collegeIncome)
    library(tidyverse)
    library(magrittr)
    library(ggplot2)
    library(kableExtra)
    library(DT)
    library(PerformanceAnalytics)

## 2. Data Loading and Initial Exploration

    # Loading college data
    data("college")
    df_college <- college

    # Display dimensions
    dim(df_college)

    ## [1] 173  19

    # Structure of data
    str(df_college)

    ## 'data.frame':    173 obs. of  19 variables:
    ##  $ rank                            : int  1 2 3 4 5 6 7 8 9 10 ...
    ##  $ major_code                      : int  2419 2416 2415 2417 2405 2418 6202 5001 2414 2408 ...
    ##  $ major                           : chr  "Petroleum Engineering" "Mining And Mineral Engineering" "Metallurgical Engineering" "Naval Architecture And Marine Engineering" ...
    ##  $ major_category                  : chr  "Engineering" "Engineering" "Engineering" "Engineering" ...
    ##  $ total                           : int  2339 756 856 1258 32260 2573 3777 1792 91227 81527 ...
    ##  $ sample_size                     : int  36 7 3 16 289 17 51 10 1029 631 ...
    ##  $ perc_women                      : num  0.911 0.515 0.594 0.652 0.418 ...
    ##  $ p25th                           : num  25000 26000 26700 26000 31500 23000 32500 37900 29200 23000 ...
    ##  $ median                          : num  40000 37000 45000 35000 62000 44700 45000 57000 36000 32200 ...
    ##  $ p75th                           : num  50000 40000 60000 45000 109000 50000 58000 67000 46000 47100 ...
    ##  $ perc_men                        : num  0.0891 0.4846 0.4058 0.3479 0.5821 ...
    ##  $ perc_employed                   : num  0.912 0.798 0.787 0.847 0.852 ...
    ##  $ perc_employed_fulltime          : num  0.921 0.711 0.883 0.937 0.809 ...
    ##  $ perc_employed_parttime          : num  0.177 0.362 0.339 0.167 0.402 ...
    ##  $ perc_employed_fulltime_yearround: num  0.77 0.709 0.774 0.653 0.685 ...
    ##  $ perc_unemployed                 : num  0.0885 0.2019 0.2128 0.1534 0.1484 ...
    ##  $ perc_college_jobs               : num  0.67 0.387 0.729 0.246 0.587 ...
    ##  $ perc_non_college_jobs           : num  0.182 0.516 0.176 0.411 0.386 ...
    ##  $ perc_low_wage_jobs              : num  0.0554 0.2156 0.0301 0.0432 0.118 ...

## 3. Data Cleaning and Preparation

    # Create clean dataset
    df_tidy <- df_college %>%
        # Remove unnecessary columns
        dplyr::select(-c(rank, major_code, major, sample_size, 
                         perc_men, perc_unemployed, 
                         perc_employed_fulltime, perc_college_jobs)) %>%
        # Convert to factor
        dplyr::mutate(major_category = factor(major_category)) %>%
        # Remove NA values
        tidyr::drop_na() %>%
        # Remove non-representative categories
        dplyr::filter(major_category != "Interdisciplinary")

    # Verify cleaning
    sum(is.na(df_tidy))

    ## [1] 0

## 4. Statistical Analysis

### 4.1 Linear Regression Model

    # Fit linear model
    model <- lm(median ~ major_category + 
                perc_employed_fulltime_yearround + 
                perc_non_college_jobs + 
                perc_low_wage_jobs - 1, 
                data = df_tidy)

    # Model summary
    summary(model)

    ## 
    ## Call:
    ## lm(formula = median ~ major_category + perc_employed_fulltime_yearround + 
    ##     perc_non_college_jobs + perc_low_wage_jobs - 1, data = df_tidy)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -20030  -7102  -2192   5104  58427 
    ## 
    ## Coefficients:
    ##                                                   Estimate Std. Error t value
    ## major_categoryAgriculture & Natural Resources      13231.0    10328.5   1.281
    ## major_categoryArts                                  5867.2    11041.3   0.531
    ## major_categoryBiology & Life Science               13137.8    10212.8   1.286
    ## major_categoryBusiness                             19560.1     9904.4   1.975
    ## major_categoryCommunications & Journalism          10454.2    11535.4   0.906
    ## major_categoryComputers & Mathematics               2211.9    10943.0   0.202
    ## major_categoryEducation                             7240.5    10223.7   0.708
    ## major_categoryEngineering                           9661.5    10176.6   0.949
    ## major_categoryHealth                                8751.5    10539.4   0.830
    ## major_categoryHumanities & Liberal Arts             3793.9    10354.5   0.366
    ## major_categoryIndustrial Arts & Consumer Services   8252.9    11143.6   0.741
    ## major_categoryLaw & Public Policy                   7279.2    10909.6   0.667
    ## major_categoryPhysical Sciences                     9430.3    10511.4   0.897
    ## major_categoryPsychology & Social Work              7813.2    10779.4   0.725
    ## major_categorySocial Science                        8027.9    10731.9   0.748
    ## perc_employed_fulltime_yearround                   37822.5    13212.2   2.863
    ## perc_non_college_jobs                               6962.3     7295.5   0.954
    ## perc_low_wage_jobs                                  -488.3    17594.8  -0.028
    ##                                                   Pr(>|t|)   
    ## major_categoryAgriculture & Natural Resources      0.20214   
    ## major_categoryArts                                 0.59592   
    ## major_categoryBiology & Life Science               0.20025   
    ## major_categoryBusiness                             0.05009 . 
    ## major_categoryCommunications & Journalism          0.36623   
    ## major_categoryComputers & Mathematics              0.84009   
    ## major_categoryEducation                            0.47990   
    ## major_categoryEngineering                          0.34394   
    ## major_categoryHealth                               0.40764   
    ## major_categoryHumanities & Liberal Arts            0.71457   
    ## major_categoryIndustrial Arts & Consumer Services  0.46008   
    ## major_categoryLaw & Public Policy                  0.50564   
    ## major_categoryPhysical Sciences                    0.37106   
    ## major_categoryPsychology & Social Work             0.46967   
    ## major_categorySocial Science                       0.45559   
    ## perc_employed_fulltime_yearround                   0.00479 **
    ## perc_non_college_jobs                              0.34143   
    ## perc_low_wage_jobs                                 0.97790   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 11160 on 152 degrees of freedom
    ## Multiple R-squared:  0.9366, Adjusted R-squared:  0.9291 
    ## F-statistic: 124.8 on 18 and 152 DF,  p-value: < 2.2e-16

### 4.2 Visualization of Results

    # Create boxplot of income by major category
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

![](ffff_files/figure-markdown_strict/visualization-1.png)

## 5. Results and Conclusion

The analysis shows no significant association between college major
category and income, based on:

1.  Linear regression results showing high p-values for most major
    categories
2.  No clear pattern in the distribution of income across categories
3.  Limited explanatory power of the major category variable

The lack of significance is demonstrated by: - High p-values in the
regression model - Overlapping income distributions across categories -
Limited effect size of major category on income variation

Therefore, we cannot conclude that there is a significant association
between college major category and income.
