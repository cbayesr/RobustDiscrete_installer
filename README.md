# RobustDiscrete package

*Cristian L. Bayes<sup>1</sup>, Jorge L. Bazán <sup>2</sup> and Luis Valdivieso<sup>1</sup>*

<sup>1</sup>Departamento de Ciencias, Pontificia Universidad Católica del Perú, Lima, Perú.
<sup>2</sup>Department of Applied Mathematics and Statistics, University of São Paulo, São Carlos, Brazil.

*Corresponding author:* Cristian L. Bayes, cbayes@pucp.edu.pe

## Requirements

To run this package, it is needed to previously install Rcpp, gamlss, gamlss.dist and numDeriv packages. 

## Installation

Windows:
```r
install.packages("https://github.com/cbayesr/RobustDiscrete_installer/raw/main/RobustDiscrete_0.1.0.zip")
```
This R code was tested on Windows 10, R version 4.4.1, and RStudio version 2024.04.2.

macOS:
```r
install.packages("https://github.com/cbayesr/RobustDiscrete_installer/raw/main/RobustDiscrete_0.1.0.tar.gz",repos=NULL,type="source")
```
This R code was tested on macOS 14.6, R version 4.4.1, and RStudio version 2024.04.2.

## Citation

To cite RobustDiscrete in publications use:

Bayes, C. L., Bazán, J. L., & Valdivieso, L. (2024). A robust regression model for bounded count health data. *Statistical Methods in Medical Research*. Advance online publication. [https://doi.org/10.1177/09622802241259178](https://doi.org/10.1177/09622802241259178)

## Example

From Appendix C of Bayes, Bazán, and Valdvieso (2024). Estimation of model M5 had a run time of 212 seconds on an Intel Core i-7 processor with 2.80 GHz and 16.0 GB RAM due to the use of numerical integration to evaluate the PDF of the B2B distribution.

```r
library(gamlss)
library(numDeriv)
library(RobustDiscrete)

# First, we estimate model M2 using gamlss, to get initial values
# for model M5
M2<-gamlss(y~ward+loglos+year,
           sigma.formula=~year+ward,
           family=BB, data=aep)

## GAMLSS-RS iteration 1: Global Deviance = 4490.361 
## GAMLSS-RS iteration 2: Global Deviance = 4483.13 
## GAMLSS-RS iteration 3: Global Deviance = 4483.021 
## GAMLSS-RS iteration 4: Global Deviance = 4483.02 
## GAMLSS-RS iteration 5: Global Deviance = 4483.02 

# To estimate model M5, we use the new family B2B of
# RobustDiscrete package.
# Notice that we have to transform the initials values for sigma
# from model M2 due the different parametrization that use the
# gamlss package for BB model.
M5 <-gamlss(y~ward+loglos+year,
            sigma.formula=~year+ward,
            nu.formula = ~year,
            family=B2B, data=aep,
            mu.start = M2$mu.fv,
            sigma.start = M2$sigma.fv/(1+M2$sigma.fv))

## GAMLSS-RS iteration 1: Global Deviance = 4450.638 
## GAMLSS-RS iteration 2: Global Deviance = 4449.373 
## GAMLSS-RS iteration 3: Global Deviance = 4449.152 
## GAMLSS-RS iteration 4: Global Deviance = 4449.116 
## GAMLSS-RS iteration 5: Global Deviance = 4449.11 
## GAMLSS-RS iteration 6: Global Deviance = 4449.109 

summary(M5)

## ******************************************************************
## Family:  c("BB", "Beta-2-Binomial") 
## 
## Call:  gamlss(formula = y ~ ward + loglos + year, sigma.formula = ~year +  
##     ward, nu.formula = ~year, family = B2B, data = aep, mu.start = M2$mu.fv,  
##     sigma.start = M2$sigma.fv/(1 + M2$sigma.fv)) 
## 
## Fitting method: RS() 
## 
## ------------------------------------------------------------------
## Mu link function:  logit
## Mu Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -0.98203    0.07976 -12.312  < 2e-16 ***
## ward2       -0.52860    0.09046  -5.843 6.38e-09 ***
## ward3       -0.73761    0.27748  -2.658  0.00795 ** 
## loglos       0.55101    0.05554   9.920  < 2e-16 ***
## year90       0.19811    0.08991   2.203  0.02774 *  
## ---
## Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
## 
## ------------------------------------------------------------------
## Sigma link function:  logit
## Sigma Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  0.29456    0.09492   3.103 0.001954 ** 
## year90      -0.45151    0.12546  -3.599 0.000331 ***
## ward2       -0.69533    0.12588  -5.524 3.96e-08 ***
## ward3       -0.82951    0.61858  -1.341 0.180142    
## ---
## Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
## 
## ------------------------------------------------------------------
## Nu link function:  logit 
## Nu Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  -1.9563     0.5742  -3.407 0.000676 ***
## year90        1.2522     0.6365   1.968 0.049323 *  
## ---
## Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
## 
## ------------------------------------------------------------------
## No. of observations in the fit:  1383 
## Degrees of Freedom for the fit:  11
##       Residual Deg. of Freedom:  1372 
##                       at cycle:  6 
##  
## Global Deviance:     4449.109 
##             AIC:     4471.109 
##             SBC:     4528.661 
## ******************************************************************
```


