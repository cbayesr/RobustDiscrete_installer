# RobustDiscrete package

*Cristian L. Bayes<sup>1</sup>, Jorge L. Bazán <sup>2,3</sup> and Luis Valdivieso<sup>1</sup>*

<sup>1</sup>Departamento de Ciencias, Pontificia Universidad Católica del Perú, Lima, Perú.
<sup>2</sup>Faculty of Mathematics, Pontificia Universidad Católica de Chile, Santiago, Chile
<sup>3</sup>Department of Applied Mathematics and Statistics, University of São Paulo, São Carlos, Brazil.

*Corresponding author:* Cristian L. Bayes, cbayes@pucp.edu.pe

## Description 

The RobustDiscrete package provides robust regression tools for modeling discrete response variables, supporting both bounded and unbounded counts, through the GAMLSS modeling framework for flexible parameter specification. The package implements the Beta-2-Binomial (B2B) model for bounded, and the Gamma-Negative-Binomial (GNB_k) model for unbounded count data. These new robust models extends the usual Beta-Binomial and Negative-Binomial models respectively, and are designed to handle extreme observations.

## Requirements

To run this package previously, install the Rcpp, gamlss, gamlss.dist, and numDeriv packages. 

## Installation

Windows:
```r
install.packages("https://github.com/cbayesr/RobustDiscrete_installer/raw/main/RobustDiscrete_0.1.0.zip")
```
This R code was tested on Windows 10, R version 4.5.2, and RStudio version 2025.09.2.

macOS:
```r
install.packages("https://github.com/cbayesr/RobustDiscrete_installer/raw/main/RobustDiscrete_0.1.0.tar.gz",repos=NULL,type="source")
```
This R code was tested on macOS 14.5, R version 4.5.2, and RStudio version 2025.09.2.

## Citation

To cite RobustDiscrete in publications, for bounded count data use:

Bayes, C. L., Bazán, J. L., & Valdivieso, L. (2024). A robust regression model for bounded count health data. *Statistical Methods in Medical Research*. **33(8)**:1392-1411. [https://doi.org/10.1177/09622802241259178](https://doi.org/10.1177/09622802241259178)

and for unbounded count data use:

Bayes, C. L., Bazán, J. L., & Valdivieso, L. (2025+). A robust regression model for count data in medical research. *Under review*. 

## Example for Gamma-Negative-Binomial model

Taken from Appendix 2 of Bayes, Bazán, and Valdivieso (2025). Estimation of model GNB0 took 100 seconds on an Intel Core i-7 processor with 2.80 GHz and 16.0 GB RAM, this is mainly due to the use of numerical integration in the GNB0 probability mass evaluation.

```r
library(gamlss)
library(numDeriv)
library(RobustDiscrete)

# Dataset
library(AER)
data(NMES1988)
nmes = NMES1988[,c("visits","hospital","health","chronic","gender",
                   "school","insurance")]
dat = nmes[nmes$health=="excellent",]

# First, we estimate model NB0 using gamlss, to get initial values
# for model GNB0
model = visits~sqrt(chronic)+ sqrt(hospital) + insurance + gender +school
fit.NB0.M1<-gamlss(model,
                   family=NB0,
                   data=dat,
                   control = gamlss.control(n.cyc = 2000))

# To estimate model GNB_0, we use the new family GNB0 of
# RobustDiscrete package.
system.time(
fit.GNB0.M1<-gamlss(model,
                    sigma.formula=~1,
                    nu.formula = ~1,
                    family=GNB0,
                    data=dat,
                    mu.start = fit.NB0.M1$mu.fv,
                    sigma.start = fit.NB0.M1$sigma.fv,
                    nu.start = rep(1,nrow(dat)),
                    control = gamlss.control(n.cyc = 2000)
))

## GAMLSS-RS iteration 1: Global Deviance = 1525.599 
## GAMLSS-RS iteration 2: Global Deviance = 1522.969 
## GAMLSS-RS iteration 3: Global Deviance = 1522.665 
## GAMLSS-RS iteration 4: Global Deviance = 1522.575 
## GAMLSS-RS iteration 5: Global Deviance = 1522.512 
## GAMLSS-RS iteration 6: Global Deviance = 1522.467 
## GAMLSS-RS iteration 7: Global Deviance = 1522.436 
## GAMLSS-RS iteration 8: Global Deviance = 1522.415 
## GAMLSS-RS iteration 9: Global Deviance = 1522.401 
## GAMLSS-RS iteration 10: Global Deviance = 1522.391 
## GAMLSS-RS iteration 11: Global Deviance = 1522.385 
## GAMLSS-RS iteration 12: Global Deviance = 1522.381 
## GAMLSS-RS iteration 13: Global Deviance = 1522.378 
## GAMLSS-RS iteration 14: Global Deviance = 1522.376 
## GAMLSS-RS iteration 15: Global Deviance = 1522.374 
## GAMLSS-RS iteration 16: Global Deviance = 1522.374

summary(fit.GNB0.M1)

## ******************************************************************
## Family:  c("NBF", "Negative-Binomial-Gamma") 
## 
## Call:  gamlss(formula = model, sigma.formula = ~1, nu.formula = ~1,  
##     family = GNB0, data = dat, mu.start = fit.NB0.M1$mu.fv, sigma.start = fit.NB0.M1$sigma.fv,  
##     nu.start = rep(1, nrow(dat)), control = gamlss.control(n.cyc = 2000)) 
## 
## 
## Fitting method: RS() 
## 
## ------------------------------------------------------------------
## Mu link function:  log
## Mu Coefficients:
##                 Estimate Std. Error t value Pr(>|t|)    
## (Intercept)    -0.005791   0.177684  -0.033   0.9740    
## sqrt(chronic)   0.390353   0.070480   5.538 6.15e-08 ***
## sqrt(hospital)  0.486773   0.103876   4.686 4.05e-06 ***
## insuranceyes    0.278416   0.141943   1.961   0.0506 .  
## gendermale     -0.015701   0.084798  -0.185   0.8532    
## school          0.054039   0.011464   4.714 3.56e-06 ***
## ---
## Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
## 
## ------------------------------------------------------------------
## Sigma link function:  log
## Sigma Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  2.33218    0.09492   24.57   <2e-16 ***
## ---
## Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
## 
## ------------------------------------------------------------------
## Nu link function:  log 
## Nu Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   5.0027     0.3422   14.62   <2e-16 ***
## ---
## Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
## 
## ------------------------------------------------------------------
## No. of observations in the fit:  343 
## Degrees of Freedom for the fit:  8
##       Residual Deg. of Freedom:  335 
##                       at cycle:  16 
##  
## Global Deviance:     1522.374 
##             AIC:     1538.374 
##             SBC:     1569.075 
## ******************************************************************
```

## Example for Beta-2-Binomial model

Taken from Appendix C of Bayes, Bazán, and Valdivieso (2024). Estimation of model M5 took 212 seconds on an Intel Core i-7 processor with 2.80 GHz and 16.0 GB RAM, this is mainly due to the use of numerical integration in the B2B probability mass evaluation.

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


