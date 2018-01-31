01\_explore-libraries\_jenny.R
================
cmg
Wed Jan 31 14:07:08 2018

``` r
## how jenny might do this in a first exploration
## purposely leaving a few things to change later!
```

Which libraries does R search for packages?

``` r
.libPaths()
```

    ## [1] "/Library/Frameworks/R.framework/Versions/3.4/Resources/library"

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

Installed packages

``` r
library(tidyverse)
```

    ## ── Attaching packages ──────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
    ## ✔ tidyr   0.8.0     ✔ stringr 1.2.0
    ## ✔ readr   1.1.1     ✔ forcats 0.2.0

    ## ── Conflicts ─────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 136

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 3 x 3
    ##   LibPath                                                 Priority       n
    ##   <chr>                                                   <chr>      <int>
    ## 1 /Library/Frameworks/R.framework/Versions/3.4/Resources… base          14
    ## 2 /Library/Frameworks/R.framework/Versions/3.4/Resources… recommend…    15
    ## 3 /Library/Frameworks/R.framework/Versions/3.4/Resources… <NA>         107

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                  61 0.449 
    ## 2 yes                 70 0.515 
    ## 3 <NA>                 5 0.0368

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 4 x 3
    ##   Built     n  prop
    ##   <chr> <int> <dbl>
    ## 1 3.4.0    58 0.426
    ## 2 3.4.1    14 0.103
    ## 3 3.4.2    15 0.110
    ## 4 3.4.3    49 0.360

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

    ##   [1] "assertthat"   "backports"    "base64enc"    "BH"          
    ##   [5] "bindr"        "bindrcpp"     "bitops"       "broom"       
    ##   [9] "callr"        "car"          "caTools"      "cellranger"  
    ##  [13] "cli"          "clipr"        "clisymbols"   "colorspace"  
    ##  [17] "crayon"       "curl"         "DBI"          "dbplyr"      
    ##  [21] "debugme"      "desc"         "dichromat"    "digest"      
    ##  [25] "dplyr"        "enc"          "evaluate"     "forcats"     
    ##  [29] "fs"           "gdata"        "ggplot2"      "gh"          
    ##  [33] "git2r"        "glue"         "gplots"       "gridExtra"   
    ##  [37] "gtable"       "gtools"       "haven"        "here"        
    ##  [41] "highr"        "hms"          "htmltools"    "httr"        
    ##  [45] "ini"          "jsonlite"     "knitr"        "labeling"    
    ##  [49] "lazyeval"     "leaps"        "lme4"         "lubridate"   
    ##  [53] "magrittr"     "markdown"     "MatrixModels" "mime"        
    ##  [57] "minqa"        "mnormt"       "modelr"       "munsell"     
    ##  [61] "nloptr"       "openssl"      "pbkrtest"     "pillar"      
    ##  [65] "pkgconfig"    "plogr"        "plotrix"      "plyr"        
    ##  [69] "praise"       "psych"        "purrr"        "quantreg"    
    ##  [73] "R6"           "RColorBrewer" "Rcpp"         "RcppEigen"   
    ##  [77] "readr"        "readxl"       "rematch"      "rematch2"    
    ##  [81] "reprex"       "reshape2"     "rlang"        "rmarkdown"   
    ##  [85] "ROCR"         "rprojroot"    "rstudioapi"   "rvest"       
    ##  [89] "scales"       "selectr"      "SparseM"      "stringi"     
    ##  [93] "stringr"      "styler"       "testthat"     "tibble"      
    ##  [97] "tidyr"        "tidyselect"   "tidyverse"    "translations"
    ## [101] "usethis"      "utf8"         "viridisLite"  "whisker"     
    ## [105] "withr"        "xml2"         "xtable"       "yaml"

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
    ## 1 F         68 0.500
    ## 2 T         68 0.500
