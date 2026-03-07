# 4 Data Importing and Tidy Data
Max Hachemeister
2026-03-07

- [Prerequisites](#prerequisites)
- [Introduction](#introduction)
  - [!Clarity](#clarity)
- [4.2 Tidy Data](#42-tidy-data)
  - [**LC4.1**](#lc41)
  - [**LC4.2**](#lc42)
  - [LC4.3](#lc43)
- [4.3 Case study: democracy in
  Guatemala](#43-case-study-democracy-in-guatemala)
  - [!clarity](#clarity-1)
  - [**LC4.4**](#lc44)
  - [**LC4.5**](#lc45)
- [4.4 Tidyverse Package](#44-tidyverse-package)

## Prerequisites

``` r
# The "setup" makes the code chunk execute before any other, when this file is accessed again.

#| label: setup
#| include: false


# packages don't get saved between sessions, so need to install them each time
## install.packages(c(
##   "dplyr",
##   "ggplot2",
##   "readr",
##   "tidyr",
##   "nycflights23",
##   "fivethirtyeight",
##   "markdown"
##   ))

# and then open the packages
library(dplyr)
```


    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':

        filter, lag

    The following objects are masked from 'package:base':

        intersect, setdiff, setequal, union

``` r
library(ggplot2)
library(readr)
library(tidyr)
library(nycflights23)
library(fivethirtyeight)
```

    Some larger datasets need to be installed separately, like senators and
    house_district_forecast. To install these, we recommend you install the
    fivethirtyeightdata package by running:
    install.packages('fivethirtyeightdata', repos =
    'https://fivethirtyeightdata.github.io/drat/', type = 'source')

``` r
library(markdown)
```

## Introduction

### !Clarity

> You will see that having data stored in “tidy” format is about more
> than just what the everyday definition of the term “tidy” might
> suggest: having your data “neatly organized.” Instead, we define the
> term “tidy” as it’s used by data scientists who use R, outlining a set
> of rules by which data is saved.

I’d like these sentences to be more homogenous, by using the same
sentence deliminator for both. So either two colons, or two commas, or
so.

``` r
library(readr)
dem_score <- read_csv("https://moderndive.com/data/dem_score.csv")
```

    Rows: 96 Columns: 10
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr (1): country
    dbl (9): 1952, 1957, 1962, 1967, 1972, 1977, 1982, 1987, 1992

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
View(dem_score)
```

## 4.2 Tidy Data

### **LC4.1**

> What are common characteristics of “tidy” data frames?

- Each Column is a Variable

  - if columns are named like “x2_height” “x2_height” “x3_height”, then
    it’s better to make another column that refers to those Xs

- Each Row is an Observation

  - this is less obvious, and I can’t think of an example at the moment.
    Gotta go fast!

- Each field contains just one value

  - yeah, these are the entries that have values like “33C/15F” or
    something.

### **LC4.2**

> What makes “tidy” data frames useful for organizing data?

It’s especially readable for machines, and therefore all of the
tidyverse functions work best with tidy data.

### LC4.3

> Take a look at the `airline_safety` data frame included in the
> `fivethirtyeight` data package.
>
> This data frame is not in “tidy” format. How would you convert this
> data frame to be in “tidy” format, in particular so that it has a
> variable `fatalities_years` indicating the incident year and a
> variable `count` of the fatality counts?

``` r
airline_safety_smaller <- airline_safety |>
  select(airline, starts_with("fatalities"))
airline_safety_smaller
```

    # A tibble: 56 × 3
       airline               fatalities_85_99 fatalities_00_14
       <chr>                            <int>            <int>
     1 Aer Lingus                           0                0
     2 Aeroflot                           128               88
     3 Aerolineas Argentinas                0                0
     4 Aeromexico                          64                0
     5 Air Canada                           0                0
     6 Air France                          79              337
     7 Air India                          329              158
     8 Air New Zealand                      0                7
     9 Alaska Airlines                      0               88
    10 Alitalia                            50                0
    # ℹ 46 more rows

Okay, we can see, that the columns have the naming xx_yy, so i can
deduce that this needs to be split up.

So I want to pivot longer, an put the names to `fatalities_years` and
the values to `count`. An I need to do this only on the columns with
`fatalities` in the name.

``` r
airline_safety_smaller |>
  pivot_longer(
    names_to = "fatalities_years",
    values_to = "count",
    cols = starts_with("fatalities")
  )
```

    # A tibble: 112 × 3
       airline               fatalities_years count
       <chr>                 <chr>            <int>
     1 Aer Lingus            fatalities_85_99     0
     2 Aer Lingus            fatalities_00_14     0
     3 Aeroflot              fatalities_85_99   128
     4 Aeroflot              fatalities_00_14    88
     5 Aerolineas Argentinas fatalities_85_99     0
     6 Aerolineas Argentinas fatalities_00_14     0
     7 Aeromexico            fatalities_85_99    64
     8 Aeromexico            fatalities_00_14     0
     9 Air Canada            fatalities_85_99     0
    10 Air Canada            fatalities_00_14     0
    # ℹ 102 more rows

Now the values for `fatalities_years` are a bit ugly still, but that’s
not the task right now.

## 4.3 Case study: democracy in Guatemala

### !clarity

I think introducing the `names_transform` argument, which only works
with the `list` function is confusing. Yeah it’s the elegant way, but
this can also be achieved with tools we’ve already learned and `mutate`
is also just one line of code. Ah yeah, doing this with mutate is also a
new concept, we haven’t really talked about converting column types very
much.

### **LC4.4**

> Convert the `dem_score` data frame into a “tidy” data frame and assign
> the name of `dem_score_tidy` to the resulting long-formatted data
> frame.

``` r
dem_score |>
  pivot_longer(
    names_to = "year",
    values_to = "democracy_score",
    cols = -country,
    names_transform = list(year = as.integer)
  )
```

    # A tibble: 864 × 3
       country    year democracy_score
       <chr>     <int>           <dbl>
     1 Albania    1952              -9
     2 Albania    1957              -9
     3 Albania    1962              -9
     4 Albania    1967              -9
     5 Albania    1972              -9
     6 Albania    1977              -9
     7 Albania    1982              -9
     8 Albania    1987              -9
     9 Albania    1992               5
    10 Argentina  1952              -9
    # ℹ 854 more rows

### **LC4.5**

> Read in the life expectancy data stored at
> <https://moderndive.com/data/le_mess.csv> and convert it to a “tidy”
> data frame.

``` r
# import data
le_mess <-
read_csv("https://moderndive.com/data/le_mess.csv")
```

    Rows: 202 Columns: 67
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr  (1): country
    dbl (66): 1951, 1952, 1953, 1954, 1955, 1956, 1957, 1958, 1959, 1960, 1961, ...

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# convert to tidy
le_mess_tidy <-
le_mess |>
  pivot_longer(
    names_to = "year",
    values_to = "life_expectancy",
    cols = -country,
    names_transform = list(year = as.integer)
  )
le_mess_tidy
```

    # A tibble: 13,332 × 3
       country      year life_expectancy
       <chr>       <int>           <dbl>
     1 Afghanistan  1951            27.1
     2 Afghanistan  1952            27.7
     3 Afghanistan  1953            28.2
     4 Afghanistan  1954            28.7
     5 Afghanistan  1955            29.3
     6 Afghanistan  1956            29.8
     7 Afghanistan  1957            30.3
     8 Afghanistan  1958            30.9
     9 Afghanistan  1959            31.4
    10 Afghanistan  1960            31.9
    # ℹ 13,322 more rows

## 4.4 Tidyverse Package
