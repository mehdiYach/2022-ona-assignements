Exercice 4
================

## 1

``` r
#install.packages('wru')
library(wru)
```

    ## Warning: le package 'wru' a été compilé avec la version R 4.1.3

``` r
library(arrow)
```

    ## Warning: le package 'arrow' a été compilé avec la version R 4.1.3

    ## 
    ## Attachement du package : 'arrow'

    ## L'objet suivant est masqué depuis 'package:utils':
    ## 
    ##     timestamp

``` r
library(lubridate)
```

    ## 
    ## Attachement du package : 'lubridate'

    ## L'objet suivant est masqué depuis 'package:arrow':
    ## 
    ##     duration

    ## Les objets suivants sont masqués depuis 'package:base':
    ## 
    ##     date, intersect, setdiff, union

``` r
library(tidyverse)
```

    ## Warning: le package 'tidyverse' a été compilé avec la version R 4.1.3

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --

    ## v ggplot2 3.3.5     v purrr   0.3.4
    ## v tibble  3.1.5     v dplyr   1.0.7
    ## v tidyr   1.2.0     v stringr 1.4.0
    ## v readr   2.0.2     v forcats 0.5.1

    ## Warning: le package 'tidyr' a été compilé avec la version R 4.1.3

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x lubridate::as.difftime() masks base::as.difftime()
    ## x lubridate::date()        masks base::date()
    ## x lubridate::duration()    masks arrow::duration()
    ## x dplyr::filter()          masks stats::filter()
    ## x lubridate::intersect()   masks base::intersect()
    ## x dplyr::lag()             masks stats::lag()
    ## x lubridate::setdiff()     masks base::setdiff()
    ## x lubridate::union()       masks base::union()

``` r
#install.packages('gender')
library(gender)
```

    ## Warning: le package 'gender' a été compilé avec la version R 4.1.3

``` r
library(gridExtra)
```

    ## 
    ## Attachement du package : 'gridExtra'

    ## L'objet suivant est masqué depuis 'package:dplyr':
    ## 
    ##     combine

``` r
library(ggplot2)
library(grid)
library(igraph)
```

    ## Warning: le package 'igraph' a été compilé avec la version R 4.1.3

    ## 
    ## Attachement du package : 'igraph'

    ## Les objets suivants sont masqués depuis 'package:dplyr':
    ## 
    ##     as_data_frame, groups, union

    ## Les objets suivants sont masqués depuis 'package:purrr':
    ## 
    ##     compose, simplify

    ## L'objet suivant est masqué depuis 'package:tidyr':
    ## 
    ##     crossing

    ## L'objet suivant est masqué depuis 'package:tibble':
    ## 
    ##     as_data_frame

    ## Les objets suivants sont masqués depuis 'package:lubridate':
    ## 
    ##     %--%, union

    ## Les objets suivants sont masqués depuis 'package:stats':
    ## 
    ##     decompose, spectrum

    ## L'objet suivant est masqué depuis 'package:base':
    ## 
    ##     union

``` r
library(ggraph)
```

    ## Warning: le package 'ggraph' a été compilé avec la version R 4.1.3

``` r
library(readr)

#install.packages("arrow")
library("arrow")

edges <- read_csv("C:/Users/Mehdi/Desktop/2022-ona-assignements/edges_sample.csv")
```

    ## Rows: 32906 Columns: 4
    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## chr  (1): application_number
    ## dbl  (2): ego_examiner_id, alter_examiner_id
    ## date (1): advice_date
    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
applications <- read_parquet("C:/Users/Mehdi/Desktop/2022-ona-assignements/app_data_sample.parquet")
```

``` r
# get list of unique first names
name_of_examiner = applications %>% distinct(examiner_name_first)
name_of_examiner
```

    ## # A tibble: 2,595 x 1
    ##    examiner_name_first
    ##    <chr>              
    ##  1 JACQUELINE         
    ##  2 BEKIR              
    ##  3 CYNTHIA            
    ##  4 MARY               
    ##  5 MICHAEL            
    ##  6 LINDA              
    ##  7 KARA               
    ##  8 VANESSA            
    ##  9 TERESA             
    ## 10 SUN                
    ## # ... with 2,585 more rows

# Get Gender

``` r
library(dplyr)
gender = name_of_examiner %>% do(outcome = gender(.$examiner_name_first, method = "ssa")) %>% unnest(cols = c(outcome), keep_empty=TRUE) %>% 
  select(examiner_name_first = name, gender,proportion_female)

gender
```

    ## # A tibble: 1,822 x 3
    ##    examiner_name_first gender proportion_female
    ##    <chr>               <chr>              <dbl>
    ##  1 AARON               male              0.0082
    ##  2 ABDEL               male              0     
    ##  3 ABDOU               male              0     
    ##  4 ABDUL               male              0     
    ##  5 ABDULHAKIM          male              0     
    ##  6 ABDULLAH            male              0     
    ##  7 ABDULLAHI           male              0     
    ##  8 ABIGAIL             female            0.998 
    ##  9 ABIMBOLA            female            0.944 
    ## 10 ABRAHAM             male              0.0031
    ## # ... with 1,812 more rows

## Joining and Cleaning

``` r
# keep only the name and the gender columns in the table
gender = gender %>% select(examiner_name_first, gender)

# Adding the gender to the previous data frame
applications = applications %>% left_join(gender, by = "examiner_name_first")

applications
```

    ## # A tibble: 2,018,477 x 17
    ##    application_number filing_date examiner_name_last examiner_name_first
    ##    <chr>              <date>      <chr>              <chr>              
    ##  1 08284457           2000-01-26  HOWARD             JACQUELINE         
    ##  2 08413193           2000-10-11  YILDIRIM           BEKIR              
    ##  3 08531853           2000-05-17  HAMILTON           CYNTHIA            
    ##  4 08637752           2001-07-20  MOSHER             MARY               
    ##  5 08682726           2000-04-10  BARR               MICHAEL            
    ##  6 08687412           2000-04-28  GRAY               LINDA              
    ##  7 08716371           2004-01-26  MCMILLIAN          KARA               
    ##  8 08765941           2000-06-23  FORD               VANESSA            
    ##  9 08776818           2000-02-04  STRZELECKA         TERESA             
    ## 10 08809677           2002-02-20  KIM                SUN                
    ## # ... with 2,018,467 more rows, and 13 more variables:
    ## #   examiner_name_middle <chr>, examiner_id <dbl>, examiner_art_unit <dbl>,
    ## #   uspc_class <chr>, uspc_subclass <chr>, patent_number <chr>,
    ## #   patent_issue_date <date>, abandon_date <date>, disposal_type <chr>,
    ## #   appl_status_code <dbl>, appl_status_date <chr>, tc <dbl>, gender <chr>

# Race

``` r
surname = applications %>% distinct(surname = examiner_name_last) 

surname
```

    ## # A tibble: 3,806 x 1
    ##    surname   
    ##    <chr>     
    ##  1 HOWARD    
    ##  2 YILDIRIM  
    ##  3 HAMILTON  
    ##  4 MOSHER    
    ##  5 BARR      
    ##  6 GRAY      
    ##  7 MCMILLIAN 
    ##  8 FORD      
    ##  9 STRZELECKA
    ## 10 KIM       
    ## # ... with 3,796 more rows

``` r
race = predict_race(voter.file=surname, surname.only=T) %>% as_tibble()
```

    ## [1] "Proceeding with surname-only predictions..."

    ## Warning in merge_surnames(voter.file): Probabilities were imputed for 698
    ## surnames that could not be matched to Census list.

``` r
# Get Race probability based on surname 

race = race %>% 
  mutate(max_proba_race = pmax( pred.his,pred.oth,pred.asi, pred.bla, pred.whi)) 

race = race %>% 
  mutate(race = case_when(
    max_proba_race == pred.bla ~ "Black",
    max_proba_race == pred.whi ~ "white",
    max_proba_race == pred.asi ~ "Asian",
    max_proba_race == pred.his ~ "Hispanic",
    max_proba_race == pred.oth ~ "Other",
    TRUE ~ NA_character_
  ))

race
```

    ## # A tibble: 3,806 x 8
    ##    surname    pred.whi pred.bla pred.his pred.asi pred.oth max_proba_race race 
    ##    <chr>         <dbl>    <dbl>    <dbl>    <dbl>    <dbl>          <dbl> <chr>
    ##  1 HOWARD       0.643   0.295    0.0237   0.005     0.0333          0.643 white
    ##  2 YILDIRIM     0.861   0.0271   0.0609   0.0135    0.0372          0.861 white
    ##  3 HAMILTON     0.702   0.237    0.0245   0.0054    0.0309          0.702 white
    ##  4 MOSHER       0.947   0.00410  0.0241   0.00640   0.0185          0.947 white
    ##  5 BARR         0.827   0.117    0.0226   0.00590   0.0271          0.827 white
    ##  6 GRAY         0.687   0.251    0.0241   0.0054    0.0324          0.687 white
    ##  7 MCMILLIAN    0.359   0.574    0.0189   0.00260   0.0463          0.574 Black
    ##  8 FORD         0.620   0.32     0.0237   0.0045    0.0313          0.620 white
    ##  9 STRZELECKA   0.666   0.0853   0.137    0.0797    0.0318          0.666 white
    ## 10 KIM          0.0252  0.00390  0.00650  0.945     0.0198          0.945 Asian
    ## # ... with 3,796 more rows

``` r
# keeping only the race and the surname
race = race %>% select(surname,race)

#Joining to the data frame
applications = applications %>% left_join(race, by = c("examiner_name_last" = "surname"))
```

# Tenure

``` r
# get filling dates, start and end date and calculate the tenure
dates = applications %>% select(examiner_id, filing_date, appl_status_date) %>% mutate(start_date = ymd(filing_date), end_date = as_date( dmy_hms(appl_status_date) )) %>% group_by(examiner_id) %>% 
  summarise(
    earliest = min(start_date, na.rm = TRUE), 
    latest = max(end_date, na.rm = TRUE),
    tenure = interval(earliest, latest) %/% days(1)
    ) %>% filter(year(latest)<2018)

dates
```

    ## # A tibble: 5,625 x 4
    ##    examiner_id earliest   latest     tenure
    ##          <dbl> <date>     <date>      <dbl>
    ##  1       59012 2004-07-28 2015-07-24   4013
    ##  2       59025 2009-10-26 2017-05-18   2761
    ##  3       59030 2005-12-12 2017-05-22   4179
    ##  4       59040 2007-09-11 2017-05-23   3542
    ##  5       59052 2001-08-21 2007-02-28   2017
    ##  6       59054 2000-11-10 2016-12-23   5887
    ##  7       59055 2004-11-02 2007-12-26   1149
    ##  8       59056 2000-03-24 2017-05-22   6268
    ##  9       59074 2000-01-31 2017-03-17   6255
    ## 10       59081 2011-04-21 2017-05-19   2220
    ## # ... with 5,615 more rows

``` r
# Join to data frame
applications = applications %>% left_join(dates, by = "examiner_id")
applications
```

    ## # A tibble: 2,018,477 x 21
    ##    application_number filing_date examiner_name_last examiner_name_first
    ##    <chr>              <date>      <chr>              <chr>              
    ##  1 08284457           2000-01-26  HOWARD             JACQUELINE         
    ##  2 08413193           2000-10-11  YILDIRIM           BEKIR              
    ##  3 08531853           2000-05-17  HAMILTON           CYNTHIA            
    ##  4 08637752           2001-07-20  MOSHER             MARY               
    ##  5 08682726           2000-04-10  BARR               MICHAEL            
    ##  6 08687412           2000-04-28  GRAY               LINDA              
    ##  7 08716371           2004-01-26  MCMILLIAN          KARA               
    ##  8 08765941           2000-06-23  FORD               VANESSA            
    ##  9 08776818           2000-02-04  STRZELECKA         TERESA             
    ## 10 08809677           2002-02-20  KIM                SUN                
    ## # ... with 2,018,467 more rows, and 17 more variables:
    ## #   examiner_name_middle <chr>, examiner_id <dbl>, examiner_art_unit <dbl>,
    ## #   uspc_class <chr>, uspc_subclass <chr>, patent_number <chr>,
    ## #   patent_issue_date <date>, abandon_date <date>, disposal_type <chr>,
    ## #   appl_status_code <dbl>, appl_status_date <chr>, tc <dbl>, gender <chr>,
    ## #   race <chr>, earliest <date>, latest <date>, tenure <dbl>

# Get application duration

``` r
#Get the filling dates
dates_of_applications = applications %>%
  select(application_number, filing_date, appl_status_date) %>%
  mutate(start_date = ymd(filing_date), end_date = as_date(dmy_hms(appl_status_date))) %>%
  summarise( application_number = application_number,filed =start_date,decision = end_date,appl_days = interval(filed,decision) %/% days(1)) %>% filter(year(decision)<2018)

#Joining 
applications =  applications %>% left_join(dates_of_applications, by = "application_number")

head(applications)
```

    ## # A tibble: 6 x 24
    ##   application_number filing_date examiner_name_last examiner_name_first
    ##   <chr>              <date>      <chr>              <chr>              
    ## 1 08284457           2000-01-26  HOWARD             JACQUELINE         
    ## 2 08413193           2000-10-11  YILDIRIM           BEKIR              
    ## 3 08531853           2000-05-17  HAMILTON           CYNTHIA            
    ## 4 08637752           2001-07-20  MOSHER             MARY               
    ## 5 08682726           2000-04-10  BARR               MICHAEL            
    ## 6 08687412           2000-04-28  GRAY               LINDA              
    ## # ... with 20 more variables: examiner_name_middle <chr>, examiner_id <dbl>,
    ## #   examiner_art_unit <dbl>, uspc_class <chr>, uspc_subclass <chr>,
    ## #   patent_number <chr>, patent_issue_date <date>, abandon_date <date>,
    ## #   disposal_type <chr>, appl_status_code <dbl>, appl_status_date <chr>,
    ## #   tc <dbl>, gender <chr>, race <chr>, earliest <date>, latest <date>,
    ## #   tenure <dbl>, filed <date>, decision <date>, appl_days <dbl>

# Dealing with missing values by imputations

``` r
#Removing variables for which it's not interesting to input missing values

my_subset = applications %>% subset( select = -c(examiner_name_middle, latest, filing_date, abandon_date, earliest, decision, filed, appl_status_date, patent_issue_date, patent_number)) %>% drop_na(examiner_id)

#install.packages('mice')
require(mice)
```

    ## Le chargement a nécessité le package : mice

    ## Warning: le package 'mice' a été compilé avec la version R 4.1.3

    ## 
    ## Attachement du package : 'mice'

    ## L'objet suivant est masqué depuis 'package:stats':
    ## 
    ##     filter

    ## Les objets suivants sont masqués depuis 'package:base':
    ## 
    ##     cbind, rbind

``` r
md.pattern(my_subset)
```

![](Exercice4_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

    ##         application_number examiner_name_last examiner_name_first examiner_id
    ## 1696847                  1                  1                   1           1
    ## 291415                   1                  1                   1           1
    ## 13926                    1                  1                   1           1
    ## 2655                     1                  1                   1           1
    ## 1                        1                  1                   1           1
    ## 21                       1                  1                   1           1
    ## 2                        1                  1                   1           1
    ## 3801                     1                  1                   1           1
    ## 550                      1                  1                   1           1
    ## 23                       1                  1                   1           1
    ## 7                        1                  1                   1           1
    ##                          0                  0                   0           0
    ##         examiner_art_unit uspc_class uspc_subclass disposal_type tc race
    ## 1696847                 1          1             1             1  1    1
    ## 291415                  1          1             1             1  1    1
    ## 13926                   1          1             1             1  1    1
    ## 2655                    1          1             1             1  1    1
    ## 1                       1          1             1             1  1    1
    ## 21                      1          1             1             1  1    1
    ## 2                       1          1             1             1  1    1
    ## 3801                    1          1             1             1  1    1
    ## 550                     1          1             1             1  1    1
    ## 23                      1          1             1             1  1    1
    ## 7                       1          1             1             1  1    1
    ##                         0          0             0             0  0    0
    ##         appl_status_code appl_days tenure gender       
    ## 1696847                1         1      1      1      0
    ## 291415                 1         1      1      0      1
    ## 13926                  1         1      0      1      1
    ## 2655                   1         1      0      0      2
    ## 1                      1         0      1      0      2
    ## 21                     1         0      0      1      2
    ## 2                      1         0      0      0      3
    ## 3801                   0         0      1      1      2
    ## 550                    0         0      1      0      3
    ## 23                     0         0      0      1      3
    ## 7                      0         0      0      0      4
    ##                     4381      4405  16634 294630 320050

``` r
# Set gender as factor to use the mice package
my_subset$gender = as.factor(my_subset$gender)
applications_2 = complete(mice(my_subset, m=3, maxit=3))
```

    ## 
    ##  iter imp variable
    ##   1   1  appl_status_code  gender  tenure  appl_days
    ##   1   2  appl_status_code  gender  tenure  appl_days
    ##   1   3  appl_status_code  gender  tenure  appl_days
    ##   2   1  appl_status_code  gender  tenure  appl_days
    ##   2   2  appl_status_code  gender  tenure  appl_days
    ##   2   3  appl_status_code  gender  tenure  appl_days
    ##   3   1  appl_status_code  gender  tenure  appl_days
    ##   3   2  appl_status_code  gender  tenure  appl_days
    ##   3   3  appl_status_code  gender  tenure  appl_days

    ## Warning: Number of logged events: 7

# Advice Network

``` r
# Get work group of examiners 
ex_art_unit = distinct(subset(applications_2, select = c(examiner_art_unit, examiner_id)))

# Add work group for the graphs
ex_art_unit$wg = substr(ex_art_unit$examiner_art_unit, 1,3)

# Merging for ego and alter

my_network = merge(x = edges, y = ex_art_unit, by.x = "ego_examiner_id", by.y = "examiner_id", all.x = TRUE)
my_network = my_network %>% rename(art_unit_ego = examiner_art_unit, wg_ego = wg)

my_network = merge(x = my_network, y = ex_art_unit, by.x = "alter_examiner_id", by.y = "examiner_id", all.x = TRUE)
my_network = my_network %>% rename(art_unit_alter = examiner_art_unit, wg_alter = wg)

my_network = drop_na(my_network)

# Ego Nodes vs alter Nodes
ego_nodes = subset(my_network, select=c(ego_examiner_id, art_unit_ego, wg_ego)) 

ego_nodes = ego_nodes %>% rename(examiner_id=ego_examiner_id,art_unit=art_unit_ego,work_group=wg_ego)

alter_nodes = subset(my_network, select=c(alter_examiner_id, art_unit_alter, wg_alter)) 

alter_nodes = alter_nodes %>% rename(examiner_id=alter_examiner_id,art_unit=art_unit_alter, work_group=wg_alter)

nodes = distinct(rbind(ego_nodes, alter_nodes)) %>% group_by(examiner_id) %>% summarise(examiner_id = first(examiner_id), art_unit = first(art_unit), work_group = first(work_group))

network = graph_from_data_frame(d=my_network, vertices=nodes, directed=TRUE)
network
```

    ## IGRAPH 9778b83 DN-- 2400 109687 -- 
    ## + attr: name (v/c), art_unit (v/n), work_group (v/c),
    ## | application_number (e/c), advice_date (e/n), art_unit_ego (e/n),
    ## | wg_ego (e/c), art_unit_alter (e/n), wg_alter (e/c)
    ## + edges from 9778b83 (vertex names):
    ##  [1] 59025->76727 59025->76727 59025->76727 59052->98224 59052->98224
    ##  [6] 59052->96682 59052->96682 59052->96682 59052->96682 59052->69249
    ## [11] 59052->69249 59052->75058 59052->75058 59052->75772 59052->75772
    ## [16] 59052->75058 59052->75058 59052->60399 59052->60399 59052->96682
    ## [21] 59052->96682 59052->94852 59052->94852 59052->60399 59052->60399
    ## [26] 59052->61813 59052->61813 59052->75772 59052->75772 59052->98224
    ## + ... omitted several edges

``` r
my_degree = round(degree(network, v=V(network)),2)
my_betweenness = round(betweenness(network),2)
my_closeness = round(closeness(network),2)

V(network)$size = my_degree
V(network)$bet = my_betweenness
V(network)$clo = my_closeness
V(network)$color = nodes$art_unit
```

``` r
#Merge the centralities 
centrality = data.frame( round( cbind(my_degree, my_betweenness, my_closeness), 2) )

centrality = cbind(examiner_id = rownames(centrality),centrality)

#Merge on applications data frame
applications_3 = merge(x=applications_2, y=centrality, by="examiner_id", all.x=TRUE)

applications_3 = drop_na(applications_3)
```

# Modeling

## Model 1: Relationship between the centrality measures and the target variable: application days

Control variables: Race, Gender, Tenure

``` r
# Setting the reference level
lm_1 = lm(appl_days ~ my_degree+ my_betweenness+ my_closeness + race + gender + tenure , data=applications_3)
summary(lm_1)
```

    ## 
    ## Call:
    ## lm(formula = appl_days ~ my_degree + my_betweenness + my_closeness + 
    ##     race + gender + tenure, data = applications_3)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -1713.2  -680.2  -269.1   327.8  4933.8 
    ## 
    ## Coefficients:
    ##                  Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)     4.150e+02  1.127e+01  36.836  < 2e-16 ***
    ## my_degree      -8.102e-02  6.494e-03 -12.476  < 2e-16 ***
    ## my_betweenness  9.501e-03  4.033e-04  23.556  < 2e-16 ***
    ## my_closeness    3.472e+01  3.198e+00  10.856  < 2e-16 ***
    ## raceBlack       3.019e+01  6.928e+00   4.359 1.31e-05 ***
    ## raceHispanic    1.025e+02  1.065e+01   9.619  < 2e-16 ***
    ## racewhite       5.063e+00  2.634e+00   1.922   0.0546 .  
    ## gendermale      3.097e+01  2.687e+00  11.525  < 2e-16 ***
    ## tenure          1.586e-01  1.785e-03  88.866  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1075 on 786115 degrees of freedom
    ## Multiple R-squared:  0.011,  Adjusted R-squared:  0.01099 
    ## F-statistic:  1093 on 8 and 786115 DF,  p-value: < 2.2e-16

### Interpretation:

All the variables coefficients seems to be very significant (very low
p-values). The race White coefficient can also be considered as
significant at 95% confidence level.

The average application process takes around 415 days for an asian
female.

The following points are noted:

    - When an examiner seeks advices from a new examiner, which is adding one unit to the centrality degree, the application processing time decreases by 0.08 days, which is approximately 2 hours.

    - When the betweeness score is increased by one unit, meaning that the amount of influence an examiner has over the flow of information in the network increases by 1, the processing time also increases by 0.009, whcih is 13 minutes.

    - Increasing by one unit the closeness score of an examiner, which is how fast the flow of information goes through the node, increases the processing time by 34 days. This means that the shorter is the distance between this examiner and the rest of them, the longer the processing time will be.

    - If the examiner is black, hispanic or white, the application will take respectively 30, 101 and 5 additional days than for an asian examiner.

    - If the examiner is a male, it will take 32 more days than for a female.

    - Increasing the tenure day by one unit also increases the processing time by 0.1 days, which is approximately 2.5 hours.

## Model 2: Interactions between centralities and gender

``` r
lm_3 = lm(appl_days ~ my_degree+ my_betweenness+ my_closeness + race + gender + tenure + gender*my_degree + gender*my_betweenness + gender*my_closeness , data=applications_3)
summary(lm_3)
```

    ## 
    ## Call:
    ## lm(formula = appl_days ~ my_degree + my_betweenness + my_closeness + 
    ##     race + gender + tenure + gender * my_degree + gender * my_betweenness + 
    ##     gender * my_closeness, data = applications_3)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -1740.4  -682.8  -270.1   327.9  5200.5 
    ## 
    ## Coefficients:
    ##                             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                4.643e+02  1.145e+01  40.568  < 2e-16 ***
    ## my_degree                 -3.585e-01  1.134e-02 -31.611  < 2e-16 ***
    ## my_betweenness             9.008e-03  1.007e-03   8.949  < 2e-16 ***
    ## my_closeness               5.020e-01  5.925e+00   0.085 0.932486    
    ## raceBlack                  2.619e+01  6.926e+00   3.781 0.000156 ***
    ## raceHispanic               1.072e+02  1.065e+01  10.062  < 2e-16 ***
    ## racewhite                  4.292e+00  2.635e+00   1.629 0.103332    
    ## gendermale                -3.169e+01  4.226e+00  -7.498 6.51e-14 ***
    ## tenure                     1.578e-01  1.787e-03  88.287  < 2e-16 ***
    ## my_degree:gendermale       4.117e-01  1.380e-02  29.824  < 2e-16 ***
    ## my_betweenness:gendermale -6.179e-04  1.100e-03  -0.562 0.574257    
    ## my_closeness:gendermale    4.776e+01  6.982e+00   6.841 7.89e-12 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1074 on 786112 degrees of freedom
    ## Multiple R-squared:  0.01215,    Adjusted R-squared:  0.01213 
    ## F-statistic: 878.6 on 11 and 786112 DF,  p-value: < 2.2e-16

### Interpretation:

All the variables coefficients seems to be very significant (very low
p-values) except the interaction term of a male examiner betweeness
degree, a hispanic examiner, and the closeness.

The interactions terms shows the following points that increasing the
centrality degree or the closeness degree of a male examiner by one unit
leads to an increase in the processing time of respectively 0.4 and 50
days comparing to a female examiner. This shows that a female that is
very well placed to influence the whole network will see her processing
time be 50 days shorter than for a male with this same characteristic.

Also, according to this new model, we have the following points:

    - The average application process takes around 465 days for an asian female.

    - When an examiner seeks advices from a new examiner, which is adding one unit to the centrality degree, the application processing time decreases by 0.3 days.

    - When the betweeness score is increased by one unit, meaning that the amount of influence an examiner has over the flow of information in the network increases by 1, the processing time also increases by 0.009 days.

    - If the examiner is black, hispanic or white, the application will take respectively 26, 106 and 4.5 additional days than for an asian examiner.

    - If the examiner is a male, it will take 31 less days than for a female.

    - Increasing the tenure day by one unit also increases the processing time by 0.2 days, which is approximately 5 hours.

# Additional discussion and implications

Within the organization, it seems like the asian examiners have the
shortest processing time while it takes 106 additionnal days for the
hispanic examiners, which is significantly greater. Asian examiners
seems to benefit more from the processing time.

The last model shows that women that are very well placed to influence
the network (measured by the closeness) will see there application
processing time shortened by 50 days compared to a male in the same
position.
