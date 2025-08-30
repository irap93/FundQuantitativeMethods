# Frequentists Statistical Model

This is the section that most of you will find more familiar as we cover concepts typically considered "classical" statistics. These methods are tried and true, and are perhaps the most frequently seen in the literature and scientific presentations. In the best case, your data will match one of the these methods exactly. Many research projects are specifically designed to fit one of these classical techniques. Often, we may be able to transform our data or modify our model to fit a classical technique. However, this comes with the caveat that it will change the interpretability of our fitted model, and may make it more difficult to report or compare the results. But, this trade off comes with a number of positive attributes including 
- Increasing the ability to improve the computational speed and stability of our analysis
- Stable definitions of parameter estimation
- Efficient communication of analytical methods
- utilize existing R machinery

However, I strongly believe that our efforts taken build our own deterministic and statistical models engrained a deeper understanding of math and probability. These efforts should 

## Intake vs BW Relationship


``` r
# Libraries ----
library(tidyverse) # Graphing 
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.4     ✔ readr     2.1.5
## ✔ forcats   1.0.0     ✔ stringr   1.5.1
## ✔ ggplot2   3.5.2     ✔ tibble    3.2.1
## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
## ✔ purrr     1.0.4     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

``` r
library(data.table) # Reading data and manipulating data
```

```
## 
## Attaching package: 'data.table'
## 
## The following objects are masked from 'package:lubridate':
## 
##     hour, isoweek, mday, minute, month, quarter, second, wday, week,
##     yday, year
## 
## The following objects are masked from 'package:dplyr':
## 
##     between, first, last
## 
## The following object is masked from 'package:purrr':
## 
##     transpose
```

``` r
library(readxl) # Reading excel
library(qthink)

# Data
?bodyweight
data("bodyweight")
head(bodyweight)
```

```
##    Ref ID   VID          EID   Pen BW-42  BW-1   BW0   BW7  BW14  BW21  BW28
##     <num> <num>        <num> <num> <num> <num> <num> <num> <num> <num> <num>
## 1:      1   248 9.820004e+14     8   574   650   638   636   670   716   698
## 2:      2   249 9.820004e+14     8   463   514   504   496   512   544   544
## 3:      3   276 9.820004e+14     6   389   453   445   450   477   508   499
## 4:      4   298 9.820004e+14     6   436   504   489   487   522   530   530
## 5:      5   322 9.820004e+14     8   436   512   504   532   562   592   592
## 6:      6   346 9.820004e+14     6   416   446   441   398   418   443   447
##     BW35  BW42  BW49  BW56  BW70 Shipping Loss Creep_Gain Pre-Wean_ADG CreepTrt
##    <num> <num> <num> <num> <num>         <num>      <num>        <num>   <char>
## 1:   732   732   768   786   806           -12         76    1.8095238        A
## 2:   564   576   600   608   670           -10         51    1.2142857        A
## 3:   534   550   548   594   636            -8         64    1.5238095        B
## 4:   566   590   578   624   650           -15         68    1.6190476        B
## 5:   600   628   654   678   702            -8         76    1.8095238        A
## 6:   464   480   468   518   560            -5         30    0.7142857        B
##    WeanTrt
##     <char>
## 1:       A
## 2:       A
## 3:       A
## 4:       A
## 5:       A
## 6:       A
```

``` r
d.bw = melt.data.table(data = bodyweight, measure.vars = c(5:16), value.name = 'BW', variable.name = 'name')
d.bw$Day = parse_number(as.character(d.bw$name))

### Assign day
days = seq(min(d.bw$Day), max(d.bw$Day), by = 1)
days = days[days >= 0]
days
```

```
##  [1]  0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24
## [26] 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49
## [51] 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70
```

``` r
d.days = data.table(Day = days)
VIDs = data.table(VID = unique(d.bw$VID))
d.daysvid = merge(days, VIDs, by = NULL) %>% 
  as.data.table
names(d.daysvid) = c('Day','VID')

names(d.days)
```

```
## [1] "Day"
```

``` r
d.bw2 = merge.data.table(d.daysvid, d.bw, by = c('VID','Day'), all = T)
```
## Anovas


``` r
heifer
```

```
## # A tibble: 77 × 21
##      VID   Pen CreepTrt WeanTrt D_42_BW Creep_Gain Shipping_Loss Day56_InitialBW
##    <dbl> <dbl> <chr>    <chr>     <dbl>      <dbl>         <dbl>           <dbl>
##  1   248     8 A        A          261.       34.5         -5.44            287.
##  2   249     8 A        A          210.       23.1         -4.54            223.
##  3   276     6 B        A          177.       29.0         -3.63            200.
##  4   298     6 B        A          198.       30.8         -6.80            218.
##  5   322     8 A        A          198.       34.5         -3.63            233.
##  6   346     6 B        A          189.       13.6         -2.27            185.
##  7   364     5 A        B          191.       40.8        -14.1             217.
##  8   367     7 B        B          160        17.2         -8.16            164.
##  9   453     6 B        A          195        35.8        -12.2             218.
## 10   472     5 A        B          183.       37.6         -4.99            211.
## # ℹ 67 more rows
## # ℹ 13 more variables: Day56_ADG <dbl>, Day56_MMBW <dbl>, Day56_DMI <dbl>,
## #   Day56_Residual <dbl>, D_1_EV <dbl>, AVE_TTB <dbl>, BVFREQ <dbl>,
## #   BVDUR <dbl>, BVFREQsd <dbl>, BVDURsd <dbl>, uDMI <dbl>, sdDMI <dbl>,
## #   cvDMI <dbl>
```

``` r
m.anova = lm(Day56_ADG ~ CreepTrt*WeanTrt, data = heifer)
summary(m.anova)
```

```
## 
## Call:
## lm(formula = Day56_ADG ~ CreepTrt * WeanTrt, data = heifer)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.78650 -0.10719 -0.00284  0.18654  0.44387 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)         0.94891    0.05939  15.977   <2e-16 ***
## CreepTrtB          -0.01040    0.08294  -0.125    0.901    
## WeanTrtB            0.05038    0.08515   0.592    0.556    
## CreepTrtB:WeanTrtB -0.14515    0.11812  -1.229    0.223    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.2589 on 73 degrees of freedom
## Multiple R-squared:  0.0474,	Adjusted R-squared:  0.008249 
## F-statistic: 1.211 on 3 and 73 DF,  p-value: 0.312
```

``` r
library(emmeans)
```

```
## Welcome to emmeans.
## Caution: You lose important information if you filter this package's results.
## See '? untidy'
```

``` r
emmeans(m.anova, specs = 'CreepTrt','WeanTrt')
```

```
## WeanTrt = A:
##  CreepTrt emmean     SE df lower.CL upper.CL
##  A         0.949 0.0594 73    0.831    1.067
##  B         0.939 0.0579 73    0.823    1.054
## 
## WeanTrt = B:
##  CreepTrt emmean     SE df lower.CL upper.CL
##  A         0.999 0.0610 73    0.878    1.121
##  B         0.844 0.0579 73    0.728    0.959
## 
## Confidence level used: 0.95
```

``` r
library(lme4)
```

```
## Loading required package: Matrix
```

```
## 
## Attaching package: 'Matrix'
```

```
## The following objects are masked from 'package:tidyr':
## 
##     expand, pack, unpack
```

``` r
m.lmer = lmer(Day56_ADG ~ CreepTrt*WeanTrt + (1|Pen), data = heifer)
```

```
## Warning in checkConv(attr(opt, "derivs"), opt$par, ctrl = control$checkConv, :
## unable to evaluate scaled gradient
```

```
## Warning in checkConv(attr(opt, "derivs"), opt$par, ctrl = control$checkConv, :
## Model failed to converge: degenerate Hessian with 1 negative eigenvalues
```

``` r
summary(m.lmer)
```

```
## Linear mixed model fit by REML ['lmerMod']
## Formula: Day56_ADG ~ CreepTrt * WeanTrt + (1 | Pen)
##    Data: heifer
## 
## REML criterion at convergence: 21.7
## 
## Scaled residuals: 
##      Min       1Q   Median       3Q      Max 
## -3.03800 -0.41404 -0.01098  0.72053  1.71454 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev.
##  Pen      (Intercept) 0.003385 0.05818 
##  Residual             0.067022 0.25889 
## Number of obs: 77, groups:  Pen, 4
## 
## Fixed effects:
##                    Estimate Std. Error t value
## (Intercept)         0.94891    0.08314  11.413
## CreepTrtB          -0.01040    0.11683  -0.089
## WeanTrtB            0.05038    0.11841   0.425
## CreepTrtB:WeanTrtB -0.14515    0.16581  -0.875
## 
## Correlation of Fixed Effects:
##             (Intr) CrpTrB WnTrtB
## CreepTrtB   -0.712              
## WeanTrtB    -0.702  0.500       
## CrpTrtB:WTB  0.501 -0.705 -0.714
## optimizer (nloptwrap) convergence code: 0 (OK)
## unable to evaluate scaled gradient
## Model failed to converge: degenerate  Hessian with 1 negative eigenvalues
```

``` r
emmeans(m.lmer, specs = 'CreepTrt', 'WeanTrt')
```

```
## Warning in .qf.non0(object@V, x): Negative variance estimate obtained!
```

```
## Warning in .qf.non0(object@V, x): Negative variance estimate obtained!
```

```
## WeanTrt = A:
##  CreepTrt emmean     SE df lower.CL upper.CL
##  A         0.949 0.1430  0     -Inf      Inf
##  B         0.939    NaN  0      NaN      NaN
## 
## WeanTrt = B:
##  CreepTrt emmean     SE df lower.CL upper.CL
##  A         0.999    NaN  0      NaN      NaN
##  B         0.844 0.0931  0     -Inf      Inf
## 
## Degrees-of-freedom method: kenward-roger 
## Confidence level used: 0.95
```



## General Linear Models

## Example one

## Example two

## Liklihood vs. Probability
(inspired by the following [medium article](https://medium.com/@wl8380/probability-vs-likelihood-the-most-misunderstood-duo-in-data-science-759fc4dcb730))


# Demonstrating Equivalence

 I do have some concerns with the message that is conveyed by the title and approach taken in the statistical methods. Specifically, remember the definition of a p-value is “the probability of obtaining a value of the test statistic that is as likely or more likely to reject the null hypothesis (H0) as the actual observed value of the test statistic, assuming the null hypothesis is true.” The null hypothesis tested by a general linear mixed effects model for your study is generally “Newly weaned calves limit fed a high concentrate diet perform does not alter growth, performance, and efficiency compared to calves fed an ad libitum forage based diet”,  with H0: ALF = LFC and HA: ALF ≠ LFC. A high p-value in this instance indicates a failure to reject the null hypothesis. No matter how large the p-value may be, it does not provide support for the null hypothesis. Failure to reject the null hypothesis means one must now become concerned with the power, or n, of the study, and then move forward to show sufficient effort was made to demonstrate equivalence, or conversely find differences, between treatments.  This is typically illustrated by determining an acceptable delta, (difference between treatment) and then conducting a power analysis to show how many animals would be required to obtain a significant p-value at that acceptable delta given the variation in the system.
 
If this is unnecessarily restrictive or feels difficult to communicate, more studies are moving towards a more Bayesian type of approach, which allows you to specify the prior distribution of the effect size (informed by what is biologically or economically relevant) and then calculate the likelihood of observing no meaningful effect. 
