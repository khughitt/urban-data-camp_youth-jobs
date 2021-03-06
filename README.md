\#UrbanDataCamp: Using Data to Help Low-Income Youth Find Jobs

Overview
========

Questions
---------

1.  Can we show that job opportunities for less-privileged youths leads to good outcomes later in life? (College enrollment, etc.)

Notes
-----

### General

-   "Alumni"
-   "Terminated" - didn't finish

### Factors associated with a positive outcome:

-   college enrollment
-   college persistence
-   alumni

Methods
=======

Load data
---------

``` r
library(readr)
library(dplyr)

dat = read_csv('input/UA_UI_DataCamp_with_Codebook_4_29.csv')
```

    ## Warning: 2402 problems parsing
    ## 'input/UA_UI_DataCamp_with_Codebook_4_29.csv'. See problems(...) for more
    ## details.

``` r
# preview data
kable(dat[1:5, 1:6])
```

| DataCamp ID | Academic Year | Location                            | Intern Status | Intern Work Site                                               |  Work Site ZIP Code|
|:------------|:--------------|:------------------------------------|:--------------|:---------------------------------------------------------------|-------------------:|
| UADC1096    | 2014 - 2015   | DC - Year Round High School Program | Active        | Clark Construction Group, LLC Accounts Payable 2014-2015       |               20814|
| UADC1281    | 2014 - 2015   | NCR                                 | Active        | Patent and Trade Office NCR 2014-2015                          |               22314|
| UADC1129    | 2014 - 2015   | Baltimore - YAIP                    | Alumni        | ECSM BAL YAIP 2014-2015                                        |               21213|
| UADC1254    | 2014 - 2015   | DC - Year Round High School Program | Active        | Office of Personnel Management - Retirement Services 2014-2015 |               20240|
| UADC1007    | 2014 - 2015   | NCR                                 | Active        | Alexandria City Attorney's Office NCR 2014-2015                |               22314|

``` r
kable(dat[1:5, 7:12])
```

| Organization                             | Industry    | Total Hours Worked | DOB        |  Mailing ZIP/Postal Code|  Median Household Income|
|:-----------------------------------------|:------------|:------------------:|:-----------|------------------------:|------------------------:|
| Clark Construction Group, LLC            | Real Estate |         251        | 1996-02-21 |                    20019|                    34832|
| Patent and Trade Office                  | Government  |         162        | 1997-02-15 |                    22304|                    76061|
| Episcopal Community Services of Maryland | Non-profit  |         55         | 1993-11-15 |                    21215|                    34471|
| Office of Personnel Management           | Government  |         129        | 1997-10-18 |                    20020|                    34685|
| Alexandria City Attorney's Office        | Government  |         103        | 1997-06-11 |                    22312|                    76845|

``` r
kable(dat[1:5, 13:19])
```

|  Labor Force Participation|  Unemployment Rate|    SSI|  Cash Assistance|   SNAP|  All Families Below Poverty|  Educational Attainment - BA or Higher|
|--------------------------:|------------------:|------:|----------------:|------:|---------------------------:|--------------------------------------:|
|                      0.595|              0.136|  0.115|            0.103|  0.324|                       0.264|                                  0.134|
|                      0.788|              0.045|  0.022|            0.017|  0.048|                       0.046|                                  0.586|
|                      0.555|              0.102|  0.123|            0.064|  0.268|                       0.223|                                  0.171|
|                      0.561|              0.115|  0.113|            0.124|  0.348|                       0.317|                                  0.160|
|                      0.757|              0.037|  0.013|            0.016|  0.056|                       0.091|                                  0.435|

``` r
kable(dat[1:5, 20:26])
```

|  Female-Headed Households| Race            | Gender | High School                                                                 |  Title I School?|  FRL  |  Graduation Rate of HS|
|-------------------------:|:----------------|:-------|:----------------------------------------------------------------------------|----------------:|:-----:|----------------------:|
|                     0.188| Black           | F      | Washington Mathematics Science Technology Public Charter High School (WMST) |                1| 1.000 |                  0.910|
|                     0.049| Hispanic/Latino | F      | TC Williams                                                                 |                0| 0.630 |                  0.843|
|                     0.118| Black           | F      |                                                                             |               NA|   NA  |                     NA|
|                     0.203| Black           | M      | Ballou Senior High School                                                   |                1| 0.998 |                  0.500|
|                     0.044| Black           | F      | TC Williams                                                                 |                0| 0.630 |                  0.843|

``` r
kable(dat[1:5, 26:33])
```

|  Graduation Rate of HS|  College Enrollment Rate of HS|  HS ZIP Code|  NSC Data Available?| 1st Year College Enrollment | 1st Year College Type (2/4) | 1st Year College State | Student Quote |
|----------------------:|------------------------------:|------------:|--------------------:|:----------------------------|:----------------------------|:-----------------------|:--------------|
|                  0.910|                             NA|        20002|                    0| NA                          | NA                          | NA                     |               |
|                  0.843|                             NA|        22302|                    0| NA                          | NA                          | NA                     |               |
|                     NA|                             NA|           NA|                    0| NA                          | NA                          | NA                     |               |
|                  0.500|                             NA|        20032|                    0| NA                          | NA                          | NA                     |               |
|                  0.843|                             NA|        22302|                    0| NA                          | NA                          | NA                     |               |

### Variables

Note: Formatting for metadata is a little bit messed up...

``` r
metadata = read_csv('input/UA_UI_DataCamp_with_Codebook_4_29_metadata.csv')
kable(metadata)
```

| Column Heading                           | Description                                                                                                                                                          |
|:-----------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataCamp ID                              | Unique intern identifier                                                                                                                                             |
| Academic Year                            | Program year of participation                                                                                                                                        |
| Location                                 | Urban Alliance program/region                                                                                                                                        |
| Intern Status                            | Active: Intern is currently in program                                                                                                                               |
| Alumni: Intern successfully completed pr | ogram.                                                                                                                                                               |
| Summer Program Alumni: Intern successful | ly completed the program and will likely return in the fall (Rising HS seniors)                                                                                      |
| Terminated: Dropped out of the program d | ue to performance reasons or self-removal                                                                                                                            |
| Intern Work Site                         | Tag for where interns worked, comprised of organization name, location, and academic year                                                                            |
| Work Site ZIP Code                       | ZIP Code of Intern Work Site                                                                                                                                         |
| Organization                             | Work Site Organization Name                                                                                                                                          |
| Industry                                 | Specific industry organization falls into                                                                                                                            |
| Total Hours Worked                       | Sum of monthly hours interns worked during the program; compiled from ADP payroll reports                                                                            |
| DOB                                      | Intern date of birth                                                                                                                                                 |
| Mailing ZIP/Postal Code                  | Intern's home ZIP Code                                                                                                                                               |
| Median Household Income                  | ZIP Code-based; Median household income of residents within intern's home ZIP code; ACS 2013 5-year estimates                                                        |
| Labor Force Participation                | ZIP Code-based; Labor force participation rate of residents within intern's home ZIP code; ACS 2013 5-year estimates                                                 |
| Unemployment Rate                        | ZIP Code-based; Unemployment rate of residents within intern's home ZIP code; ACS 2013 5-year estimates                                                              |
| SSI                                      | ZIP Code-based; Percent of residents receiving SSI within intern's home ZIP code; ACS 2013 5-year estimates                                                          |
| Cash Assistance                          | ZIP Code-based; Percent of residents receiving cash assistance within intern's home ZIP code; ACS 2013 5-year estimates                                              |
| SNAP                                     | ZIP Code-based; Percent of residents receiving SNAP benefits within intern's home ZIP code; ACS 2013 5-year estimates                                                |
| All Families Below Poverty               | ZIP Code-based; Percent of residents living below poverty within intern's home ZIP code; ACS 2013 5-year estimates                                                   |
| Educational Attainment - BA or Higher    | ZIP Code-based; Percent of residents with a Bachelor's degree or higher within intern's home ZIP code; ACS 2013 5-year estimates                                     |
| Female-Headed Households                 | ZIP Code-based; Percent of residents living in female-headed households with children under 18 years of age within intern's home ZIP code; ACS 2013 5-year estimates |
| Race                                     | Intern race (self-identified, voluntary)                                                                                                                             |
| Gender                                   | Intern gender (self-identified, voluntary)                                                                                                                           |
| High School                              | High School intern attended                                                                                                                                          |
| Title I School?                          | School-based                                                                                                                                                         |
| 1: High School receives Title I funding  |                                                                                                                                                                      |
| 0: High School does not receive Title I  | funding                                                                                                                                                              |
| blank: Title I data not found            |                                                                                                                                                                      |
| FRL                                      | School-based; Percent of students in high school qualified for free or reduced lunch                                                                                 |
| Graduation Rate of HS                    | School-based; Percent of high school students graduating within four years                                                                                           |
| College Enrollment Rate of HS            | School-based; Percent of high school students who enroll in college following graudation                                                                             |
| HS ZIP Code                              | ZIP Code of High School                                                                                                                                              |
| NSC Data Available?                      | 1: Alum has been included in a National Student Clearinghouse data request to determine college enrollment                                                           |
| 0: Alum has not been included in a Natio | nal Student Clearinghouse data request to determine college enrollment                                                                                               |
| 1st Year College Enrollment              | Enrolled: Alum was enrolled in college at time of National Student Clearinghouse data pull                                                                           |
| Unenrolled: Alum was not enrolled at tim | e of National Student Clearinghouse data pull                                                                                                                        |
| 1st Year College Type (2/4)              | 2: Two-year college                                                                                                                                                  |
| 4: Four-year college                     |                                                                                                                                                                      |
| No record found: Alum did not have a Nat | ional Student Clearinghouse record                                                                                                                                   |
| 1st Year College State                   | State in which college is located                                                                                                                                    |
| Student Quote                            | Open-ended response from intern post-program survey                                                                                                                  |

``` r
# convert dates to factors so they aren't treated as numeric
dat[['Work Site ZIP Code']]      = factor(dat[['Work Site ZIP Code']])
dat[['Mailing ZIP/Postal Code']] = factor(dat[['Mailing ZIP/Postal Code']])
dat[['HS ZIP Code']]             = factor(dat[['HS ZIP Code']])
```

``` r
table(dat[['Intern Status']])
```

    ## 
    ##                                 Active 
    ##                                    355 
    ##                                 Alumni 
    ##                                    744 
    ##                  Summer Program Alumni 
    ##                                     42 
    ##                     Terminated Student 
    ##                                     29 
    ## Terminated Student - Performance Based 
    ##                                    109 
    ##      Terminated Student - Self Removed 
    ##                                    117

### Rename variables

For easier typing...

``` r
colnames(dat) = c('datacamp_id', 'academic_year', 'location', 'intern_status',
                  'intern_work_site', 'work_site_zip', 'organization', 'industry',
                  'total_hours_worked', 'dob', 'mailing_zip',
                  'median_household_income', 'labor_force_participation',
                  'unemployment_rate', 'ssi', 'cash_assistance', 'snap',
                  'all_families_below_poverty', 'educational_attainment_ba',
                  'female_headed_households', 'race', 'gender', 'high_school',
                  'title_i_school', 'frl', 'graduation_rate_of_hs',
                  'college_enrollment_of_hs', 'hs_zip_code', 'nsc_data_available',
                  'first_year_college_enrollment', 'first_year_college_type',
                  'first_year_college_state', 'student_quote')

# save with alternative name
write_csv(dat, 'input/UA_UI_DataCamp_with_Codebook_4_29_clean.csv')
```

### Exploratory data analysis

#### Variable relationships

``` r
library(gplots)
library(bpca)
library(RColorBrewer)

# create short version of data with a subset of the fields and not including
# the current (2014-2015) interns who haven't yet finished; remove rows with
# duplicated ids
duplicate_ids = names(table(dat$datacamp_id)[table(dat$datacamp_id) > 1])
dat_short = dat %>%
    filter(academic_year != '2014 - 2015' & 
           industry != "" & 
           !datacamp_id %in% duplicate_ids) %>%
    select(datacamp_id, intern_status, industry, total_hours_worked,
           median_household_income, labor_force_participation,
           unemployment_rate, ssi, cash_assistance, snap,
           all_families_below_poverty, educational_attainment_ba,
           female_headed_households, graduation_rate_of_hs) %>%
    mutate(intern_status=ifelse(intern_status %in% 
                                c("Alumni", "Summer Program Alumni"),
                                "Alumni", "Terminated"))
    
dat_short_complete = dat_short[complete.cases(dat_short),]

ids = dat_short_complete$datacamp_id

dat_short_complete = dat_short_complete %>% select(-datacamp_id)

# Heatmap
heatmap.2(cor(as.matrix(dat_short_complete %>% select(-intern_status, -industry))),
          margins=c(26, 26), trace='none')
```

![](README_files/figure-markdown_github/eda-1.png)

``` r
# group by location
dat$location_colors = brewer.pal(length(unique(dat$location)), "Set1")[
    as.numeric(as.factor((as.character(dat$location))))]
location_colors_complete = dat$location_colors[dat$datacamp_id %in% ids]

# biplot
plot(bpca(dat_short_complete %>% select(-intern_status, -industry),
          var.color=location_colors_complete, scale=TRUE))
```

![](README_files/figure-markdown_github/eda-2.png)

#### Students

``` r
# rescale data
rescale = function(x) {
    (x - min(x)) / max(x)
}

dat_scaled = dat_short_complete

dat_scaled$median_household_income  = rescale(dat_scaled$median_household_income)
dat_scaled$total_hours_worked       = rescale(dat_scaled$total_hours_worked)
dat_scaled$ssi                      = rescale(dat_scaled$ssi)
dat_scaled$unemployment_rate        = rescale(dat_scaled$unemployment_rate)
dat_scaled$female_headed_households = rescale(dat_scaled$female_headed_households)
dat_scaled$cash_assistance          = rescale(dat_scaled$cash_assistance)
dat_scaled$snap                     = rescale(dat_scaled$snap)
dat_scaled$all_families_below_poverty = rescale(dat_scaled$all_families_below_poverty)

# numeric fields only
#mat = dat_short_complete %>% select(-intern_status)
#mat_scaled = scale(dat_short_complete)
heatmap.2(as.matrix(dat_scaled %>% select(-intern_status, -industry)),
          margins=c(26, 26), trace='none',
          RowSideColors=location_colors_complete)
```

![](README_files/figure-markdown_github/student_vis-1.png)

#### Median household income vs. Hours worked

``` r
library(ggplot2)
ggplot(dat_short_complete, aes(total_hours_worked, median_household_income,
                               group=intern_status), color=intern_status) +
    geom_point(aes(color=intern_status))
```

![](README_files/figure-markdown_github/household_income_vs_intern_status-1.png)

#### Intern status vs. Industry

``` r
dat_short_complete %>% group_by(industry) %>% ggplot(aes(intern_status)) +
    theme(axis.text.x = element_text(angle = 90, hjust = 1)) +
    geom_bar() +
    facet_wrap(~industry)
```

![](README_files/figure-markdown_github/intern_status_vs_industry-1.png)

``` r
#ggplot(dat_short_complete, aes(intern_status, total_hours_worked,
#                               group=industry), color=industry) +
#    geom_point(aes(color=industry))
```

Session info
============

``` r
date()
```

    ## [1] "Sat May  2 14:45:16 2015"

``` r
sessionInfo()
```

    ## R version 3.1.3 (2015-03-09)
    ## Platform: x86_64-unknown-linux-gnu (64-bit)
    ## Running under: Arch Linux
    ## 
    ## locale:
    ##  [1] LC_CTYPE=en_US.UTF-8       LC_NUMERIC=C              
    ##  [3] LC_TIME=en_US.UTF-8        LC_COLLATE=en_US.UTF-8    
    ##  [5] LC_MONETARY=en_US.UTF-8    LC_MESSAGES=en_US.UTF-8   
    ##  [7] LC_PAPER=en_US.UTF-8       LC_NAME=C                 
    ##  [9] LC_ADDRESS=C               LC_TELEPHONE=C            
    ## [11] LC_MEASUREMENT=en_US.UTF-8 LC_IDENTIFICATION=C       
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ##  [1] ggplot2_1.0.1        RColorBrewer_1.1-2   bpca_1.2-2          
    ##  [4] rgl_0.95.1201        scatterplot3d_0.3-35 gplots_2.16.0       
    ##  [7] dplyr_0.4.1          readr_0.1.0          knitr_1.9.17        
    ## [10] rmarkdown_0.5.1      knitrBootstrap_1.0.0 setwidth_1.0-3      
    ## [13] colorout_1.0-3       vimcom_1.2-3        
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] assertthat_0.1     bitops_1.0-6       caTools_1.17.1    
    ##  [4] colorspace_1.2-6   DBI_0.3.1          digest_0.6.8      
    ##  [7] evaluate_0.6       formatR_1.1        gdata_2.13.3      
    ## [10] grid_3.1.3         gtable_0.1.2       gtools_3.4.2      
    ## [13] highr_0.4.1        htmltools_0.2.6    KernSmooth_2.23-14
    ## [16] labeling_0.3       lazyeval_0.1.10    magrittr_1.5      
    ## [19] markdown_0.7.4     MASS_7.3-40        munsell_0.4.2     
    ## [22] parallel_3.1.3     plyr_1.8.1         proto_0.3-10      
    ## [25] Rcpp_0.11.5        reshape2_1.4.1     scales_0.2.4      
    ## [28] stringr_0.6.2      tools_3.1.3        yaml_2.1.13
