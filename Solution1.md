Solution to `tidyr::gather()` question
================
Joyce Robbins
1/31/2018

``` r
library(MASS)
library(tidyverse)
tidypaint <- painters %>% rownames_to_column("Name") %>% 
  gather(key = "Skill", value = "Score", -Name, -School)
```
