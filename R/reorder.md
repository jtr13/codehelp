Reorder those bars, once and for all, with **forcats**
================
Joyce Robbins
2/6/2018

<img src = "../images/ggplot2SO.png" width = "500"></img>

Since I happened to be preparing to teach, among other things, *how to reorder the bars in a bar chart* when I saw Claus Wilke's response, I thought I'd write my notes up as a tutorial.

My theory is that this is considered a troublesome topic because several different problems are conflated into one question. If you identify first what the issue is, it will be much easier to know what to do. And with **forcats** functions -- namely, `fct_inorder`, `fct_relevel`, `fct_reorder`, and `fct_infreq` -- it will become second nature to get the order of the bars right. If you don't want to, you don't have to change the data at all; all of the reordering happens in the calls to `ggplot()`.

There are two key situations that call for reordering:

### 1. Bars should appear in their natural order.

The levels or categories have a natural order to them (a.k.a. ordinal data), but they are appearing in the wrong order, that is, alphabetical order unless specified otherwise.

Example:

``` r
library(tidyverse)
mycolor <- "#002448"; myfill = "#7192E3"

mydf <- tibble(Skill = c("Beginner", "Adv Beginner", "Intermediate", "Expert"),
               Num = c(75, 60, 15, 25))

ggplot(mydf, aes(Skill, Num)) + 
  geom_col(color = mycolor, fill = myfill) + theme_grey(14)
```

<img src="reorder_files/figure-markdown_github/unnamed-chunk-1-1.png" style="display: block; margin: auto;" />

The simplest way to get the order right depends on the situation at hand:

#### (a) The row order is correct

In this case, we can simply indicate with `fct_inorder()` that we want the levels to be plotted in the order in which they appear in the data frame:

``` r
mydf
```

    ## # A tibble: 4 x 2
    ##   Skill          Num
    ##   <chr>        <dbl>
    ## 1 Beginner      75.0
    ## 2 Adv Beginner  60.0
    ## 3 Intermediate  15.0
    ## 4 Expert        25.0

Since the row order in the data frame is correct, it's a simple fix:

``` r
ggplot(mydf, aes(fct_inorder(Skill), Num)) + 
  geom_col(color = mycolor, fill = myfill) + theme_grey(14)
```

<img src="reorder_files/figure-markdown_github/unnamed-chunk-3-1.png" style="display: block; margin: auto;" />

#### (b) Only one\* category is out of order

Often there's just one level out of order. In the case below it's "Under 15 years":

``` r
# 2015 U.S. Births
MotherAge <-  c("15-19 years", "20-24 years", "25-29 years", 
                "30-34 years", "35-39 years", "40-44 years",
                "45-49 years", "50 years and over", "Under 15 years")

Num <- c(229.715, 850.509, 1152.311, 1094.693, 527.996, 111.848,
            8.171, .754, 2.500)

Births2015 <- tibble(MotherAge, Num)

ggplot(Births2015, aes(MotherAge, Num)) + 
  geom_col(color = mycolor, fill = myfill) + 
  ggtitle("United States Births, 2015", subtitle = "in thousands") +
  scale_y_continuous(breaks = seq(0, 1250, 250)) +
  coord_flip() + theme_grey(14)
```

<img src="reorder_files/figure-markdown_github/unnamed-chunk-4-1.png" style="display: block; margin: auto;" />

We can use `fct_relevel` from the [**forcats**](http://forcats.tidyverse.org) package to move it where it needs to go. Although the `MotherAge` variable is character data (the tibble default is `stringsAsFactors = FALSE`), it will be coerced to factor by `fct_relevel`.

``` r
ggplot(Births2015, aes(fct_relevel(MotherAge, "Under 15 years"), Num)) + 
  ggtitle("United States Births, 2015", subtitle = "in thousands") +
  scale_y_continuous(breaks = seq(0, 1250, 250)) +
  geom_col(color = mycolor, fill = myfill) + coord_flip() + theme_grey(14)
```

<img src="reorder_files/figure-markdown_github/unnamed-chunk-5-1.png" style="display: block; margin: auto;" />

Although we can move the levels around without touching the original data, in this case, we probably do want to change the levels to the correct natural order and then plot, as follows:

``` r
Births2015 <- Births2015 %>%
  mutate(MotherAge = fct_relevel(MotherAge, "Under 15 years"))

ggplot(Births2015, aes(MotherAge, Num)) + 
  ggtitle("United States Births, 2015", subtitle = "in thousands") +
  scale_y_continuous(breaks = seq(0, 1250, 250)) +
  geom_col(color = mycolor, fill = myfill) + coord_flip() + theme_grey(14)
```

<img src="reorder_files/figure-markdown_github/unnamed-chunk-6-1.png" style="display: block; margin: auto;" />

\*Actually, we can use this technique for more than one category, as long as the categories we name all need to be moved to the same position:

``` r
x <- factor(c("A", "B", "C", "move1", "D", "E", "move2", "F"))
x
```

    ## [1] A     B     C     move1 D     E     move2 F    
    ## Levels: A B C D E F move1 move2

``` r
fct_relevel(x, "move1", "move2")   # move to the beginning (default)
```

    ## [1] A     B     C     move1 D     E     move2 F    
    ## Levels: move1 move2 A B C D E F

``` r
fct_relevel(x, "move1", "move2", after = 4) # move after the fourth item
```

    ## [1] A     B     C     move1 D     E     move2 F    
    ## Levels: A B C D move1 move2 E F

``` r
fct_relevel(x, "move1", "move2", after = Inf) # move to the end
```

    ## [1] A     B     C     move1 D     E     move2 F    
    ## Levels: A B C D E F move1 move2

Some important notes:

-   This problem has nothing to do with any other variable. There is simply a mismatch between the levels of the factors and the natural order of the categories.

-   Don't be tempted to use ordered factors even though your data has ordered levels. The levels are ordered for *all* factors.

### 2. Bars should be ordered by frequency count.

Ordering by frequency count is the recommended approach for nominal data, that is, categories that are not naturally ordered. However, the default again will be alphabetical, if not specified otherwise.

#### (a) Using `geom_col()`

Once again, the bars are ordered alphabetically, which is not what we want:

``` r
weekend_gross <- tibble(movie = c("Jumanji", "Maze Runner", "Winchester",
                        "The Greatest Snowman", "The Post"),
              gross = c(10.93, 10.475, 9.307, 7.696, 5.218))

ggplot(weekend_gross, aes(movie, gross)) + 
  ggtitle("Weekend Box Office", subtitle = "Feb 2-4, 2018") + 
  xlab("") + ylab("millions of dollars") +
  geom_col(color = mycolor, fill = myfill) + coord_flip() + theme_grey(14)
```

<img src="reorder_files/figure-markdown_github/unnamed-chunk-8-1.png" style="display: block; margin: auto;" />

This issue can be addressed within the call to `ggplot()` with `fct_reorder()`, also from **forcats**; we do not have to actually reorder the factor levels"

``` r
# note the change in the first line:
ggplot(weekend_gross, aes(fct_reorder(movie, gross), gross)) +  
  ggtitle("Weekend Box Office", subtitle = "Feb 2-4, 2018") + 
  xlab("") + ylab("millions of dollars") +
  geom_col(color = mycolor, fill = myfill) + coord_flip() + theme_grey(14)
```

<img src="reorder_files/figure-markdown_github/unnamed-chunk-9-1.png" style="display: block; margin: auto;" />

Notes:

-   Although it appears that the bars are ordered from hightest to lowest frequency count, in fact, they are ordered from lowest to highest, and plotted from the bottom up in a horizontal bar chart. If the order is wrong, you can add a minus sign to the variable which determines the order: `fct_reorder(movie, -gross)`

#### (b) Using `geom_bar()` -- data is unbinned

In this case, we can't order by another variable since we only have one variable: a list of categories:

``` r
unbinned <- tibble(response = sample(c("yes", "no", "maybe"), 100, 
                                     replace = TRUE, prob = c(.5, .15, .35)))

ggplot(unbinned, aes(response)) + geom_bar(color = mycolor, fill = myfill) +
  theme_grey(14)
```

<img src="reorder_files/figure-markdown_github/unnamed-chunk-10-1.png" style="display: block; margin: auto;" />

Again the bars are ordered alphabetically by default, not in order of frequency. The solution is a third **forcats** function, `fct_infreq()`:

``` r
ggplot(unbinned, aes(fct_infreq(response))) + geom_bar(color = mycolor, fill = myfill) +
  theme_grey(14)
```

<img src="reorder_files/figure-markdown_github/unnamed-chunk-11-1.png" style="display: block; margin: auto;" />

Thank you to Emily Zabor for convincing me to use **forcats**, and Jenny Bryan for pointing out `fct_infreq()`

\*\*\* See "Be the boss of your factors"

<http://stat545.com/block029_factors.html>
