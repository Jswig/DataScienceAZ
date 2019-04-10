Predicting Startup Profitability
================
Anders Poirel

*Another exercise done using dataset from the Data Science AZ course.*

Load the data into R:

``` r
startup_data <- read.csv('P12-50-Startups.csv')
```

Create dummy variables to subsitute for states in which the startups operate, and remove the original 'State' column:

``` r
startup_data <- dummy_cols(startup_data, select_columns = "State", remove_first_dummy = TRUE)
startup_data <- startup_data[,-4]
```

Above, I omit the first dummy variable with `remove_first_dummy = TRUE` to avoid multicolinearity issues.

To avoid overfitting the model, we use backward elimination to progressively eliminate poor predictors. I use an (arbitrary) significance level of p = 0.1 as the criterion for elemination:

``` r
fit <- lm(startup_data$Profit ~ startup_data$R.D.Spend + startup_data$Administration + startup_data$Marketing.Spend + startup_data$State_California, data = startup_data)
summary(fit)
```

    ## 
    ## Call:
    ## lm(formula = startup_data$Profit ~ startup_data$R.D.Spend + startup_data$Administration + 
    ##     startup_data$Marketing.Spend + startup_data$State_California, 
    ##     data = startup_data)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -34163  -4312    113   6631  17916 
    ## 
    ## Coefficients:
    ##                                 Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                    4.908e+04  6.950e+03   7.062 8.25e-09 ***
    ## startup_data$R.D.Spend         8.080e-01  4.575e-02  17.662  < 2e-16 ***
    ## startup_data$Administration   -2.362e-02  5.186e-02  -0.455    0.651    
    ## startup_data$Marketing.Spend   2.637e-02  1.668e-02   1.581    0.121    
    ## startup_data$State_California  1.332e+03  2.690e+03   0.495    0.623    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 9309 on 45 degrees of freedom
    ## Multiple R-squared:  0.951,  Adjusted R-squared:  0.9467 
    ## F-statistic: 218.4 on 4 and 45 DF,  p-value: < 2.2e-16

"Administration"" has the highest p-value (0.651 &gt; 0.1) so I eliminate for the next step of the fitting process:

``` r
fit <- lm(startup_data$Profit ~ startup_data$R.D.Spend + startup_data$Marketing.Spend + startup_data$State_California, data = startup_data)
summary(fit)
```

    ## 
    ## Call:
    ## lm(formula = startup_data$Profit ~ startup_data$R.D.Spend + startup_data$Marketing.Spend + 
    ##     startup_data$State_California, data = startup_data)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -34332  -4681    100   5792  17834 
    ## 
    ## Coefficients:
    ##                                Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                   4.624e+04  3.013e+03  15.347   <2e-16 ***
    ## startup_data$R.D.Spend        8.003e-01  4.217e-02  18.976   <2e-16 ***
    ## startup_data$Marketing.Spend  2.859e-02  1.581e-02   1.809    0.077 .  
    ## startup_data$State_California 1.485e+03  2.646e+03   0.561    0.577    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 9228 on 46 degrees of freedom
    ## Multiple R-squared:  0.9508, Adjusted R-squared:  0.9476 
    ## F-statistic: 296.2 on 3 and 46 DF,  p-value: < 2.2e-16

Same for startup location ("State\_California"):

``` r
fit <- fit <- lm(startup_data$Profit ~ startup_data$R.D.Spend + startup_data$Marketing.Spend , data = startup_data)
summary(fit)
```

    ## 
    ## Call:
    ## lm(formula = startup_data$Profit ~ startup_data$R.D.Spend + startup_data$Marketing.Spend, 
    ##     data = startup_data)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -33645  -4632   -414   6484  17097 
    ## 
    ## Coefficients:
    ##                               Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                  4.698e+04  2.690e+03  17.464   <2e-16 ***
    ## startup_data$R.D.Spend       7.966e-01  4.135e-02  19.266   <2e-16 ***
    ## startup_data$Marketing.Spend 2.991e-02  1.552e-02   1.927     0.06 .  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 9161 on 47 degrees of freedom
    ## Multiple R-squared:  0.9505, Adjusted R-squared:  0.9483 
    ## F-statistic: 450.8 on 2 and 47 DF,  p-value: < 2.2e-16

None of the remaining prdictors have a p-value above 0.1 so the process of backwards elimination is complete.
