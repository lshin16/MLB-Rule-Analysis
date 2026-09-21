Baserunning Stats
================

This is an [R Markdown](http://rmarkdown.rstudio.com) Notebook. When you
execute code within the notebook, the results appear beneath the code.

Try executing this chunk by clicking the *Run* button within the chunk
or by placing your cursor inside it and pressing *Cmd+Shift+Enter*.

``` r
sbd <- read.csv("sbd.csv")
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.2.0     ✔ readr     2.2.0
    ## ✔ forcats   1.0.1     ✔ stringr   1.6.0
    ## ✔ ggplot2   4.0.2     ✔ tibble    3.3.1
    ## ✔ lubridate 1.9.5     ✔ tidyr     1.3.2
    ## ✔ purrr     1.2.1     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(ggplot2)
sbd_clean <- sbd %>%
  filter(Year != 2020) %>%
  mutate(
    CS = as.numeric(gsub("[^0-9.]", "", CS)) / 100,  # divide by 100 to fix scale
    SB_pct = SB / (SB + CS) * 100,
    SB_attempts = SB + CS,
    rule_change = ifelse(Year >= 2023, "Post", "Pre")
  )
head(sbd_clean)
```

    ##   Year                   Tm  R.G ROE   XI RS.  SBO  SB CS SB. SB2 CS2 SB3 CS3
    ## 1 2015 Arizona Diamondbacks 4.44  62 100% 30% 2277 132 44 75% 106  35  25   8
    ## 2 2015       Atlanta Braves 3.54  55 100% 26% 2212  69 33 68%  62  31   7   2
    ## 3 2015    Baltimore Orioles 4.40  56 100% 30% 2099  44 25 64%  34  22   9   2
    ## 4 2015       Boston Red Sox 4.62  72 100% 31% 2304  71 27 72%  56  22  15   4
    ## 5 2015         Chicago Cubs 4.25  49 100% 28% 2262  95 37 72%  82  32  13   3
    ## 6 2015    Chicago White Sox 3.84  58 100% 28% 2148  68 42 62%  68  39   0   3
    ##   SBH CSH PO PCS OOB OOB1 OOB2 OOB3 OOBHm     BT XBT. X1stS X1stS2 X1stS3 X1stD
    ## 1   1   1 16  10  52   10   15   10    17 19200%  40%   326    229     96    71
    ## 2   0   0 13   6  45    9   19    7    10 11300%  40%   303    223     79    76
    ## 3   1   1 11   4  48    7   15   10    16 11600%  37%   282    199     78    76
    ## 4   0   1 11   6  62    3   26   14    19 16400%  37%   319    223     87    97
    ## 5   0   2  6   4  65    9   22   11    23 12800%  45%   281    177     97    75
    ## 6   0   0  8   3  74   16   18   18    22 15400%  37%   309    228     75    81
    ##   X1stD3 X1stDH X2ndS X2ndS3 X2ndSH   SB_pct SB_attempts rule_change
    ## 1     33     37   169     71     92 75.00000         176         Pre
    ## 2     43     31   183     66    112 67.64706         102         Pre
    ## 3     45     27   165     68     90 63.76812          69         Pre
    ## 4     47     47   180     83     88 72.44898          98         Pre
    ## 5     39     35   174     55    107 71.96970         132         Pre
    ## 6     45     35   172     61    100 61.81818         110         Pre

``` r
glimpse(sbd_clean)
```

    ## Rows: 279
    ## Columns: 37
    ## $ Year        <int> 2015, 2015, 2015, 2015, 2015, 2015, 2015, 2015, 2015, 2015…
    ## $ Tm          <chr> "Arizona Diamondbacks", "Atlanta Braves", "Baltimore Oriol…
    ## $ R.G         <dbl> 4.44, 3.54, 4.40, 4.62, 4.25, 3.84, 3.95, 4.16, 4.55, 4.28…
    ## $ ROE         <int> 62, 55, 56, 72, 49, 58, 54, 43, 55, 46, 53, 58, 52, 35, 47…
    ## $ XI          <chr> "100%", "100%", "100%", "100%", "100%", "100%", "0%", "100…
    ## $ RS.         <chr> "30%", "26%", "30%", "31%", "28%", "28%", "27%", "29%", "3…
    ## $ SBO         <int> 2277, 2212, 2099, 2304, 2262, 2148, 2150, 2283, 2121, 2227…
    ## $ SB          <int> 132, 69, 44, 71, 95, 68, 134, 86, 97, 83, 121, 104, 52, 59…
    ## $ CS          <dbl> 44, 33, 25, 27, 37, 42, 38, 28, 43, 51, 48, 34, 34, 34, 45…
    ## $ SB.         <chr> "75%", "68%", "64%", "72%", "72%", "62%", "78%", "75%", "6…
    ## $ SB2         <int> 106, 62, 34, 56, 82, 68, 99, 79, 81, 66, 99, 76, 45, 51, 9…
    ## $ CS2         <int> 35, 31, 22, 22, 32, 39, 32, 26, 37, 44, 41, 30, 31, 26, 42…
    ## $ SB3         <int> 25, 7, 9, 15, 13, 0, 35, 7, 16, 17, 21, 27, 6, 8, 14, 12, …
    ## $ CS3         <int> 8, 2, 2, 4, 3, 3, 5, 1, 5, 5, 5, 2, 2, 8, 3, 5, 3, 1, 2, 3…
    ## $ SBH         <int> 1, 0, 1, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0…
    ## $ CSH         <int> 1, 0, 1, 1, 2, 0, 1, 1, 1, 2, 2, 2, 1, 0, 0, 0, 1, 1, 0, 1…
    ## $ PO          <int> 16, 13, 11, 11, 6, 8, 11, 8, 15, 19, 21, 12, 10, 13, 19, 1…
    ## $ PCS         <int> 10, 6, 4, 6, 4, 3, 5, 3, 4, 11, 11, 8, 2, 7, 10, 7, 6, 4, …
    ## $ OOB         <int> 52, 45, 48, 62, 65, 74, 50, 48, 52, 62, 56, 59, 71, 57, 48…
    ## $ OOB1        <int> 10, 9, 7, 3, 9, 16, 7, 4, 7, 10, 6, 8, 12, 10, 5, 8, 6, 9,…
    ## $ OOB2        <int> 15, 19, 15, 26, 22, 18, 13, 15, 21, 13, 18, 20, 18, 18, 12…
    ## $ OOB3        <int> 10, 7, 10, 14, 11, 18, 7, 9, 7, 14, 14, 7, 19, 13, 11, 12,…
    ## $ OOBHm       <int> 17, 10, 16, 19, 23, 22, 23, 20, 17, 25, 18, 24, 22, 16, 20…
    ## $ BT          <chr> "19200%", "11300%", "11600%", "16400%", "12800%", "15400%"…
    ## $ XBT.        <chr> "40%", "40%", "37%", "37%", "45%", "37%", "33%", "38%", "4…
    ## $ X1stS       <int> 326, 303, 282, 319, 281, 309, 290, 284, 294, 321, 246, 314…
    ## $ X1stS2      <int> 229, 223, 199, 223, 177, 228, 228, 206, 193, 240, 159, 215…
    ## $ X1stS3      <int> 96, 79, 78, 87, 97, 75, 61, 76, 99, 71, 82, 95, 87, 76, 86…
    ## $ X1stD       <int> 71, 76, 76, 97, 75, 81, 76, 96, 62, 85, 77, 102, 74, 72, 6…
    ## $ X1stD3      <int> 33, 43, 45, 47, 39, 45, 46, 53, 28, 53, 44, 52, 33, 41, 28…
    ## $ X1stDH      <int> 37, 31, 27, 47, 35, 35, 27, 42, 34, 29, 30, 45, 39, 28, 35…
    ## $ X2ndS       <int> 169, 183, 165, 180, 174, 172, 137, 166, 174, 183, 139, 176…
    ## $ X2ndS3      <int> 71, 66, 68, 83, 55, 61, 55, 66, 53, 76, 44, 46, 51, 61, 77…
    ## $ X2ndSH      <int> 92, 112, 90, 88, 107, 100, 77, 91, 108, 100, 85, 123, 89, …
    ## $ SB_pct      <dbl> 75.00000, 67.64706, 63.76812, 72.44898, 71.96970, 61.81818…
    ## $ SB_attempts <dbl> 176, 102, 69, 98, 132, 110, 172, 114, 140, 134, 169, 138, …
    ## $ rule_change <chr> "Pre", "Pre", "Pre", "Pre", "Pre", "Pre", "Pre", "Pre", "P…

``` r
yearly <- sbd_clean %>%
  filter(Tm == "League Average") %>%
   select(Year, SB, CS, SB_attempts, SB_pct, rule_change)
```

``` r
ggplot(yearly, aes(x = Year, y = SB_attempts)) +
  geom_line(color = "steelblue", linewidth = 1.2) +
  geom_point(color = "steelblue", size = 3) +
  geom_vline(xintercept = 2023, linetype = "dashed", color = "red") +
  annotate("text", x = 2023.1, y = max(yearly$SB_attempts), 
           label = "Rule Changes", color = "red", hjust = 0) +
  scale_x_continuous(breaks = seq(2015, 2025, by = 1)) +
  labs(title = "MLB Stolen Base Attempts by Year",
       x = "Year", y = "Total Attempts (League Average)") +
  theme_minimal()
```

![](Rule-Change_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
ggplot(yearly, aes(x = Year, y = SB_pct)) +
  geom_line(color = "darkgreen", linewidth = 1.2) +
  geom_point(color = "darkgreen", size = 3) +
  geom_vline(xintercept = 2023, linetype = "dashed", color = "red") +
  annotate("text", x = 2023.1, y = max(yearly$SB_pct), 
           label = "Rule Changes", color = "red", hjust = 0) +
  scale_x_continuous(breaks = seq(2015, 2025, by = 1)) +
  labs(title = "MLB Stolen Base Success Rate by Year",
       x = "Year", y = "Success Rate (%)") +
  theme_minimal()
```

![](Rule-Change_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
model_attempts <- lm(SB_attempts ~ Year + rule_change, data = yearly)
model_rates <- lm(SB_pct ~ Year + rule_change, data = yearly)
summary(model_attempts)
```

    ## 
    ## Call:
    ## lm(formula = SB_attempts ~ Year + rule_change, data = yearly)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -8.4816  0.1265  1.0653  1.3224  5.5837 
    ## 
    ## Coefficients:
    ##                 Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)    4329.1796  1579.2584   2.741 0.033679 *  
    ## Year             -2.0653     0.7803  -2.647 0.038185 *  
    ## rule_changePre  -48.7143     5.7573  -8.461 0.000149 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 4.986 on 6 degrees of freedom
    ## Multiple R-squared:  0.9505, Adjusted R-squared:  0.934 
    ## F-statistic: 57.58 on 2 and 6 DF,  p-value: 0.0001214

``` r
summary(model_rates)
```

    ## 
    ## Call:
    ## lm(formula = SB_pct ~ Year + rule_change, data = yearly)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -1.8586 -0.6482  0.1133  0.6343  1.7485 
    ## 
    ## Coefficients:
    ##                  Estimate Std. Error t value Pr(>|t|)  
    ## (Intercept)    -1107.9234   390.2911  -2.839   0.0296 *
    ## Year               0.5864     0.1928   3.041   0.0228 *
    ## rule_changePre    -2.4608     1.4228  -1.729   0.1344  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.232 on 6 degrees of freedom
    ## Multiple R-squared:  0.9013, Adjusted R-squared:  0.8685 
    ## F-statistic: 27.41 on 2 and 6 DF,  p-value: 0.0009604

Add a new chunk by clicking the *Insert Chunk* button on the toolbar or
by pressing *Cmd+Option+I*.

When you save the notebook, an HTML file containing the code and output
will be saved alongside it (click the *Preview* button or press
*Cmd+Shift+K* to preview the HTML file).

The preview shows you a rendered HTML copy of the contents of the
editor. Consequently, unlike *Knit*, *Preview* does not run any R code
chunks. Instead, the output of the chunk when it was last run in the
editor is displayed.

``` r
write.csv(sbd, "sbd.csv", row.names = FALSE)
```
