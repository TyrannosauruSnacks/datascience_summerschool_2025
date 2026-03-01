# Estimation, Confidence Intervals, and Bootstrapping
Max Hachemeister
2026-03-01

- [Prerequisites](#prerequisites)
  - [Tying the Sampling Distribution to
    Estimation](#tying-the-sampling-distribution-to-estimation)

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
theme_set(theme_light())
```

## Tying the Sampling Distribution to Estimation

#### LC8.1

> What is the expected value of the sample mean weight of almonds in a
> large sample according to the sampling distribution theory?

- A. It is always larger than the population mean.
- B. It is always smaller than the population mean.
- C. It is exactly equal to the population mean.
- *D. It is equal to the population mean on average but may vary* *in a
  single sample.*

#### LC8.2

> What is a *point estimate* and how does it doffer from an *interval*
> *estimate* in the context of statistical estimation?

- A. A point estimate uses multiple values to estimate a parameter; an
  interval estimate uses a single value.

- *B. A point estimate is a single value used to estimate a parameter;*
  *an interval estimate provides a range of values within which the*
  *parameter likely fails.*

- C. A point estimate is the mean of multiple samples; an interval
  estimate is the median.

- D. A point estimate and an interval estimate are the same and can be
  used interchangeably.
