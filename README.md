\#UrbanDataCamp: Using Data to Help Low-Income Youth Find Jobs

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
kable(dat[1:5, 1:8])
```

| DataCamp ID | Academic Year | Location                            | Intern Status | Intern Work Site                                               |  Work Site ZIP Code| Organization                             | Industry    |
|:------------|:--------------|:------------------------------------|:--------------|:---------------------------------------------------------------|-------------------:|:-----------------------------------------|:------------|
| UADC1096    | 2014 - 2015   | DC - Year Round High School Program | Active        | Clark Construction Group, LLC Accounts Payable 2014-2015       |               20814| Clark Construction Group, LLC            | Real Estate |
| UADC1281    | 2014 - 2015   | NCR                                 | Active        | Patent and Trade Office NCR 2014-2015                          |               22314| Patent and Trade Office                  | Government  |
| UADC1129    | 2014 - 2015   | Baltimore - YAIP                    | Alumni        | ECSM BAL YAIP 2014-2015                                        |               21213| Episcopal Community Services of Maryland | Non-profit  |
| UADC1254    | 2014 - 2015   | DC - Year Round High School Program | Active        | Office of Personnel Management - Retirement Services 2014-2015 |               20240| Office of Personnel Management           | Government  |
| UADC1007    | 2014 - 2015   | NCR                                 | Active        | Alexandria City Attorney's Office NCR 2014-2015                |               22314| Alexandria City Attorney's Office        | Government  |

``` r
kable(dat[1:5, 9:16])
```

| Total Hours Worked | DOB        |  Mailing ZIP/Postal Code|  Median Household Income|  Labor Force Participation|  Unemployment Rate|    SSI|  Cash Assistance|
|:------------------:|:-----------|------------------------:|------------------------:|--------------------------:|------------------:|------:|----------------:|
|         251        | 1996-02-21 |                    20019|                    34832|                      0.595|              0.136|  0.115|            0.103|
|         162        | 1997-02-15 |                    22304|                    76061|                      0.788|              0.045|  0.022|            0.017|
|         55         | 1993-11-15 |                    21215|                    34471|                      0.555|              0.102|  0.123|            0.064|
|         129        | 1997-10-18 |                    20020|                    34685|                      0.561|              0.115|  0.113|            0.124|
|         103        | 1997-06-11 |                    22312|                    76845|                      0.757|              0.037|  0.013|            0.016|

``` r
kable(dat[1:5, 17:24])
```

|   SNAP|  All Families Below Poverty|  Educational Attainment - BA or Higher|  Female-Headed Households| Race            | Gender | High School                                                                 |  Title I School?|
|------:|---------------------------:|--------------------------------------:|-------------------------:|:----------------|:-------|:----------------------------------------------------------------------------|----------------:|
|  0.324|                       0.264|                                  0.134|                     0.188| Black           | F      | Washington Mathematics Science Technology Public Charter High School (WMST) |                1|
|  0.048|                       0.046|                                  0.586|                     0.049| Hispanic/Latino | F      | TC Williams                                                                 |                0|
|  0.268|                       0.223|                                  0.171|                     0.118| Black           | F      |                                                                             |               NA|
|  0.348|                       0.317|                                  0.160|                     0.203| Black           | M      | Ballou Senior High School                                                   |                1|
|  0.056|                       0.091|                                  0.435|                     0.044| Black           | F      | TC Williams                                                                 |                0|

``` r
kable(dat[1:5, 24:33])
```

|  Title I School?|  FRL  |  Graduation Rate of HS|  College Enrollment Rate of HS|  HS ZIP Code|  NSC Data Available?| 1st Year College Enrollment | 1st Year College Type (2/4) | 1st Year College State | Student Quote |
|----------------:|:-----:|----------------------:|------------------------------:|------------:|--------------------:|:----------------------------|:----------------------------|:-----------------------|:--------------|
|                1| 1.000 |                  0.910|                             NA|        20002|                    0| NA                          | NA                          | NA                     |               |
|                0| 0.630 |                  0.843|                             NA|        22302|                    0| NA                          | NA                          | NA                     |               |
|               NA|   NA  |                     NA|                             NA|           NA|                    0| NA                          | NA                          | NA                     |               |
|                1| 0.998 |                  0.500|                             NA|        20032|                    0| NA                          | NA                          | NA                     |               |
|                0| 0.630 |                  0.843|                             NA|        22302|                    0| NA                          | NA                          | NA                     |               |

### Variables

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