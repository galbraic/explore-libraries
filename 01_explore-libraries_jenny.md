01\_explore-libraries\_jenny.R
================
cmg
Wed Jan 31 14:26:18 2018

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

    ## [1] 138

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
    ## 3 /Library/Frameworks/R.framework/Versions/3.4/Resources… <NA>         109

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                  62 0.449 
    ## 2 yes                 71 0.514 
    ## 3 <NA>                 5 0.0362

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 4 x 3
    ##   Built     n  prop
    ##   <chr> <int> <dbl>
    ## 1 3.4.0    59 0.428
    ## 2 3.4.1    14 0.101
    ## 3 3.4.2    16 0.116
    ## 4 3.4.3    49 0.355

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
    ##  [21] "debugme"      "desc"         "devtools"     "dichromat"   
    ##  [25] "digest"       "dplyr"        "enc"          "evaluate"    
    ##  [29] "forcats"      "fs"           "gdata"        "ggplot2"     
    ##  [33] "gh"           "git2r"        "glue"         "gplots"      
    ##  [37] "gridExtra"    "gtable"       "gtools"       "haven"       
    ##  [41] "here"         "highr"        "hms"          "htmltools"   
    ##  [45] "httr"         "ini"          "jsonlite"     "knitr"       
    ##  [49] "labeling"     "lazyeval"     "leaps"        "lme4"        
    ##  [53] "lubridate"    "magrittr"     "markdown"     "MatrixModels"
    ##  [57] "memoise"      "mime"         "minqa"        "mnormt"      
    ##  [61] "modelr"       "munsell"      "nloptr"       "openssl"     
    ##  [65] "pbkrtest"     "pillar"       "pkgconfig"    "plogr"       
    ##  [69] "plotrix"      "plyr"         "praise"       "psych"       
    ##  [73] "purrr"        "quantreg"     "R6"           "RColorBrewer"
    ##  [77] "Rcpp"         "RcppEigen"    "readr"        "readxl"      
    ##  [81] "rematch"      "rematch2"     "reprex"       "reshape2"    
    ##  [85] "rlang"        "rmarkdown"    "ROCR"         "rprojroot"   
    ##  [89] "rstudioapi"   "rvest"        "scales"       "selectr"     
    ##  [93] "SparseM"      "stringi"      "stringr"      "styler"      
    ##  [97] "testthat"     "tibble"       "tidyr"        "tidyselect"  
    ## [101] "tidyverse"    "translations" "usethis"      "utf8"        
    ## [105] "viridisLite"  "whisker"      "withr"        "xml2"        
    ## [109] "xtable"       "yaml"

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
    ## 1 F         68 0.493
    ## 2 T         70 0.507

devtools gives all information about the session at the time of compilation. ...this is useful for debugging shared code

``` r
devtools::session_info()
```

    ## Session info -------------------------------------------------------------

    ##  setting  value                       
    ##  version  R version 3.4.3 (2017-11-30)
    ##  system   x86_64, darwin15.6.0        
    ##  ui       X11                         
    ##  language (EN)                        
    ##  collate  en_US.UTF-8                 
    ##  tz       US/Pacific                  
    ##  date     2018-01-31

    ## Packages -----------------------------------------------------------------

    ##  package    * version date       source        
    ##  assertthat   0.2.0   2017-04-11 CRAN (R 3.4.0)
    ##  backports    1.1.1   2017-09-25 CRAN (R 3.4.2)
    ##  base       * 3.4.3   2017-12-07 local         
    ##  bindr        0.1     2016-11-13 CRAN (R 3.4.0)
    ##  bindrcpp   * 0.2     2017-06-17 CRAN (R 3.4.0)
    ##  broom        0.4.3   2017-11-20 CRAN (R 3.4.3)
    ##  cellranger   1.1.0   2016-07-27 CRAN (R 3.4.0)
    ##  cli          1.0.0   2017-11-05 CRAN (R 3.4.2)
    ##  colorspace   1.3-2   2016-12-14 CRAN (R 3.4.0)
    ##  compiler     3.4.3   2017-12-07 local         
    ##  crayon       1.3.4   2017-09-16 CRAN (R 3.4.1)
    ##  datasets   * 3.4.3   2017-12-07 local         
    ##  devtools     1.13.4  2017-11-09 CRAN (R 3.4.2)
    ##  digest       0.6.12  2017-01-27 CRAN (R 3.4.0)
    ##  dplyr      * 0.7.4   2017-09-28 CRAN (R 3.4.2)
    ##  evaluate     0.10.1  2017-06-24 CRAN (R 3.4.1)
    ##  forcats    * 0.2.0   2017-01-23 CRAN (R 3.4.0)
    ##  foreign      0.8-69  2017-06-22 CRAN (R 3.4.3)
    ##  fs         * 1.1.0   2018-01-26 CRAN (R 3.4.3)
    ##  ggplot2    * 2.2.1   2016-12-30 CRAN (R 3.4.0)
    ##  glue         1.2.0   2017-10-29 CRAN (R 3.4.2)
    ##  graphics   * 3.4.3   2017-12-07 local         
    ##  grDevices  * 3.4.3   2017-12-07 local         
    ##  grid         3.4.3   2017-12-07 local         
    ##  gtable       0.2.0   2016-02-26 CRAN (R 3.4.0)
    ##  haven        1.1.1   2018-01-18 CRAN (R 3.4.3)
    ##  hms          0.4.1   2018-01-24 CRAN (R 3.4.3)
    ##  htmltools    0.3.6   2017-04-28 CRAN (R 3.4.0)
    ##  httr         1.3.1   2017-08-20 CRAN (R 3.4.1)
    ##  jsonlite     1.5     2017-06-01 CRAN (R 3.4.0)
    ##  knitr        1.17    2017-08-10 CRAN (R 3.4.1)
    ##  lattice      0.20-35 2017-03-25 CRAN (R 3.4.3)
    ##  lazyeval     0.2.0   2016-06-12 CRAN (R 3.4.0)
    ##  lubridate    1.7.1   2017-11-03 CRAN (R 3.4.2)
    ##  magrittr     1.5     2014-11-22 CRAN (R 3.4.0)
    ##  memoise      1.1.0   2017-04-21 CRAN (R 3.4.0)
    ##  methods    * 3.4.3   2017-12-07 local         
    ##  mnormt       1.5-5   2016-10-15 CRAN (R 3.4.0)
    ##  modelr       0.1.1   2017-07-24 CRAN (R 3.4.1)
    ##  munsell      0.4.3   2016-02-13 CRAN (R 3.4.0)
    ##  nlme         3.1-131 2017-02-06 CRAN (R 3.4.3)
    ##  parallel     3.4.3   2017-12-07 local         
    ##  pillar       1.1.0   2018-01-14 CRAN (R 3.4.3)
    ##  pkgconfig    2.0.1   2017-03-21 CRAN (R 3.4.0)
    ##  plyr         1.8.4   2016-06-08 CRAN (R 3.4.0)
    ##  psych        1.7.8   2017-09-09 CRAN (R 3.4.3)
    ##  purrr      * 0.2.4   2017-10-18 CRAN (R 3.4.2)
    ##  R6           2.2.2   2017-06-17 CRAN (R 3.4.0)
    ##  Rcpp         0.12.13 2017-09-28 CRAN (R 3.4.2)
    ##  readr      * 1.1.1   2017-05-16 CRAN (R 3.4.0)
    ##  readxl       1.0.0   2017-04-18 CRAN (R 3.4.0)
    ##  reshape2     1.4.2   2016-10-22 CRAN (R 3.4.0)
    ##  rlang        0.1.6   2017-12-21 CRAN (R 3.4.3)
    ##  rmarkdown    1.6     2017-06-15 CRAN (R 3.4.0)
    ##  rprojroot    1.2     2017-01-16 CRAN (R 3.4.0)
    ##  rstudioapi   0.7     2017-09-07 CRAN (R 3.4.1)
    ##  rvest        0.3.2   2016-06-17 CRAN (R 3.4.0)
    ##  scales       0.5.0   2017-08-24 CRAN (R 3.4.1)
    ##  stats      * 3.4.3   2017-12-07 local         
    ##  stringi      1.1.5   2017-04-07 CRAN (R 3.4.0)
    ##  stringr    * 1.2.0   2017-02-18 CRAN (R 3.4.0)
    ##  tibble     * 1.4.2   2018-01-22 CRAN (R 3.4.3)
    ##  tidyr      * 0.8.0   2018-01-29 CRAN (R 3.4.3)
    ##  tidyverse  * 1.2.1   2017-11-14 CRAN (R 3.4.2)
    ##  tools        3.4.3   2017-12-07 local         
    ##  utf8         1.1.3   2018-01-03 CRAN (R 3.4.3)
    ##  utils      * 3.4.3   2017-12-07 local         
    ##  withr        2.1.1   2017-12-19 CRAN (R 3.4.3)
    ##  xml2         1.2.0   2018-01-24 CRAN (R 3.4.3)
    ##  yaml         2.1.14  2016-11-12 CRAN (R 3.4.0)
