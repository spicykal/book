## Bayesian Simple Linear Regression {#sec:simple-linear}

In this section, we will turn to Bayesian inference in simple linear regressions. We will use the reference prior distribution on coefficients, which will provide a connection between the frequentist solutions and Bayesian answers. This provides a baseline analysis for comparions with more informative prior distributions. To illustrate the ideas, we will use an example of predicting body fat. 

### Frequentist Ordinary Least Square (OLS) Simple Linear Regression

Obtaining accurate measurements of body fat is expensive and not easy to be done. Instead, predictive models that predict the percentage of body fat which use readily available measurements such as abdominal circumference are easy to use and inexpensive. We will apply a simple linear regression to predict body fat using abdominal circumference as an example to illustrate the Bayesian approach of linear regression. The data set `bodyfat` can be found from the library `BAS`. 

To start, we load the `BAS` library (which can be downloaded from CRAN) to access the dataframe. We print out a summary of the variables in this dataframe.

\indent


```r
library(BAS)
data(bodyfat)
summary(bodyfat)
```

```
##     Density         Bodyfat           Age            Weight     
##  Min.   :0.995   Min.   : 0.00   Min.   :22.00   Min.   :118.5  
##  1st Qu.:1.041   1st Qu.:12.47   1st Qu.:35.75   1st Qu.:159.0  
##  Median :1.055   Median :19.20   Median :43.00   Median :176.5  
##  Mean   :1.056   Mean   :19.15   Mean   :44.88   Mean   :178.9  
##  3rd Qu.:1.070   3rd Qu.:25.30   3rd Qu.:54.00   3rd Qu.:197.0  
##  Max.   :1.109   Max.   :47.50   Max.   :81.00   Max.   :363.1  
##      Height           Neck           Chest           Abdomen      
##  Min.   :29.50   Min.   :31.10   Min.   : 79.30   Min.   : 69.40  
##  1st Qu.:68.25   1st Qu.:36.40   1st Qu.: 94.35   1st Qu.: 84.58  
##  Median :70.00   Median :38.00   Median : 99.65   Median : 90.95  
##  Mean   :70.15   Mean   :37.99   Mean   :100.82   Mean   : 92.56  
##  3rd Qu.:72.25   3rd Qu.:39.42   3rd Qu.:105.38   3rd Qu.: 99.33  
##  Max.   :77.75   Max.   :51.20   Max.   :136.20   Max.   :148.10  
##       Hip            Thigh            Knee           Ankle     
##  Min.   : 85.0   Min.   :47.20   Min.   :33.00   Min.   :19.1  
##  1st Qu.: 95.5   1st Qu.:56.00   1st Qu.:36.98   1st Qu.:22.0  
##  Median : 99.3   Median :59.00   Median :38.50   Median :22.8  
##  Mean   : 99.9   Mean   :59.41   Mean   :38.59   Mean   :23.1  
##  3rd Qu.:103.5   3rd Qu.:62.35   3rd Qu.:39.92   3rd Qu.:24.0  
##  Max.   :147.7   Max.   :87.30   Max.   :49.10   Max.   :33.9  
##      Biceps         Forearm          Wrist      
##  Min.   :24.80   Min.   :21.00   Min.   :15.80  
##  1st Qu.:30.20   1st Qu.:27.30   1st Qu.:17.60  
##  Median :32.05   Median :28.70   Median :18.30  
##  Mean   :32.27   Mean   :28.66   Mean   :18.23  
##  3rd Qu.:34.33   3rd Qu.:30.00   3rd Qu.:18.80  
##  Max.   :45.00   Max.   :34.90   Max.   :21.40
```

</br>

This data frame includes 252 observations of men's body fat and other measurements, such as waist circumference (`Abdomen`). We will construct a Bayesian model of simple linear regression, which uses `Abdomen` to predict the response variable `Bodyfat`. Let $y_i,\ i=1,\cdots, 252$ denote the measurements of the response variable `Bodyfat`, and let $x_i$ be the waist circumference measurements `Abdomen`. We regress `Bodyfat` on the predictor `Abdomen`. This regression model can be formulated as
$$ y_i = \alpha + \beta x_i + \epsilon_i, \quad i = 1,\cdots, 252.$$
Here, we assume error $\epsilon_i$ is independent and identically distributed as normal random variables with mean zero and constant variance $\sigma^2$:
$$ \epsilon_i \iid \No(0, \sigma^2). $$

The figure below shows the percentage body fat obtained from under water weighing and the abdominal circumference measurements for 252 men. To predict body fat, the line overlayed on the scatter plot illustrates the best fitting ordinary least squares (OLS) line obtained with the `lm` function in R.


```r
# Frequentist OLS linear regression
bodyfat.lm = lm(Bodyfat ~ Abdomen, data = bodyfat)
summary(bodyfat.lm)
```

```
## 
## Call:
## lm(formula = Bodyfat ~ Abdomen, data = bodyfat)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -19.0160  -3.7557   0.0554   3.4215  12.9007 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -39.28018    2.66034  -14.77   <2e-16 ***
## Abdomen       0.63130    0.02855   22.11   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.877 on 250 degrees of freedom
## Multiple R-squared:  0.6617,	Adjusted R-squared:  0.6603 
## F-statistic: 488.9 on 1 and 250 DF,  p-value: < 2.2e-16
```

```r
# Extract coefficients
beta = coef(bodyfat.lm)

# Visualize regression line on the scatter plot
library(ggplot2)
ggplot(data = bodyfat, aes(x = Abdomen, y = Bodyfat)) +
  geom_point(color = "blue") +
  geom_abline(intercept = beta[1], slope = beta[2], size = 1) +
  xlab("abdomen circumference (cm)") 
```



\begin{center}\includegraphics[width=0.7\linewidth]{06-regression-01-Bayesian-simple-regression_files/figure-latex/unnamed-chunk-1-1} \end{center}

From the summary, we see that this model has an estimated slope, $\hat{\beta}$, of 0.63 and an estimated $y$-intercept, $\hat{\alpha}$, of about -39.28%. This gives us the prediction formula 
$$ \widehat{\text{Bodyfat}} = -39.28 + 0.63\times\text{Abdomen}. $$
For every additional centimeter, we expect body fat to increase by 0.63%. The negative $y$-intercept of course does not make sense as a physical model, but neither does predicting a male with a waist of zero centimeter. Nevertheless, this linear regression may be an accurate approximation for prediction purpose for measurements that are in the observed range for this population. 

Each of the residuals, which provide an estimate of the fitting error, is equal to $\hat{\epsilon}_i = y_i - \hat{y}_i$, the difference between the observed value $y_i$ and the fited value $\hat{y}_i = \hat{\alpha} + \hat{\beta}x_i$, where $x_i$ is the abdominal circumference for the $i$th male. $\hat{\epsilon}_i$ is used for diagnostics as well as estimating the constant variance in the assumption of the model $\sigma^2$ via the mean squared error (MSE):
$$ \hat{\sigma}^2 = \frac{1}{n-2}\sum_i^n (y_i-\hat{y}_i)^2 = \frac{1}{n-2}\sum_i^n \hat{\epsilon}_i^2. $$
Here the degrees of freedom $n-2$ are the number of observations adjusted for the number of parameters (which is 2) that we estimated in the regression. The MSE, $\hat{\sigma}^2$, may be calculated through squaring the residuals of the output of `bodyfat.lm`.



```r
# Obtain residuals and n
resid = residuals(bodyfat.lm)
n = length(resid)

# Calculate MSE
MSE = 1/ (n - 2) * sum((resid ^ 2))
MSE
```

```
## [1] 23.78985
```


If this model is correct, the residuals and fitted values should be uncorrelated, and the expected value of the residuals is zero. We apply the scatterplot of residuals versus fitted values, which provides an additional visual check of the model adequacy.


```r
# Combine residuals and fitted values into a data frame
result = data.frame(fitted_values = fitted.values(bodyfat.lm),
                    residuals = residuals(bodyfat.lm))

# Load library and plot residuals versus fitted values
library(ggplot2)
ggplot(data = result, aes(x = fitted_values, y = residuals)) +
  geom_point(pch = 1, size = 2) + 
  geom_abline(intercept = 0, slope = 0) + 
  xlab(expression(paste("fitted value ", widehat(Bodyfat)))) + 
  ylab("residuals")
```



\begin{center}\includegraphics[width=0.7\linewidth]{06-regression-01-Bayesian-simple-regression_files/figure-latex/unnamed-chunk-2-1} \end{center}

```r
# Readers may also use `plot` function
```

With the exception of one observation for the individual with the largest fitted value, the residual plot suggests that this linear regression is a reasonable approximation. The case number of the observation with the largest fitted value can be obtained using the `which` function in R. Further examination of the data frame shows that this case also has the largest waist measurement `Abdomen`. This may be our potential outlier and we will have more discussion on outlier in Section \@ref(sec:Checking-outliers).


```r
# Find the observation with the largest fitted value
which.max(as.vector(fitted.values(bodyfat.lm)))
```

```
## [1] 39
```

```r
# Shows this observation has the largest Abdomen
which.max(bodyfat$Abdomen)
```

```
## [1] 39
```

Furthermore, we can check the normal probability plot of the residuals for the assumption of normally distributed errors. We see that only Case 39, the one with the largest waist measurement, is exceptionally away from the normal quantile. 



```r
plot(bodyfat.lm, which = 2)
```



\begin{center}\includegraphics[width=0.7\linewidth]{06-regression-01-Bayesian-simple-regression_files/figure-latex/unnamed-chunk-3-1} \end{center}



The confidence interval of $\alpha$ and $\beta$ can be constructed using the standard errors $\text{se}_{\alpha}$ and $\text{se}_{\beta}$ respectively. To proceed, we introduce notations of some "sums of squares"
$$
\begin{aligned}
\text{S}_{xx} = & \sum_i^n (x_i-\bar{x})^2\\
\text{S}_{yy} = & \sum_i^n (y_i-\bar{x})^2 \\
\text{S}_{xy} = & \sum_i^n (x_i-\bar{x})(y_i-\bar{y}) \\
\text{SSE}    = & \sum_i^n (y_i-\hat{y}_i)^2 = \sum_i^n \hat{\epsilon}_i^2. 
\end{aligned}
$$

The estimates of the $y$-intercept $\alpha$, and the slope $\beta$, which are denoted as $\hat{\alpha}$ and $\hat{\beta}$ respectively, can be calculated using these "sums of squares"
$$ \hat{\beta} = \frac{\sum_i (x_i-\bar{x})(y_i-\bar{y})}{\sum_i (x_i-\bar{x})^2} = \frac{\text{S}_{xy}}{\text{S}_{xx}},\qquad \qquad \hat{\alpha} = \bar{y} - \hat{\beta}\bar{x} = \bar{y}-\frac{\text{S}_{xy}}{\text{S}_{xx}}\bar{x}. $$

The last "sum of square" is the *sum of squares of errors* (SSE). Its sample mean is exactly the mean squared error (MSE) we introduced previously
$$
\hat{\sigma}^2 = \frac{\text{SSE}}{n-2} = \text{MSE}.
$$

The standard errors, $\text{se}_{\alpha}$ and $\text{se}_{\beta}$, are given as
$$ 
\begin{aligned}
\text{se}_{\alpha} = &  \sqrt{\frac{\text{SSE}}{n-2}\left(\frac{1}{n}+\frac{\bar{x}^2}{\text{S}_{xx}}\right)} = \hat{\sigma}\sqrt{\frac{1}{n}+\frac{\bar{x}^2}{\text{S}_{xx}}},\\
\text{se}_{\beta} = & \sqrt{\frac{\text{SSE}}{n-2}\frac{1}{\text{S}_{xx}}} = \frac{\hat{\sigma}}{\sqrt{\text{S}_{xx}}}.
\end{aligned}
$$

We may construct the confidence intervals of $\alpha$ and $\beta$ using the $t$-statistics
$$ 
t_\alpha^\ast = \frac{\alpha - \hat{\alpha}}{\text{se}_{\alpha}},\qquad \qquad t_\beta^\ast = \frac{\beta-\hat{\beta}}{\text{se}_{\beta}}.
$$

They both have degrees of freedom $n-2$.


### Bayesian Simple Linear Regression Using the Reference Prior

Let us now turn to the Bayesian version and show that under the reference prior, we will obtain the posterior distributions of $\alpha$ and $\beta$ analogous with the frequentist OLS results.

The Bayesian model starts with the same model as the classical frequentist approach:
$$ y_i = \alpha + \beta x_i + \epsilon_i,\quad i = 1,\cdots, n. $$
with the assumption that the errors, $\epsilon_i$, are independent and identically distributed as normal random variables with mean zero and constant variance $\sigma^2$. This assumption is exactly the same as in the classical inference case for testing and constructing confidence intervals for $\alpha$ and $\beta$. 


Our goal is to update the distributions of the unknown parameters $\alpha$, $\beta$, and $\sigma^2$, based on the data $x_1, y_1, \cdots, x_n, y_n$, where $n$ is the number of observations.   


Under the assumption that the errors $\epsilon_i$ are normally distributed with constant variance $\sigma^2$, we have for the random variable of each response $Y_i$, conditioning on the observed data $x_i$ and the parameters $\alpha,\ \beta,\ \sigma^2$, is normally distributed: 
$$ Y_i~|~x_i, \alpha, \beta,\sigma^2~ \sim~ \No(\alpha + \beta x_i, \sigma^2),\qquad i = 1,\cdots, n. $$

That is, the likelihood of each $Y_i$ given $x_i, \alpha, \beta$, and $\sigma^2$ is given by
$$ p(y_i~|~x_i, \alpha, \beta, \sigma^2) = \frac{1}{\sqrt{2\pi\sigma^2}}\exp\left(-\frac{(y_i-(\alpha+\beta  x_i))^2}{2\sigma^2}\right). $$

The likelihood of $Y_1,\cdots,Y_n$ is the product of each likelihood $p(y_i~|~x_i, \alpha, \beta,\sigma^2)$, since we assume each response $Y_i$ is independent from each other. Since this likelihood depends on the values of $\alpha$, $\beta$, and $\sigma^2$, it is sometimes denoted as a function of $\alpha$, $\beta$, and $\sigma^2$: $\mathcal{L}(\alpha, \beta, \sigma^2)$. 


We first consider the case under the reference prior, which is our standard noninformative prior. Using the reference prior, we will obtain familiar distributions as the posterior distributions of $\alpha$, $\beta$, and $\sigma^2$, which gives the analogue to the frequentist results. Here we assume the joint prior distribution of $\alpha,\ \beta$, and $\sigma^2$ to be proportional to the inverse of $\sigma^2$

\begin{equation} 
p(\alpha, \beta, \sigma^2)\propto \frac{1}{\sigma^2}.
(\#eq:joint-prior)
\end{equation}

Using the hierachical model framework, this is equivalent to assuming that the joint prior distribution of $\alpha$ and $\beta$ under $\sigma^2$ is the uniform prior, while the prior distribution of $\sigma^2$ is proportional to $\displaystyle \frac{1}{\sigma^2}$. That is
$$ p(\alpha, \beta~|~\sigma^2) \propto 1, \qquad\qquad p(\sigma^2) \propto \frac{1}{\sigma^2}, $$
Combining the two using conditional probability, we will get the same joint prior distribution \@ref(eq:joint-prior).

Then we apply the Bayes' rule to derive the joint posterior distribution after observing data $y_1,\cdots, y_n$. Bayes' rule states that the joint posterior distribution of $\alpha$, $\beta$, and $\sigma^2$ is proportional to the product of the likelihood and the joint prior distribution: 
$$
\begin{aligned}
p^*(\alpha, \beta, \sigma^2~|~y_1,\cdots,y_n) \propto & \left[\prod_i^n p(y_i~|~x_i,\alpha,\beta,\sigma^2)\right]p(\alpha, \beta,\sigma^2) \\
\propto & \left[\left(\frac{1}{(\sigma^2)^{1/2}}\exp\left(-\frac{(y_1-(\alpha+\beta x_1 ))^2}{2\sigma^2}\right)\right)\times\cdots \right.\\
& \left. \times \left(\frac{1}{(\sigma^2)^{1/2}}\exp\left(-\frac{(y_n-(\alpha +\beta x_n))^2}{2\sigma^2}\right)\right)\right]\times\left(\frac{1}{\sigma^2}\right)\\
\propto & \frac{1}{(\sigma^2)^{(n+2)/2}}\exp\left(-\frac{\sum_i\left(y_i-\alpha-\beta  x_i\right)^2}{2\sigma^2}\right)
\end{aligned}
$$

To obtain the marginal posterior distribution of $\beta$,  we need to integrate $\alpha$ and $\sigma^2$ out from the joint posterior distribution
$$ p^*(\beta~|~y_1,\cdots,y_n) = \int_0^\infty \left(\int_{-\infty}^\infty p^*(\alpha, \beta, \sigma^2~|~y_1,\cdots, y_n)\, d\alpha\right)\, d\sigma^2. $$

We leave the detailed calculation in Section \@ref(sec:derivations). It can be shown that the marginal posterior distribution of $\beta$ is the Student's $t$-distribution
$$ \beta~|~y_1,\cdots,y_n ~\sim~ \St\left(n-2,\ \hat{\beta},\ \frac{\hat{\sigma}^2}{\text{S}_{xx}}\right) = \St\left(n-2,\  \hat{\beta},\ (\text{se}_{\beta})^2\right), $$
with degrees of freedom $n-2$, center at $\hat{\beta}$, the slope estimate we obtained from the frequentist OLS model, and scale parameter $\displaystyle \frac{\hat{\sigma}^2}{\text{S}_{xx}}=\left(\text{se}_{\beta}\right)^2$, which is the square of the standard error of $\hat{\beta}$ under the frequentist OLS model.

Similarly, we can integrate out $\beta$ and $\sigma^2$ from the joint posterior distribution to get the marginal posterior distribution of $\alpha$, $p^*(\alpha~|~y_1,\cdots, y_n)$. It turns out that $p^*(\alpha~|~y_1,\cdots,y_n)$ is again a Student's $t$-distribution with degrees of freedom $n-2$, center at $\hat{\alpha}$, the $y$-intercept estimate from the frequentist OLS model, and scale parameter $\displaystyle \hat{\sigma}^2\left(\frac{1}{n}+\frac{\bar{x}^2}{\text{S}_{xx}}\right) = \left(\text{se}_{\alpha}\right)^2$, which is the square of the standard error of $\hat{\alpha}$ under the frequentist OLS model
$$ \alpha~|~y_1,\cdots,y_n~\sim~  \St\left(n-2,\ \hat{\alpha},\ \hat{\sigma}^2\left(\frac{1}{n}+\frac{\bar{x}^2}{\text{S}_{xx}}\right)\right) = \St\left(n-2,\ \hat{\alpha},\ (\text{se}_{\alpha})^2\right).$$


Finally, we can show that the marginal posterior distribution of $\sigma^2$ is the inverse Gamma distribution, or equivalently, the reciprocal of $\sigma^2$, which is the precision $\phi$, follows the Gamme distribution
$$ \phi = \frac{1}{\sigma^2}~|~y_1,\cdots,y_n \sim \Ga\left(\frac{n-2}{2}, \frac{\text{SSE}}{2}\right). $$

Moreover, similar to the Normal-Gamma conjugacy under the reference prior introduced in the previous chapters, the joint posterior distribution of $\beta, \sigma^2$, and the joint posterior distribution of $\alpha, \sigma^2$ are both Normal-Gamma. In particular, the posterior distribution of $\beta$ conditioning on $\sigma^2$ is
$$ \beta~|~\sigma^2, \text{data}~\sim ~\No\left(\hat{\beta}, \frac{\sigma^2}{\text{S}_{xx}}\right), $$

and the posterior distribution of $\alpha$ conditioning on $\sigma^2$ is
$$ \alpha~|~\sigma^2, \text{data}~\sim ~\No\left(\hat{\alpha}, \sigma^2\left(\frac{1}{n}+\frac{\bar{x}^2}{\text{S}_{xx}}\right)\right).$$

**Credible Intervals for Slope $\beta$ and $y$-Intercept $\alpha$ **

The Bayesian posterior distribution results of $\alpha$ and $\beta$ show that under the reference prior, the posterior credible intervals are in fact **numerically equivalent** to the confidence intervals from the classical frequentist OLS analysis. This provides a baseline analysis for other Bayesian analyses with other informative prior distributions or perhaps other "objective" prior distributions, such as the Cauchy distribution. (Cauchy distribution is the Student's $t$ prior with 1 degree of freedom.)

Since the credible intervals are numerically the same as the confidence intervals, We can use the `lm` function to obtain the OLS estimates and construct the credible intervals of $\alpha$ and $\beta$


```r
output = summary(bodyfat.lm)$coef[, 1:2]
output
```

```
##                Estimate Std. Error
## (Intercept) -39.2801847 2.66033696
## Abdomen       0.6313044 0.02855067
```


The columns labeled `Estimate` and `Std. Error` are equivalent to the centers (or posterior means) and scale parameters (or standard deviations) in the two Student's $t$-distributions respectively. The credible intervals of $\alpha$ and $\beta$ are the same as the frequentist confidence intervals, but now we can interpret them from the Bayesian perspective. 

The `confint` function provides 95% confidence intervals. Under the reference prior, they are equivalent to the 95% credible intervals. The code below extracts them and relabels the output as the Bayesian results.

```r
out = cbind(output, confint(bodyfat.lm))
colnames(out) = c("posterior mean", "posterior std", "2.5", "97.5")
round(out, 2)
```

```
##             posterior mean posterior std    2.5   97.5
## (Intercept)         -39.28          2.66 -44.52 -34.04
## Abdomen               0.63          0.03   0.58   0.69
```

These intervals coincide with the confidence intervals from the frequentist approach. The primary difference is the interpretation. For example, based on the data, we believe that there is 95% chance that body fat will increase by 5.75% up to 6.88% for every additional 10 centimeter increase in the waist circumference.


**Credible Intervals for the Mean $\mu_Y$ and the Prediction $y_{n+1}$**

From our assumption of the model
$$ y_i = \alpha + \beta x_i + \epsilon_i, $$
the mean of the response variable $Y$, $\mu_Y$, at the point $x_i$ is
$$ \mu_Y~|~x_i = E[Y~|~x_i] = \alpha + \beta x_i. $$

Under the reference prior, $\mu_Y$ has a posterior distributuion 
$$ 
\alpha + \beta x_i ~|~ \text{data} \sim \St(n-2,\ \hat{\alpha} + \hat{\beta} x_i,\ \text{S}_{Y|X_i}^2), 
$$
where
$$
\text{S}_{Y|X_i}^2 = \hat{\sigma}^2\left(\frac{1}{n}+\frac{(x_i-\bar{x})^2}{\text{S}_{xx}}\right)
$$

Any new prediction $y_{n+1}$ at a point $x_{n+1}$ also follows the Student's $t$-distribution
$$ 
y_{n+1}~|~\text{data}, x_{n+1}\ \sim \St\left(n-2,\  \hat{\alpha}+\hat{\beta} x_{n+1},\ \text{S}_{Y|X_{n+1}}^2\right), 
$$

where
$$ 
\text{S}_{Y|X_{n+1}}^2 =\hat{\sigma}^2+\hat{\sigma}^2\left(\frac{1}{n}+\frac{(x_{n+1}-\bar{x})^2}{\text{S}_{xx}}\right) = \hat{\sigma}^2\left(1+\frac{1}{n}+\frac{(x_{n+1}-\bar{x})^2}{\text{S}_{xx}}\right).
$$

The variance for predicting a new observation $y_{n+1}$ has an extra $\hat{\sigma}^2$ which comes from the uncertainty of a new observation about the mean $\mu_Y$ estimated by the regression line. 

We can extract these intervals using the `predict` function

```r
library(ggplot2)
# Construct current prediction
alpha = bodyfat.lm$coefficients[1]
beta = bodyfat.lm$coefficients[2]
new_x = seq(min(bodyfat$Abdomen), max(bodyfat$Abdomen), 
            length.out = 100)
y_hat = alpha + beta * new_x

# Get lower and upper bounds for mean
ymean = data.frame(predict(bodyfat.lm,
                            newdata = data.frame(Abdomen = new_x),
                            interval = "confidence",
                            level = 0.95))

# Get lower and upper bounds for prediction
ypred = data.frame(predict(bodyfat.lm,
                          newdata = data.frame(Abdomen = new_x),
                          interval = "prediction",
                          level = 0.95))

output = data.frame(x = new_x, y_hat = y_hat, ymean_lwr = ymean$lwr, ymean_upr = ymean$upr, 
                    ypred_lwr = ypred$lwr, ypred_upr = ypred$upr)

# Extract potential outlier data point
outlier = data.frame(x = bodyfat$Abdomen[39], y = bodyfat$Bodyfat[39])

# Scatter plot of original
plot1 = ggplot(data = bodyfat, aes(x = Abdomen, y = Bodyfat)) + geom_point(color = "blue")

# Add bounds of mean and prediction
plot2 = plot1 + 
  geom_line(data = output, aes(x = new_x, y = y_hat, color = "first"), lty = 1) +
  geom_line(data = output, aes(x = new_x, y = ymean_lwr, lty = "second")) +
  geom_line(data = output, aes(x = new_x, y = ymean_upr, lty = "second")) +
  geom_line(data = output, aes(x = new_x, y = ypred_upr, lty = "third")) +
  geom_line(data = output, aes(x = new_x, y = ypred_lwr, lty = "third")) + 
  scale_colour_manual(values = c("orange"), labels = "Posterior mean", name = "") + 
  scale_linetype_manual(values = c(2, 3), labels = c("95% CI for mean", "95% CI for predictions")
                        , name = "") + 
  theme_bw() + 
  theme(legend.position = c(1, 0), legend.justification = c(1.5, 0))

# Identify potential outlier
plot2 + geom_point(data = outlier, aes(x = x, y = y), color = "orange", pch = 1, cex = 6)
```



\begin{center}\includegraphics[width=0.8\linewidth]{06-regression-01-Bayesian-simple-regression_files/figure-latex/predict-intervals-1} \end{center}


Note in the above plot, the legend "CI" can mean either confidence interval or credible interval. The difference comes down to the interpretation. For example, the prediction at the same abdominal circumference as in Case 39 is


```r
pred.39 = predict(bodyfat.lm, newdata = bodyfat[39, ], interval = "prediction", level = 0.95)
out = cbind(bodyfat[39,]$Abdomen, pred.39)
colnames(out) = c("abdomen", "prediction", "lower", "upper")
out
```

```
##    abdomen prediction   lower    upper
## 39   148.1   54.21599 44.0967 64.33528
```

Based on the data, a Bayesian would expect that a man with waist circumference of 148.1 centermeters should have bodyfat of 54.216% with 95% chance thta it is between 44.097% and 64.335%.

While we expect the majority of the data will be within the prediction intervals (the short dashed grey lines), Case 39 seems to be well below the interval. We next use Bayesian methods in Section \@ref(sec:Checking-outliers) to calculate the probability that this case is abnormal or is an outlier by falling more than $k$ standard deviations from either side of the mean.


### Informative Priors {#sec:informative-prior}

Except from the noninformative reference prior, we may also consider using a more general semi-conjugate prior distribution of $\alpha$, $\beta$, and $\sigma^2$ when there is information available about the parameters. 

Since the data $y_1,\cdots,y_n$ are normally distributed, from Chapter 3 we see that a Normal-Gamma distribution will form a conjugacy in this situation. We then set up prior distributions through a hierarchical model. We first assume that, given $\sigma^2$, $\alpha$ and $\beta$ together follow the bivariate normal prior distribution, from which their marginal distributions are both normal, 
$$ 
\begin{aligned}
\alpha~|~\sigma^2 \sim & \No(a_0, \sigma^2\text{S}_\alpha) \\
\beta ~|~ \sigma^2 \sim & \No(b_0, \sigma^2\text{S}_\beta),
\end{aligned}
$$
with covariance
$$ \text{Cov}(\alpha, \beta ~|~\sigma^2) =\sigma^2 \text{S}_{\alpha\beta}. $$

Here, $\sigma^2$, $S_\alpha$, $S_\beta$, and $S_{\alpha\beta}$ are hyperparameters. This is equivalent to setting the coefficient vector $\bv = (\alpha, \beta)^T$^[$(\alpha, \beta)^T$ means we transpose the row vector $(\alpha, \beta)$ into a column vector $\left(\begin{array}{c} \alpha \\ \beta \end{array}\right)$.] to have a bivariate normal distribution with convariance matrix $\Sigma_0$
$$ \Sigma_0 = \sigma^2\left(\begin{array}{cc} S_\alpha & S_{\alpha\beta} \\
S_{\alpha\beta} & S_\beta \end{array} \right). $$
That is,
$$ \bv = (\alpha, \beta)^T ~|~\sigma^2 \sim \textsf{BivariateNormal}(\mathbf{b} = (a_0, b_0)^T, \sigma^2\Sigma_0). $$

Then for $\sigma^2$, we will impose an inverse Gamma distribution as its prior distribution
$$ 1/\sigma^2 \sim \Ga\left(\frac{\nu_0}{2}, \frac{\nu_0\sigma_0}{2}\right). $$

Now the join prior distribution of $\alpha, \beta$, and $\sigma^2$ form a distribution that is analogous to the Normal-Gamma distribution. Prior information about $\alpha$, $\beta$, and $\sigma^2$ are encoded in the hyperparameters $a_0$, $b_0$, $\text{S}_\alpha$, $\text{S}_\beta$, $\text{S}_{\alpha\beta}$, $\nu_0$, and $\sigma_0$. 

The marginal posterior distribution of the coefficient vector $\bv = (\alpha, \beta)$ will be bivariate normal, and the marginal posterior distribution of $\sigma^2$ is again an inverse Gamma distribution
$$ 1/\sigma^2~|~y_1,\cdots,y_n \sim \Ga\left(\frac{\nu_0+n}{2}, \frac{\nu_0\sigma_0^2+\text{SSE}}{2}\right). $$

One can see that the reference prior is the limiting case of this conjugate prior we impose. We usually use Gibbs sampling to approximate the joint posterior distribution instead of using the result directly, especially when we have more regression coefficients in multiple linear regression models. We omit the deviations of the posterior distributions due to the heavy use of advanced linear algebra. One can refer to @hoff2009first for more details.

Based on any prior information we have for the model, we can also impose other priors and assumptions on $\alpha$, $\beta$, and $\sigma^2$ to get different Bayesian results. Most of these priors will not form any conjugacy and will require us to use simulation methods such as Markov Chain Monte Carlo (MCMC) for approximations. We will introduce the general idea of MCMC in Chapter 8.


## (Optional) Derivations of Marginal Posterior Distributions of $\alpha$, $\beta$, $\sigma^2$ {#sec:derivations}

In this section, we will use the notations we introduced earlier such as $\text{SSE}$, the sum of squares of errors, $\hat{\sigma}^2$, the mean squared error, $\text{S}_{xx}$, $\text{se}_{\alpha}$, $\text{se}_{\beta}$ and so on to simplify our calculations.

We will also use the following quantities derived from the formula of $\bar{x}$, $\bar{y}$, $\hat{\alpha}$, and $\hat{\beta}$
$$
\begin{aligned}
& \sum_i^n (x_i-\bar{x}) = 0 \\
& \sum_i^n (y_i-\bar{y}) = 0 \\
& \sum_i^n (y_i - \hat{y}_i) = \sum_i^n (y_i - (\hat{\alpha} + \hat{\beta} x_i)) = 0\\
& \sum_i^n (x_i-\bar{x})(y_i - \hat{y}_i) = \sum_i^n (x_i-\bar{x})(y_i-\bar{y}-\hat{\beta}(x_i-\bar{x})) = \sum_i^n (x_i-\bar{x})(y_i-\bar{y})-\hat{\beta}\sum_i^n(x_i-\bar{x})^2 = 0\\
& \sum_i^n x_i^2 = \sum_i^n (x_i-\bar{x})^2 + n\bar{x}^2 = \text{S}_{xx}+n\bar{x}^2
\end{aligned}
$$

We first further simplify the numerator inside the exponential function in the formula of $p^*(\alpha, \beta, \sigma^2~|~y_1,\cdots,y_n)$:
$$ 
\begin{aligned}
 & \sum_i^n \left(y_i - \alpha - \beta x_i\right)^2 \\
 = & \sum_i^n \left(y_i - \hat{\alpha} - \hat{\beta}x_i - (\alpha - \hat{\alpha}) - (\beta - \hat{\beta})x_i\right)^2 \\
= & \sum_i^n \left(y_i - \hat{\alpha} - \hat{\beta}x_i\right)^2 + \sum_i^n (\alpha - \hat{\alpha})^2 + \sum_i^n (\beta-\hat{\beta})^2(x_i)^2 \\
  & - 2\sum_i^n (\alpha - \hat{\alpha})(y_i-\hat{\alpha}-\hat{\beta}x_i) - 2\sum_i^n (\beta-\hat{\beta})(x_i)(y_i-\hat{\alpha}-\hat{\beta}x_i) + 2\sum_i^n(\alpha - \hat{\alpha})(\beta-\hat{\beta})(x_i)\\
= & \text{SSE} + n(\alpha-\hat{\alpha})^2 + (\beta-\hat{\beta})^2\sum_i^n x_i^2 - 2(\alpha-\hat{\alpha})\sum_i^n (y_i-\hat{y}_i) -2(\beta-\hat{\beta})\sum_i^n x_i(y_i-\hat{y}_i)+2(\alpha-\hat{\alpha})(\beta-\hat{\beta})(n\bar{x})
\end{aligned}
$$

It is clear that
$$ -2(\alpha-\hat{\alpha})\sum_i^n(y_i-\hat{y}_i) = 0 $$

And
$$
\begin{aligned}
-2(\beta-\hat{\beta})\sum_i^n x_i(y_i-\hat{y}_i) = & -2(\beta-\hat{\beta})\sum_i(x_i-\bar{x})(y_i-\hat{y}_i) - 2(\beta-\hat{\beta})\sum_i^n \bar{x}(y_i-\hat{y}_i) \\
= & -2(\beta-\hat{\beta})\times 0 - 2(\beta-\hat{\beta})\bar{x}\sum_i^n(y_i-\hat{y}_i) = 0
\end{aligned}
$$

Finally, we use the quantity that $\displaystyle \sum_i^n x_i^2 = \sum_i^n(x_i-\bar{x})^2+ n\bar{x}^2$ to combine the terms $n(\alpha-\hat{\alpha})^2$, $2\displaystyle (\alpha-\hat{\alpha})(\beta-\hat{\beta})\sum_i^n x_i$, and $\displaystyle (\beta-\hat{\beta})^2\sum_i^n x_i^2$ together.

$$
\begin{aligned}
 & \sum_i^n (y_i-\alpha-\beta x_i)^2 \\
 = & \text{SSE} + n(\alpha-\hat{\alpha})^2 +(\beta-\hat{\beta})^2\sum_i^n (x_i-\bar{x})^2 + (\beta-\hat{\beta})^2 (n\bar{x}^2)  +2(\alpha-\hat{\alpha})(\beta-\hat{\beta})(n\bar{x})\\
= & \text{SSE} + (\beta-\hat{\beta})^2\text{S}_{xx} + n\left[(\alpha-\hat{\alpha}) +(\beta-\hat{\beta})\bar{x}\right]^2
\end{aligned}
$$


Therefore, the posterior joint distribution of $\alpha, \beta, \sigma^2$ can be simplied as
$$ 
\begin{aligned}
p^*(\alpha, \beta,\sigma^2 ~|~y_1,\cdots, y_n) \propto & \frac{1}{(\sigma^2)^{(n+2)/2}}\exp\left(-\frac{\sum_i(y_i - \alpha - \beta x_i)^2}{2\sigma^2}\right) \\
= & \frac{1}{(\sigma^2)^{(n+2)/2}}\exp\left(-\frac{\text{SSE} + n(\alpha-\hat{\alpha}+(\beta-\hat{\beta})\bar{x})^2 + (\beta - \hat{\beta})^2\sum_i (x_i-\bar{x})^2}{2\sigma^2}\right)
\end{aligned}
$$

### Marginal Posterior Distribution of $\beta$

To get the marginal posterior distribution of $\beta$, we need to integrate out $\alpha$ and $\sigma^2$ from $p^*(\alpha, \beta, \sigma^2~|~y_1,\cdots,y_n)$:

$$
\begin{aligned}
p^*(\beta ~|~y_1,\cdots,y_n) = & \int_0^\infty \int_{-\infty}^\infty p^*(\alpha, \beta, \sigma^2~|~y_1,\cdots, y_n)\, d\alpha\, d\sigma^2 \\
= & \int_0^\infty \left(\int_{-\infty}^\infty \frac{1}{(\sigma^2)^{(n+2)/2}}\exp\left(-\frac{\text{SSE} + n(\alpha-\hat{\alpha}+(\beta-\hat{\beta})\bar{x})^2+(\beta-\hat{\beta})\sum_i(x_i-\bar{x})^2}{2\sigma^2}\right)\, d\alpha\right)\, d\sigma^2\\
= & \int_0^\infty p^*(\beta, \sigma^2~|~y_1,\cdots, y_n)\, d\sigma^2
\end{aligned}
$$

We first calculate the inside integral, which gives us the joint posterior distribution of $\beta$ and $\sigma^2$
$$
\begin{aligned}
& p^*(\beta, \sigma^2~|~y_1,\cdots,y_n) \\
= & \int_{-\infty}^\infty \frac{1}{(\sigma^2)^{(n+2)/2}}\exp\left(-\frac{\text{SSE}+n(\alpha-\hat{\alpha}+(\beta-\hat{\beta})\bar{x})^2+(\beta-\hat{\beta})^2\sum_i(x_i-\bar{x})^2}{2\sigma^2}\right)\, d\alpha\\
= & \int_{-\infty}^\infty \frac{1}{(\sigma^2)^{(n+2)/2}}\exp\left(-\frac{\text{SSE}+(\beta-\hat{\beta})^2\sum_i(x_i-\bar{x})^2}{2\sigma^2}\right) \exp\left(-\frac{n(\alpha-\hat{\alpha}+(\beta-\hat{\beta})\bar{x})^2}{2\sigma^2}\right)\, d\alpha \\
= & \frac{1}{(\sigma^2)^{(n+2)/2}}\exp\left(-\frac{\text{SSE}+(\beta-\hat{\beta})^2\sum_i(x_i-\bar{x})^2}{2\sigma^2}\right) \int_{-\infty}^\infty \exp\left(-\frac{n(\alpha-\hat{\alpha}+(\beta-\hat{\beta})\bar{x})^2}{2\sigma^2}\right)\, d\alpha
\end{aligned}
$$


Here, 
$$ \exp\left(-\frac{n(\alpha-\hat{\alpha}+(\beta - \hat{\beta})\bar{x})^2}{2\sigma^2}\right) $$
can be viewed as part of a normal distribution of $\alpha$, with mean $\hat{\alpha}-(\beta-\hat{\beta})\bar{x}$, and variance $\sigma^2/n$. Therefore, the integral from the last line above is proportional to $\sqrt{\sigma^2/n}$. We get

$$
\begin{aligned}
p^*(\beta, \sigma^2~|~y_1,\cdots,y_n) 
\propto & \frac{1}{(\sigma^2)^{(n+2)/2}}\exp\left(-\frac{\text{SSE}+(\beta-\hat{\beta})^2\sum_i(x_i-\bar{x})^2}{2\sigma^2}\right) \times \sqrt{\frac{\sigma^2}{n}}\\
\propto & \frac{1}{(\sigma^2)^{(n+1)/2}}\exp\left(-\frac{\text{SSE}+(\beta-\hat{\beta})^2\sum_i (x_i-\bar{x})^2}{2\sigma^2}\right)
\end{aligned}
$$

We then integrate $\sigma^2$ out to get the marginal distribution of $\beta$. Here we first perform change of variable and set $\sigma^2 = \frac{1}{\phi}$. Then the integral becomes
$$
\begin{aligned}
p^*(\beta~|~y_1,\cdots, y_n) \propto & \int_0^\infty \frac{1}{(\sigma^2)^{(n+1)/2}}\exp\left(-\frac{\text{SSE} + (\beta-\hat{\beta})^2\sum_i(x_i-\bar{x})^2}{2\sigma^2}\right)\, d\sigma^2 \\
\propto & \int_0^\infty \phi^{\frac{n-3}{2}}\exp\left(-\frac{\text{SSE}+(\beta-\hat{\beta})^2\sum_i(x_i-\bar{x})^2}{2}\phi\right)\, d\phi\\
\propto & \left(\frac{\text{SSE}+(\beta-\hat{\beta})^2\sum_i(x_i-\bar{x})^2}{2}\right)^{-\frac{(n-2)+1}{2}}\int_0^\infty s^{\frac{n-3}{2}}e^{-s}\, ds
\end{aligned}
$$

Here we use another change of variable by setting $\displaystyle s=  \frac{\text{SSE}+(\beta-\hat{\beta})^2\sum_i(x_i-\bar{x})^2}{2}\phi$, and the fact that $\displaystyle \int_0^\infty s^{(n-3)/2}e^{-s}\, ds$ gives us the Gamma function $\Gamma(n-2)$, which is a constant.

We can rewrite the last line from above to obtain the marginal posterior distribution of $\beta$. This marginal distribution is the Student's $t$-distribution with degrees of freedom $n-2$, center $\hat{\beta}$, and scale parameter $\displaystyle \frac{\hat{\sigma}^2}{\sum_i(x_i-\bar{x})^2}$

$$ p^*(\beta~|~y_1,\cdots,y_n) \propto
 \left[1+\frac{1}{n-2}\frac{(\beta - \hat{\beta})^2}{\frac{\text{SSE}}{n-2}/(\sum_i (x_i-\bar{x})^2)}\right]^{-\frac{(n-2)+1}{2}} = \left[1 + \frac{1}{n-2}\frac{(\beta - \hat{\beta})^2}{\hat{\sigma}^2/(\sum_i (x_i-\bar{x})^2)}\right]^{-\frac{(n-2)+1}{2}},
$$

where $\displaystyle \frac{\hat{\sigma}^2}{\sum_i (x_i-\bar{x})^2}$ is exactly the square of the standard error of $\hat{\beta}$ from the frequentist OLS model. 

To summarize, under the reference prior, the marginal posterior distribution of the slope of the Bayesian simple linear regression follows the Student's $t$-distribution
$$ 
\beta ~|~y_1,\cdots, y_n \sim \St\left(n-2, \ \hat{\beta},\  \left(\text{se}_{\beta}\right)^2\right) 
$$

### Marginal Posterior Distribution of $\alpha$

A similar approach will lead us to the marginal distribution of $\alpha$. We again start from the joint posterior distribution
$$ p^*(\alpha, \beta, \sigma^2~|~y_1,\cdots,y_n) \propto \frac{1}{(\sigma^2)^{(n+2)/2}}\exp\left(-\frac{\text{SSE} + n(\alpha-\hat{\alpha}-(\beta-\hat{\beta})\bar{x})^2 + (\beta - \hat{\beta})^2\sum_i (x_i-\bar{x})^2}{2\sigma^2}\right) $$

This time we integrate $\beta$ and $\sigma^2$ out to get the marginal posterior distribution of $\alpha$. We first compute the integral
$$
\begin{aligned}
p^*(\alpha, \sigma^2~|~y_1,\cdots, y_n) = & \int_{-\infty}^\infty p^*(\alpha, \beta, \sigma^2~|~y_1,\cdots, y_n)\, d\beta\\
= & \int_{-\infty}^\infty \frac{1}{(\sigma^2)^{(n+2)/2}}\exp\left(-\frac{\text{SSE} + n(\alpha-\hat{\alpha}+(\beta-\hat{\beta})\bar{x})^2 + (\beta - \hat{\beta})^2\sum_i (x_i-\bar{x})^2}{2\sigma^2}\right)\, d\beta 
\end{aligned}
$$

Here we group the terms with $\beta-\hat{\beta}$ together, then complete the square so that we can treat is as part of a normal distribution function to simplify the integral
$$
\begin{aligned}
& n(\alpha-\hat{\alpha}+(\beta-\hat{\beta})\bar{x})^2+(\beta-\hat{\beta})^2\sum_i(x_i-\bar{x})^2 \\
= & (\beta-\hat{\beta})^2\left(\sum_i (x_i-\bar{x})^2 + n\bar{x}^2\right) + 2n\bar{x}(\alpha-\hat{\alpha})(\beta-\hat{\beta}) + n(\alpha-\hat{\alpha})^2 \\
= & \left(\sum_i (x_i-\bar{x})^2 + n\bar{x}^2\right)\left[(\beta-\hat{\beta})+\frac{n\bar{x}(\alpha-\hat{\alpha})}{\sum_i(x_i-\bar{x})^2+n\bar{x}^2}\right]^2+ n(\alpha-\hat{\alpha})^2\left[\frac{\sum_i(x_i-\bar{x})^2}{\sum_i (x_i-\bar{x})^2+n\bar{x}^2}\right]\\
= & \left(\sum_i (x_i-\bar{x})^2 + n\bar{x}^2\right)\left[(\beta-\hat{\beta})+\frac{n\bar{x}(\alpha-\hat{\alpha})}{\sum_i(x_i-\bar{x})^2+n\bar{x}^2}\right]^2+\frac{(\alpha-\hat{\alpha})^2}{\frac{1}{n}+\frac{\bar{x}^2}{\sum_i (x_i-\bar{x})^2}}
\end{aligned}
$$

When integrating, we can then view 
$$ \exp\left(-\frac{\sum_i (x_i-\bar{x})^2+n\bar{x}^2}{2\sigma^2}\left(\beta-\hat{\beta}+\frac{n\bar{x}(\alpha-\hat{\alpha})}{\sum_i (x_i-\bar{x})^2+n\bar{x}^2}\right)^2\right) $$
as part of a normal distribution function, and get
$$
\begin{aligned}
& p^*(\alpha, \sigma^2~|~y_1,\cdots,y_n) \\
\propto & \frac{1}{(\sigma^2)^{(n+2)/2}}\exp\left(-\frac{\text{SSE}+(\alpha-\hat{\alpha})^2/(\frac{1}{n}+\frac{\bar{x}^2}{\sum_i (x_i-\bar{x})^2})}{2\sigma^2}\right)\\
& \times\int_{-\infty}^\infty \exp\left(-\frac{\sum_i (x_i-\bar{x})^2+n\bar{x}^2}{2\sigma^2}\left(\beta-\hat{\beta}+\frac{n\bar{x}(\alpha-\hat{\alpha})}{\sum_i (x_i-\bar{x})^2+n\bar{x}^2}\right)^2\right)\, d\beta \\
\propto & \frac{1}{(\sigma^2)^{(n+1)/2}}\exp\left(-\frac{\text{SSE}+(\alpha-\hat{\alpha})^2/(\frac{1}{n}+\frac{\bar{x}^2}{\sum_i (x_i-\bar{x})^2})}{2\sigma^2}\right)
\end{aligned}
$$

To get the marginal posterior distribution of $\alpha$, we again integrate $\sigma^2$ out. using the same change of variable $\displaystyle \sigma^2=\frac{1}{\phi}$, and $s=\displaystyle \frac{\text{SSE}+(\alpha-\hat{\alpha})^2/(\frac{1}{n}+\frac{\bar{x}^2}{\sum_i (x_i-\bar{x})^2})}{2}\phi$.

$$
\begin{aligned}
& p^*(\alpha~|~y_1,\cdots,y_n) \\
= & \int_0^\infty p^*(\alpha, \sigma^2~|~y_1,\cdots, y_n)\, d\sigma^2 \\
\propto & \int_0^\infty \phi^{(n-3)/2}\exp\left(-\frac{\text{SSE}+(\alpha-\hat{\alpha})^2/(\frac{1}{n}+\frac{\bar{x}^2}{\sum_i (x_i-\bar{x})^2})}{2}\phi\right)\, d\phi\\
\propto & \left(\text{SSE}+(\alpha-\hat{\alpha})^2/(\frac{1}{n}+\frac{\bar{x}^2}{\sum_i (x_i-\bar{x})^2})\right)^{-\frac{(n-2)+1}{2}}\int_0^\infty s^{(n-3)/2}e^{-s}\, ds\\
\propto & \left[1+\frac{1}{n-2}\frac{(\alpha-\hat{\alpha})^2}{\frac{\text{SSE}}{n-2}\left(\frac{1}{n}+\frac{\bar{x}^2}{\sum_i (x_i-\bar{x})^2}\right)}\right]^{-\frac{(n-2)+1}{2}} = \left[1 + \frac{1}{n-2}\left(\frac{\alpha-\hat{\alpha}}{\text{se}_{\alpha}}\right)^2\right]^{-\frac{(n-2)+1}{2}}
\end{aligned}
$$

In the last line, we use the same trick as we did for $\beta$ to derive the form of the Student's $t$-distribution. This shows that the marginal posterior distribution of $\alpha$ also follows a Student's $t$-distribution, with $n-2$ degrees of freedom. Its center is $\hat{\alpha}$, the estimate of 
$\alpha$ in the frequentist OLS estimate, and its scale parameter is $\displaystyle \hat{\sigma}^2\left(\frac{1}{n}+\frac{\bar{x}^2}{\sum_i (x_i-\bar{x})^2}\right)$, which is the square of the standard error of $\hat{\alpha}$.

### Marginal Posterior Distribution of $\sigma^2$

To show that the marginal posterior distribution of $\sigma^2$ follows the inverse Gamma distribution, we only need to show the precision $\displaystyle \phi = \frac{1}{\sigma^2}$ follows a Gamma distribution. 

We have shown in Week 3 that taking the prior distribution of $\sigma^2$ proportional to $\displaystyle \frac{1}{\sigma^2}$ is equivalent to taking the prior distribution of $\phi$ proportional to $\displaystyle \frac{1}{\phi}$
$$ p(\sigma^2) \propto \frac{1}{\sigma^2}\qquad \Longrightarrow \qquad p(\phi)\propto \frac{1}{\phi} $$

Therefore, under the parameters $\alpha$, $\beta$, and the precision $\phi$, we have the joint prior distribution as
$$ p(\alpha, \beta, \phi) \propto \frac{1}{\phi} $$
and the joint posterior distribution as
$$ 
p^*(\alpha, \beta, \phi~|~y_1,\cdots,y_n) \propto \phi^{\frac{n}{2}-1}\exp\left(-\frac{\sum_i(y_i-\alpha-\beta x_i)}{2}\phi\right) 
$$


Using the partial results we have calculated previously, we get
$$
p^*(\beta, \phi~|~y_1,\cdots,y_n) = \int_{-\infty}^\infty p^*(\alpha, \beta, \phi~|~y_1,\cdots,y_n)\, d\alpha \propto \phi^{\frac{n-3}{2}}\exp\left(-\frac{\text{SSE}+(\beta-\hat{\beta})^2\sum_i (x_i-\bar{x})^2}{2}\phi\right) 
$$

Intergrating over $\beta$, we finally have
$$
\begin{aligned}
& p^*(\phi~|~y_1,\cdots,y_n) \\
\propto & \int_{-\infty}^\infty \phi^{\frac{n-3}{2}}\exp\left(-\frac{\text{SSE}+(\beta-\hat{\beta})^2\sum_i (x_i-\bar{x})^2}{2}\phi\right)\, d\beta\\
= & \phi^{\frac{n-3}{2}}\exp\left(-\frac{\text{SSE}}{2}\phi\right)\int_{-\infty}^\infty \exp\left(-\frac{(\beta-\hat{\beta})^2\sum_i(x_i-\bar{x})^2}{2}\phi\right)\, d\beta\\
\propto & \phi^{\frac{n-4}{2}}\exp\left(-\frac{\text{SSE}}{2}\phi\right) = \phi^{\frac{n-2}{2}-1}\exp\left(-\frac{\text{SSE}}{2}\phi\right).
\end{aligned}
$$

This is a Gamma distribution with shape parameter $\displaystyle \frac{n-2}{2}$ and rate parameter $\displaystyle \frac{\text{SSE}}{2}$. Therefore, the updated $\sigma^2$ follows the inverse Gamma distribution
$$ \phi = 1/\sigma^2~|~y_1,\cdots,y_n \sim \Ga\left(\frac{n-2}{2}, \frac{\text{SSE}}{2}\right). $$
That is,
$$ p(\phi~|~\text{data}) \propto \phi^{\frac{n-2}{2}-1}\exp\left(-\frac{\text{SSE}}{2}\phi\right). $$

### Joint Normal-Gamma Posterior Distributions

Recall that the joint posterior distribution of $\beta$ and $\sigma^2$ is
$$ p^*(\beta, \sigma^2~|~\text{data}) \propto \frac{1}{\sigma^{n+1}}\exp\left(-\frac{\text{SSE}+(\beta-\hat{\beta})^2\sum_i(x_i-\bar{x})^2}{2\sigma^2}\right). $$

If we rewrite this using precision $\phi=1/\sigma^2$, we get the joint posterior distribution of $\beta$ and $\phi$ to be
$$ p^*(\beta, \phi~|~\text{data}) \propto \phi^{\frac{n-2}{2}}\exp\left(-\frac{\phi}{2}\left(\text{SSE}+(\beta-\hat{\beta})^2\sum_i (x_i-\bar{x})^2\right)\right). $$
This joint posterior distribution can be viewed as the product of the posterior distribution of $\beta$ conditioning on $\phi$ and the posterior distribution of $\phi$,
$$ \pi^*(\beta~|~\phi,\text{data}) \times \pi^*(\phi~|~\text{data}) \propto \left[\phi\exp\left(-\frac{\phi}{2}(\beta-\hat{\beta})^2\sum_i (x_i-\bar{x})^2\right)\right] \times \left[\phi^{\frac{n-2}{2}-1}\exp\left(-\frac{\text{SSE}}{2}\phi\right)\right]. $$
The first term in the product is exactly the Normal distribution with mean $\hat{\beta}$ and standard deviation $\displaystyle \frac{\sigma^2}{\sum_i(x_i-\bar{x})^2} = \frac{\sigma^2}{\text{S}_{xx}}$

$$ \beta ~|~\sigma^2,\ \text{data}~ \sim ~ \No\left(\hat{\beta},\ \frac{\sigma^2}{\text{S}_{xx}}\right). $$
The second term, is the Gamma distribution of the precision $\phi$, or the inverse Gamma distribution of the variance $\sigma^2$
$$ 1/\sigma^2~|~\text{data}~\sim~\Ga\left(\frac{n-2}{2},\frac{\text{SSE}}{2}\right).$$

This means, the join posterior distribution of $\beta$ and $\sigma^2$, under the reference prior, is a Normal-Gamma distribution. Similarly, the joint posterior distribution of $\alpha$ and $\sigma^2$ is also a Normal-Gamma distribution.
$$ \alpha~|~\sigma^2, \text{data} ~\sim~\No\left(\hat{\alpha}, \sigma^2\left(\frac{1}{n}+\frac{\bar{x}^2}{\text{S}_{xx}}\right)\right),\qquad \qquad 1/\sigma^2~|~\text{data}~\sim~ \Ga\left(\frac{n-2}{2}, \frac{\text{SSE}}{2}\right). $$

In fact, when we impose the bivariate normal distribution on $\bv = (\alpha, \beta)^T$, and inverse Gamma distribution on $\sigma^2$,  as we have discussed in Section \@ref(sec:informative-prior), the joint posterior distribution of $\bv$ and $\sigma^2$ is a Normal-Gamma distribution. Since the reference prior is just the limiting case of this informative prior, it is not surprising that we will also get the limiting case Normal-Gamma distribution for $\alpha$, $\beta$, and $\sigma^2$.