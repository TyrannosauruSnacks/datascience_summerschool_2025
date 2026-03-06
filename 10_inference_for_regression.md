# Inference for Regression
Max Hachemeister
2026-03-06

- [Prerequisites](#prerequisites)
  - [10.1](#101)
    - [Tidy data](#tidy-data)
    - [Fit linear model](#fit-linear-model)
    - [Plot data an model](#plot-data-an-model)
    - [Determine values for single
      observations](#determine-values-for-single-observations)
    - [Old Faithful Eruptions](#old-faithful-eruptions)
    - [Relating Basic Regression to Other
      Methods](#relating-basic-regression-to-other-methods)
  - [Theory-based Inference for Simple Linear
    Regression](#theory-based-inference-for-simple-linear-regression)
    - [There is a wrapper function for
      that](#there-is-a-wrapper-function-for-that)
    - [Residual Diagnostics](#residual-diagnostics)

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
library(gridExtra)
```


    Attaching package: 'gridExtra'

    The following object is masked from 'package:dplyr':

        combine

``` r
library(GGally)
theme_set(theme_light())
```

## 10.1

### Tidy data

``` r
UN_data_ch10 <- 
  un_member_states_2024 |> 
  select(country,
         life_exp = life_expectancy_2022,
         fert_rate = fertility_rate_2022) |> 
  na.omit()
UN_data_ch10
```

    # A tibble: 183 × 3
       country             life_exp fert_rate
       <chr>                  <dbl>     <dbl>
     1 Afghanistan             53.6       4.3
     2 Albania                 79.5       1.4
     3 Algeria                 78.0       2.7
     4 Angola                  62.1       5  
     5 Antigua and Barbuda     77.8       1.6
     6 Argentina               78.3       1.9
     7 Armenia                 76.1       1.6
     8 Australia               83.1       1.6
     9 Austria                 82.3       1.5
    10 Azerbaijan              74.2       1.6
    # ℹ 173 more rows

### Fit linear model

``` r
linear_model <- 
  lm(fert_rate ~ life_exp, data = UN_data_ch10)
coef(linear_model)
```

    (Intercept)    life_exp 
     12.6130315  -0.1374889 

### Plot data an model

``` r
UN_data_ch10 |> 
  ggplot(aes(life_exp, fert_rate)) +
  geom_point() +
  geom_smooth(method = "lm",
              formula = y ~ x,
              se = FALSE,
              linewidth = 0.5) +
  labs(
    title = "Relationship of Fertility Rate and Life Expectancy",
    x = "Life Expectancy",
    y = "Fertility Rate",
  )
```

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-3-1.png)

### Determine values for single observations

#### Get Row ID for France

``` r
UN_data_ch10 |> 
  rowid_to_column() |> 
  filter(country == "France") |> 
  pull(rowid)
```

    [1] 57

#### Get Fit Data for France

``` r
linear_model |> 
  get_regression_points() |> 
  filter(ID == 57)
```

    # A tibble: 1 × 5
         ID fert_rate life_exp fert_rate_hat residual
      <int>     <dbl>    <dbl>         <dbl>    <dbl>
    1    57       1.8     82.6          1.26    0.542

### Old Faithful Eruptions

#### Get Summary

``` r
old_faithful_2024 |> 
  select(duration, waiting) |> 
  tidy_summary()
```

    # A tibble: 2 × 11
      column       n group type      min    Q1  mean median    Q3   max    sd
      <chr>    <int> <chr> <chr>   <dbl> <dbl> <dbl>  <dbl> <dbl> <dbl> <dbl>
    1 duration   114 <NA>  numeric    99  180   217.   240.  259    300  59.0
    2 waiting    114 <NA>  numeric   102  139.  160.   176.  184.   201  29.9

#### Visualize

``` r
old_faithful_2024 |> 
  ggplot(aes(duration, waiting)) +
  geom_point(alpha = 0.5)
```

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-7-1.png)

#### Fit Regression Model

``` r
lm_faithful <- 
  lm(waiting ~ duration, data = old_faithful_2024)

coef(lm_faithful)
```

    (Intercept)    duration 
     79.4587241   0.3710951 

``` r
sigma(lm_faithful)
```

    [1] 20.3701

So we can see that for each minute of eruption duration the waiting time
increases by 0.371 minutes on average, while we expect the waiting time
to be off for about 20.37 minutes off of the calculated time, on
average.

### Relating Basic Regression to Other Methods

#### Two-sample difference in means

Back to the `movies_sample`

``` r
# Fit models and get infos
lm_movies <- 
  lm(rating ~ genre, data = movies_sample)

get_regression_table(lm_movies)
```

    # A tibble: 2 × 7
      term           estimate std_error statistic p_value lower_ci upper_ci
      <chr>             <dbl>     <dbl>     <dbl>   <dbl>    <dbl>    <dbl>
    1 intercept          5.28     0.265     19.9    0        4.75      5.80
    2 genre: Romance     1.05     0.364      2.88   0.005    0.321     1.77

#### ANOVA

Now the `spotify_by_genre` example

``` r
# Selects columns of interest and get a sample
spotify_for_anova <- 
  spotify_by_genre |> 
  filter(track_genre %in% c("country", "hip-hop", "rock")) |> 
  select(artists, track_name, popularity, track_genre)

spotify_for_anova |> 
  slice_sample(n = 5)
```

    # A tibble: 5 × 4
      artists                  track_name           popularity track_genre
      <chr>                    <chr>                     <dbl> <chr>      
    1 Reba McEntire;The Isaacs What Child Is This?           0 country    
    2 Jordan Sandhu            Band Theke                   58 hip-hop    
    3 Brooks & Dunn            Boot Scootin' Boogie         71 country    
    4 Bryan Adams              Summer Of '69                 0 rock       
    5 The Killers              Mr. Brightside               86 rock       

``` r
# Check with Box plots
spotify_for_anova |> 
  ggplot(aes(track_genre, popularity)) +
  geom_boxplot()
```

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-11-1.png)

``` r
# Check the means for each
spotify_for_anova |> 
  group_by(track_genre) |> 
  summarize(mean_popularity = mean(popularity))
```

    # A tibble: 3 × 2
      track_genre mean_popularity
      <chr>                 <dbl>
    1 country                17.0
    2 hip-hop                37.8
    3 rock                   19.0

``` r
# Fit linear model
lm_spotify <- 
  lm(popularity ~ track_genre, data = spotify_for_anova)

lm_spotify |> 
  get_regression_table()
```

    # A tibble: 3 × 7
      term                 estimate std_error statistic p_value lower_ci upper_ci
      <chr>                   <dbl>     <dbl>     <dbl>   <dbl>    <dbl>    <dbl>
    1 intercept               17.0      0.976     17.4    0       15.1      18.9 
    2 track_genre: hip-hop    20.7      1.38      15.0    0       18.0      23.4 
    3 track_genre: rock        1.97     1.38       1.43   0.153   -0.733     4.68

``` r
# Do an analysis of variance
aov(popularity ~ track_genre, data = spotify_for_anova) |> 
  anova()
```

    Analysis of Variance Table

    Response: popularity
                  Df  Sum Sq Mean Sq F value    Pr(>F)    
    track_genre    2  261843  130922  137.43 < 2.2e-16 ***
    Residuals   2997 2855039     953                      
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

#### LC10.1

> What does the error term $\epsilon$ in the linear model
> $Y = \beta_0 + \beta_1 \cdot X + \epsilon$ represent?

- A. The exact value of the response variable.
- B. The predicted value of the response variable based on the model.
- *C. The part of the response variable not explained by the line.*
- D. The slope of the linear relationship between $X$ and $Y$.

#### LC10.2

> Which of the following is a property of the least squares estimators
> $b_0$ and $b_1$?

- A. They are biased estimators of the population parameters $b_0$ and
  $b_1$.

- *B. They are linear combinations of the observed responses*
  *$y_1, y_2,\dots, y_n$.*

- C. They are always equal to the population parameters $b_0$ and $b_1$.

- D. They depend on the specific values of the explanatory variable $X$
  only.

#### LC10.3

> How can the difference in means between two groups be represented in a
> linear regression model?

- A. By adding an interaction term between the groups and the response
  variable.

- B. By fitting separate regression lines for each group and comparing
  their slopes.

- *C. By including a dummy variable to represent the groups.*

- D. By subtracting the mean of the group from the mean of the other and
  using this difference as the predictor.

## Theory-based Inference for Simple Linear Regression

Back to `old-faithful`

#### LC10.4

> In the context of a linear regression model, what does the null
> hypothesis $H_0 : \beta_1 = 0$ represent?

- *A. There is no linear association between the response and the*
  *explanatory variable.*

- B. The difference between the observed and predicted values is zero.

- C. The linear association between response and explanatory variable
  crosses the origin

- D. The probability of committing a Type II Error is zero.

#### LC10.5

> Which of the following is an assumption of the linear regression
> model?

- *A. The error terms $\epsilon_i$ are normally distributed with*
  *constant variance.*

- B. The error terms $\epsilon_i$ have a non-zero mean.

- C. The error terms $\epsilon_i$ are dependent on each other.

- D. The explanatory variable must be normally distributed.

#### LC10.6

> What does it mean when we say that the slope estimator $b_1$ is a
> random variable?

- A. $b_1$ will be the same for every sample taken from the population.

- B. $b_1$ is a fixed value that does not change with different samples.

- *C. $b_1$ can vary from sample to sample due to sampling variation.*

- D. $b_1$ is always equal to the population slope $\beta_1$.

### There is a wrapper function for that

``` r
lm_faithful |> 
  get_regression_table()
```

    # A tibble: 2 × 7
      term      estimate std_error statistic p_value lower_ci upper_ci
      <chr>        <dbl>     <dbl>     <dbl>   <dbl>    <dbl>    <dbl>
    1 intercept   79.5       7.31       10.9       0   65.0     93.9  
    2 duration     0.371     0.032      11.4       0    0.307    0.435

``` r
summary(lm_faithful)
```


    Call:
    lm(formula = waiting ~ duration, data = old_faithful_2024)

    Residuals:
       Min     1Q Median     3Q    Max 
    -43.09 -15.46   3.68  13.26  40.74 

    Coefficients:
                Estimate Std. Error t value Pr(>|t|)    
    (Intercept) 79.45872    7.31095   10.87   <2e-16 ***
    duration     0.37110    0.03246   11.43   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 20.37 on 112 degrees of freedom
    Multiple R-squared:  0.5386,    Adjusted R-squared:  0.5344 
    F-statistic: 130.7 on 1 and 112 DF,  p-value: < 2.2e-16

##### !error

10.2.5

The statistic column contains the t-test statistic for b0 (first row)
and b1 (second row). If we focus on b1, the t

    -test statistic was constructed using the equation

t=b1−0SE(b1)=*11.594*

which corresponds to the hypotheses H0:β1=0 versus HA:β1≠0.

> The table gives 11.4 for the $t$-value of $b_1$.

##### !error

10.2.6

or all the observations *i=1,\[dots\],n.* Recall that the residual as
defined in Subsection 5.1.3, is the observed response minus the fitted
value. If we denote the residuals with the letter e we get:

### Residual Diagnostics

#### Check Linearity

``` r
# Get residuals from model.

fit_and_residuals <- 
  get_regression_points(lm_faithful)

# Plot residuals over fitted values

fit_and_residuals |> 
  ggplot(aes(waiting_hat, residual)) +
  geom_point() +
  geom_hline(yintercept = 0, col = "blue")
```

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-16-1.png)

##### !error

Now, the observations in this dataset are only a subset of all the Old
Faithful geyser eruptions that happen during this time frame and most or
all of them are eruptions that *do\[did\] not happen\[ed\]*
sequentially, one after the next.

#### Check Normality

``` r
# Plot residuals as histogram 
fit_and_residuals |> 
  ggplot(aes(residual, y = after_stat(density))) +
  geom_histogram() +
  geom_density()
```

    `stat_bin()` using `bins = 30`. Pick better value `binwidth`.

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-17-1.png)

``` r
# Plot residuals with qq plot
fit_and_residuals |> 
  ggplot(aes(sample = residual)) +
  geom_qq() +
  geom_qq_line(color = "blue")
```

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-17-2.png)

##### !clarity

``` r
fit_and_residuals |>
  ggplot(aes(sample = residual)) +
  geom_qq() +
  geom_qq_line(color = "blue")
```

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-18-1.png)

> Should have put `color = "blue"` in `geom_qq_line()` to match the
> plot.

#### Check Equality/Constancy

Also known as *homoskedasticity*.

``` r
# Plot residuals against regressor variable

fit_and_residuals |> 
  ggplot(aes(duration, residual)) +
  geom_point(alpha = 0.6) +
  geom_hline(yintercept = 0) +
  labs(
    x = "Duration",
    y = "Residual"
  )
```

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-19-1.png)

#### LC10.7

> Use the `un_member_states_2024` data frame included in the
> `moderndive` package with response variable `fertility_rate_2022` and
> the regressor `hdi_2022`. Make sure to omit missing values.
>
> - Use the `get_regression_points()` function to get the observed
>   observed values.
>
> - Perform a residual analysis and look for any systematic patterns in
>   the residuals. Ideally, there should be little to no pattern but
>   comment on what you find here.

##### Tidy Data

``` r
# tidy data
un_tidy <- 
  un_member_states_2024 |> 
    na.omit() |> 
    select(country, 
           fert_rate = fertility_rate_2022,
           hdi = hdi_2022)

# Fit model
lm_un <- 
  lm(fert_rate ~ hdi, data = un_tidy)

# Get regression points.
un_regpoints <- 
  lm_un |> 
    get_regression_points()
```

##### Residual Analysis

###### Linearity

``` r
# Check linearity by plotting residuals over estimates

un_regpoints |> 
  ggplot(aes(fert_rate_hat, residual)) + 
  geom_point() +
  geom_hline(yintercept = 0, color = "blue")
```

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-21-1.png)

It looks almost non-linear but the downward slope is kind of corrected
by the increased spread of the points. So let’s assume it’s not
blatantly non-linear.

###### Independence

We could check the independence either with another term like time. Or
explain why we think that our sample surely is random. So for the sake
of the exercise I assume the sample to be random.

###### Normality

``` r
# Plot histogram of residuals
un_regpoints |> 
  ggplot(aes(residual, after_stat(density))) +
  geom_histogram() +
  geom_density(color = "blue")
```

    `stat_bin()` using `bins = 30`. Pick better value `binwidth`.

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-22-1.png)

``` r
# Plot qq-plot
un_regpoints |> 
  ggplot(aes(sample = residual)) +
  geom_qq() +
  geom_qq_line(color = "blue")
```

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-22-2.png)

Yeah both look close to what I think a normal distribution looks like.

##### Equality of variance / Homoskedasticity

``` r
# Plot residuals against regressor variable
un_regpoints |> 
  ggplot(aes(hdi, residual)) +
  geom_point(alpha = 0.6) +
  geom_hline(yintercept = 0)
```

![](10_inference_for_regression_files/figure-commonmark/unnamed-chunk-23-1.png)

The range of the residuals stay more or less within an value of 2 over
the hdi range, but at the higher hdi the points both aggregate but also
some more deviant values appear. Bou there seems no blatant
heteroskedasticity.

##### Verdict

Overall the assumptions to properly fit a simple linear regression for
inference are met.

#### LC10.8

> In the context of linear regression, a `p_value` of near zero for the
> slope coefficient suggests which of the following?

- A. The intercept is statistically significant at a 95% confidence
  level.

- *B. There is strong evidence against the null hypothesis that the*
  *slope coefficient is zero, suggesting there exists a linear*
  *relationship between the explanatory and response variables.*

- C. The variance of the response variable is significantly greater than
  the variance of the explanatory variable.

- D. The residuals are normally distributed with mean zero and constant
  variance.

#### LC10.9

> Explain whether or not the residual plot helps assess each one of the
> following assumptions.

- Linearity of the relationships between variables
- Independence of the error terms
- Normality of the error terms
- Equality or constancy of variance

Visualizing the residual can help assess all the above assumption to
some extent. The only thing that might not easily be checked is the
independence of the error terms, if the variable causing the dependence
has not been observed. However, if the sampling procedure can be deemed
sufficiently random the independence of the error terms can also be
assumed.

#### LC10.10

> If the residual plot against fitted values shows a “U-shaped” pattern,
> what does this suggest?

- A. The variance of the residuals is constant.
- *B. The linearity assumption is violated.*
- C. The independence assumption of violated.
- D. The normality assumption is satisfied.
