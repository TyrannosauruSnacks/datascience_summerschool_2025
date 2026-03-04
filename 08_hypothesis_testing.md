# Hypthesis Testing
Max Hachemeister
2026-03-04

- [Prerequisites](#prerequisites)

# Prerequisites

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.6
    ✔ forcats   1.0.1     ✔ stringr   1.6.0
    ✔ ggplot2   4.0.1     ✔ tibble    3.3.1
    ✔ lubridate 1.9.4     ✔ tidyr     1.3.2
    ✔ purrr     1.2.0     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(moderndive)
library(infer)
library(nycflights23)
library(ggplot2movies)
theme_set(theme_light())
```

##### !error

*Many of the relevant concepts, ideas, and* we have already introduced
many of the necessary concepts to understand hypothesis testing in
Chapters 7 and 8.

##### !error

introduce the activity that motivates the simulation-based approach for
two-sample problems, data collected from *Spotify\[,\] to* investigate
whether metal music is more popular than deep-house music. In Sections
9.3, 9.4, and 9.5 we

#### !error

9.1.1

part from the null hypothesis. We find this simplification convenient as
we focus on the equal part of the null hypothesis only *and \[it\]
becomes clearer* that evidence against the null hypothesis may come only
with values to the left of 3.6, hence a

##### !error

The decision made by the authors to call a song “popular” if it is above
the 75th percentile (3rd quartile) of popularity is arbitrary and could
be changed to any other value.*)*

##### !clarity

So, based on our sample of nm=1000 metal tracks and *nf=1000* deep house
tracks, the point estimate for pm−pd

is the difference in sample proportions

ˆpm−ˆ*pf*=0.563−0.529=0.034.

This difference in favor of metal songs of 0.034 (3.4 percentage points)
is

> Shouldn’t these be $p_d$?
