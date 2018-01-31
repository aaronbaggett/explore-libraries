01\_explore-libraries\_jenny.R
================
ab
Wed Jan 31 14:05:31 2018

``` r
## how jenny might do this in a first exploration
## purposely leaving a few things to change later!
```

Which libraries does R search for
    packages?

``` r
.libPaths()
```

    ## [1] "/Users/ab/Library/R/3.4/library"                               
    ## [2] "/Library/Frameworks/R.framework/Versions/3.4/Resources/library"

``` r
## let's confirm the second element is, in fact, the default library
.Library
```

    ## [1] "/Library/Frameworks/R.framework/Resources/library"

``` r
library(fs)
path_real(.Library)
```

    ## /Library/Frameworks/R.framework/Versions/3.4/Resources/library

Installed
    packages

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
    ## ✔ tidyr   0.8.0     ✔ stringr 1.2.0
    ## ✔ readr   1.1.1     ✔ forcats 0.2.0

    ## ── Conflicts ──────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 183

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 5 x 3
    ##   LibPath                                                 Priority       n
    ##   <chr>                                                   <chr>      <int>
    ## 1 /Library/Frameworks/R.framework/Versions/3.4/Resources… base          14
    ## 2 /Library/Frameworks/R.framework/Versions/3.4/Resources… recommend…    15
    ## 3 /Library/Frameworks/R.framework/Versions/3.4/Resources… <NA>          73
    ## 4 /Users/ab/Library/R/3.4/library                         optional       1
    ## 5 /Users/ab/Library/R/3.4/library                         <NA>          80

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                  87 0.475 
    ## 2 yes                 87 0.475 
    ## 3 <NA>                 9 0.0492

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 4 x 3
    ##   Built     n  prop
    ##   <chr> <int> <dbl>
    ## 1 3.4.0    64 0.350
    ## 2 3.4.1    19 0.104
    ## 3 3.4.2    22 0.120
    ## 4 3.4.3    78 0.426

Reflections

``` r
## reflect on ^^ and make a few notes to yourself; inspiration
##   * does the number of base + recommended packages make sense to you?
##   * how does the result of .libPaths() relate to the result of .Library?
```

Going further

``` r
## if you have time to do more ...

## is every package in .Library either base or recommended?
all_default_pkgs <- list.files(.Library)
all_br_pkgs <- ipt %>%
  filter(Priority %in% c("base", "recommended")) %>%
  pull(Package)
setdiff(all_default_pkgs, all_br_pkgs)
```

    ##  [1] "assertthat"   "backports"    "base64enc"    "BH"          
    ##  [5] "bindr"        "bindrcpp"     "broom"        "callr"       
    ##  [9] "cellranger"   "cli"          "colorspace"   "crayon"      
    ## [13] "curl"         "DBI"          "dbplyr"       "debugme"     
    ## [17] "dichromat"    "digest"       "dplyr"        "evaluate"    
    ## [21] "forcats"      "ggplot2"      "glue"         "gtable"      
    ## [25] "haven"        "highr"        "hms"          "htmltools"   
    ## [29] "httr"         "jsonlite"     "knitr"        "labeling"    
    ## [33] "lazyeval"     "lubridate"    "magrittr"     "markdown"    
    ## [37] "mime"         "mnormt"       "modelr"       "munsell"     
    ## [41] "openssl"      "pillar"       "pkgconfig"    "plogr"       
    ## [45] "plyr"         "psych"        "purrr"        "R6"          
    ## [49] "RColorBrewer" "Rcpp"         "readr"        "readxl"      
    ## [53] "rematch"      "reprex"       "reshape2"     "rlang"       
    ## [57] "rmarkdown"    "rprojroot"    "rstudioapi"   "rvest"       
    ## [61] "scales"       "selectr"      "stringi"      "stringr"     
    ## [65] "tibble"       "tidyr"        "tidyselect"   "tidyverse"   
    ## [69] "translations" "utf8"         "viridisLite"  "whisker"     
    ## [73] "xml2"         "yaml"

``` r
## study package naming style (all lower case, contains '.', etc

## use `fields` argument to installed.packages() to get more info and use it!
ipt2 <- installed.packages(fields = "URL") %>%
  as_tibble()
ipt2 %>%
  mutate(github = grepl("github", URL)) %>%
  count(github) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 x 3
    ##   github     n  prop
    ##   <lgl>  <int> <dbl>
    ## 1 F         89 0.486
    ## 2 T         94 0.514
