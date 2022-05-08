Exercice1
================

# Reading the File

``` r
library(readr)
Connections <- read_csv("C:/Users/Mehdi/Desktop/2022-ona-assignements/orgb_exercie1/Connections.csv")
#View(Connections)

head(Connections)
```

    ## # A tibble: 6 x 6
    ##   `First Name` `Last Name`   `Email Address` Company  Position    `Connected On`
    ##   <chr>        <chr>         <chr>           <chr>    <chr>       <chr>         
    ## 1 Luke         McManagan     <NA>            Scotiab~ Manager, C~ 02 May 2022   
    ## 2 Charles      Tat           <NA>            CGI      Consultant~ 30 Mar 2022   
    ## 3 Myriam       Bouamaine     <NA>            Sia Par~ Talent Acq~ 24 Mar 2022   
    ## 4 Maxime       Boissonneault <NA>            La Pres~ Board Memb~ 24 Mar 2022   
    ## 5 Gabrielle    Paquette      <NA>            MDA      Étudiante ~ 21 Mar 2022   
    ## 6 Imane        Menni         <NA>            TD       Manager pe~ 20 Mar 2022

``` r
df = Connections
```

# Count of values

``` r
library(dplyr)
library("tidyverse")
```

    ## Warning: le package 'tidyverse' a été compilé avec la version R 4.1.3

``` r
library("igraph")
```

    ## Warning: le package 'igraph' a été compilé avec la version R 4.1.3

``` r
df = df %>% drop_na(Company)

my_count <- df %>%
  group_by(Company) %>%
  count(Company, sort=TRUE) 

my_count
```

    ## # A tibble: 153 x 2
    ## # Groups:   Company [153]
    ##    Company                                                 n
    ##    <chr>                                               <int>
    ##  1 McGill University - Desautels Faculty of Management    13
    ##  2 Deloitte                                                6
    ##  3 Novartis                                                5
    ##  4 Scotiabank                                              5
    ##  5 McGill University                                       4
    ##  6 Rogers Communications                                   4
    ##  7 Accenture                                               3
    ##  8 Air Transat                                             3
    ##  9 Desjardins                                              3
    ## 10 Metrolinx                                               3
    ## # ... with 143 more rows

# Create Nodes and Edges

``` r
df = df %>%  unite(name, c("First Name", "Last Name"))

nodes = df %>% select(c("name", "Company"))
nodes = nodes %>% rowid_to_column("id")

nodes %>% head(5)
```

    ## # A tibble: 5 x 3
    ##      id name                 Company     
    ##   <int> <chr>                <chr>       
    ## 1     1 Luke_McManagan       Scotiabank  
    ## 2     2 Charles_Tat          CGI         
    ## 3     3 Myriam_Bouamaine     Sia Partners
    ## 4     4 Maxime_Boissonneault La Presse   
    ## 5     5 Gabrielle_Paquette   MDA

``` r
edges = df %>% select(c(name, Company)) %>% left_join(nodes %>% select(c(id,name)), by = c("name"="name"))
edges = edges %>% left_join(edges, by = "Company", keep=FALSE) %>% select(c("id.x", "id.y", "Company")) %>% filter(id.x!=id.y)

colnames(edges) = c("x", "y", "Company")
edges %>% head(5)
```

    ## # A tibble: 5 x 3
    ##       x     y Company     
    ##   <int> <int> <chr>       
    ## 1     1    42 Scotiabank  
    ## 2     1    63 Scotiabank  
    ## 3     1   118 Scotiabank  
    ## 4     1   146 Scotiabank  
    ## 5     3    19 Sia Partners

# Plotting the network graph

``` r
#install.packages('tidygraph')
#install.packages('ggraph')
library(tidygraph)
```

    ## Warning: le package 'tidygraph' a été compilé avec la version R 4.1.3

``` r
library(ggraph)
```

    ## Warning: le package 'ggraph' a été compilé avec la version R 4.1.3

``` r
my_graph = tbl_graph(edges = edges, nodes = nodes, directed = FALSE)

ggraph(my_graph) + 
  geom_edge_link(aes(color = Company), show.legend = FALSE) + 
  geom_node_point()+
  theme_graph()
```

![](exercice1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->
