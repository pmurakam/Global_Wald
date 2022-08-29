# Global_Wald
A function to test and estimate arbitrary linear combinations of model coefficients.

Description:  
A function to test and estimate arbitrary linear combinations of model coefficients.  

Usage:  
```
global.wald(mod,type="test",vcovar,coefs=coef(mod),C,
            h=ifelse(is.matrix(C)|is.data.frame(C), matrix(0,nrow(C),1), 0),
            alpha=.05, dig=2)

Arguments:
mod    - fitted model object
type   - "test" or "estimate". "test" by default.
vcovar - variance covariance matrix of the coefficient estimates
coefs  - coefficient estimates
C      - the matrix whose product with coefs you are testing equality with h
h      - an outcome scalar or vector. All 0's by default.
alpha  - significance level. .05 by default.
dig    - number of digits to report for the values in the output. 2 by default.
```

See also:  
waldtest and coeftest in the lmtest package, and linear.hypothesis in the car package. 
Some of these do not handle all types of fitted model objects (e.g., I don't believe 
linear.hypothesis handles fitted gee model objects) and I don't believe any of them do 
estimation (only testing). This works on any fitted model object, as long as it contains 
the estimated variance-covariance matrix for the coefficient estimates, and it can do both 
testing and estimation.

Examples:
```
y <- rnorm(100,2,3)
b <- rbinom(100,2,.5)
test <- lm(y~factor(b))
C <- c(0,1,-1)
match.this <- linear.hypothesis(test,C,test="Chisq")
test <- global.wald(test,vcovar=vcov(test),type="test",C=c(0,1,-1))
#'test' should match 'match.this', and it does.

#Example to test the hypothesis that beta_age=0 AND
#beta_smoke - beta_alcohol =0 AND beta_age:smoke=0 in the following
#gee model (where I generated an extra covariate of indicators
#for alcohol consumption just to make the model somewhat larger):
library(geepack)
data(ohio)
alcohol <- round(runif(nrow(ohio)))
fit <- geese(resp ~ age + smoke + alcohol+ age:smoke, id=id,
family=binomial, corstr="exchangeable",data=ohio)

A <- rbind(c(0,1,0, 0,0),
c(0,0,1,-1,0),
c(0,0,0, 0,1))
global.wald(fit,type="test",coefs=fit$beta,vcovar=fit$vbeta,C=A)

#To estimate the difference in the smoking and drinking coefficients:
A <- c(0,0,1,-1,0)
global.wald(fit,type="estimate",coefs=fit$beta,vcovar=fit$vbeta,C=A)
```
