################################################################################
## Copyright (C) 2010 Peter Murakami <peter.murakami@gmail.com>
## 
## This program is free software: you can redistribute it and/or modify
## it under the terms of the GNU General Public License as published by
## the Free Software Foundation, either version 3 of the License, or
## (at your option) any later version.
## 
## This program is distributed in the hope that it will be useful,
## but WITHOUT ANY WARRANTY; without even the implied warranty of
## MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
## GNU General Public License for more details.
## 
## You should have received a copy of the GNU General Public License
## along with this program.  If not, see <http://www.gnu.org/licenses/>.
################################################################################

global.wald <- function(mod,type="test",vcovar,coefs=coef(mod),C,
                        h=ifelse(is.matrix(C)|is.data.frame(C),
                        matrix(0,dim(C)[1],1),0), alpha=.05, dig=2 ){
    if(type=="estimate" & !is.vector(C)){ stop("C must be a vector") }
    if(is.vector(C)){ C <- t(C) }
    if(inherits(vcovar,"dpoMatrix")) vcovar = as.matrix(vcovar)
    
    varcov <- C %*% vcovar %*% t(C)
    CB <- C %*% coefs 
    if(type=="test"){
        Chisq <- t(CB-h) %*% solve(varcov) %*% (CB-h) 
        pvalue=round(pchisq(Chisq,dim(C)[1],lower.tail=FALSE), dig)
        pvalue=as.numeric(pvalue)
        return(pvalue)
    } else if(type=="estimate"){
        zq <- qnorm(1-alpha/2)
        CI <- CB + c(-zq,zq)*sqrt(varcov)
        CI <- round(c(CI[1], CB, CI[2]), dig)
        names(CI) <- c("lower", "estimate", "upper")
        return(CI)
    }
}
