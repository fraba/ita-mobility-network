Italy mobility network
================

Network analysis based on 2011 census data available here:
<https://www.istat.it/it/archivio/139381>

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.0     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.1     ✓ dplyr   0.8.5
    ## ✓ tidyr   1.0.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ──────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(igraph)
```

    ## 
    ## Attaching package: 'igraph'

    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     as_data_frame, groups, union

    ## The following objects are masked from 'package:purrr':
    ## 
    ##     compose, simplify

    ## The following object is masked from 'package:tidyr':
    ## 
    ##     crossing

    ## The following object is masked from 'package:tibble':
    ## 
    ##     as_data_frame

    ## The following objects are masked from 'package:stats':
    ## 
    ##     decompose, spectrum

    ## The following object is masked from 'package:base':
    ## 
    ##     union

``` r
dat <- 
  read_table("MATRICE PENDOLARISMO 2011/matrix_pendo2011_10112014.txt",
             col_names = FALSE) %>%
  dplyr::filter(X1=="S") %>%
  dplyr::mutate(from = paste0(X3, X4),
                to = paste0(X8, X9),
                n = as.numeric(X15)) %>%
  dplyr::filter(to != "000000")  %>%
  dplyr::select(from, to, n)
```

    ## Parsed with column specification:
    ## cols(
    ##   X1 = col_character(),
    ##   X2 = col_double(),
    ##   X3 = col_character(),
    ##   X4 = col_character(),
    ##   X5 = col_double(),
    ##   X6 = col_double(),
    ##   X7 = col_double(),
    ##   X8 = col_character(),
    ##   X9 = col_character(),
    ##   X10 = col_character(),
    ##   X11 = col_character(),
    ##   X12 = col_character(),
    ##   X13 = col_character(),
    ##   X14 = col_character(),
    ##   X15 = col_character()
    ## )

``` r
comune_g <- 
  graph_from_data_frame(dat,
                        directed = F) %>%
  igraph::simplify(remove.multiple = TRUE, 
                   edge.attr.comb = list(n="sum"))

comune_communities <- 
    igraph::cluster_fast_greedy(comune_g,
                      weights = E(comune_g)$n)
```
