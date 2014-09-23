---
layout: post
title:  "How Not To Lie With Statistics"
date:   2014-09-23
categories: statistics
---

I just finished reading [How Not to Lie with Statistics: Avoiding 
Common Mistakes in Quantitative 
Political Science](http://gking.harvard.edu/files/mist.pdf) By Gary King, and found a number of really great pieces of advice in it. I decided to summarize the high-level sections of the paper.

#### Regression and Residuals (ROR):

* Omitting variable bias: using least square to estimate population parameters B1 and B2, instead of running a standard multivariate regression

$$ y = X1*B_1 + X2*B_2 $$

You use ROR as so:

$$ y = X1*B_1 + b_1 $$ 

$$ b1 = X2*B_2 + e $$

* The mistake is thinking that B2 in (3) is the same as B2 in (1) on, since we controlled for X1 in equation (3). Unfortunately, B1 in equation two is a biased estimate of B1 since the equation does not control for X2. Since the residuals e1 are biases on X1, B2 is also biased. **Except for two very special cases, the ROR estimate is not he same as the OLS estimate and by itself has no useful interpretation.**

#### Race Of Variables:

* Only when explanatory variables are on meaningfully common units of measurement is there a chance of comparison. If there is no common unit of measurement, there is no chance of meaningful comparison.
* Standardized coefficients are often difficult to interpret
* Standardized coefficients do not add any information that will help compare effects from different exploratory variables
* may add serious misleading information

#### The Correlation Problem

* All of the problems attributed to standardized coefficients apply to correlation coefficients. There is nothing in statistical theory that attributes causal assumptions to regression coefficients; regression is simply a sample estimate of a (population) conditional expected value. The assumptions are about the conditional probability distribution, not about the influence of x on y. Nothing can or should stop an applied researcher from stating that x causes y, but it is crucial to understand that statistical analysis does not usually provide evidence with which to evaluate this assertion


#### Coefficient of Determination

* R Squared is a measure of the spread of points around a regression line, and it is a poor measure of even that there is no statistical theory behind the R Squared statistic. There is at least one direct use and several indirect uses of R Squared . You can directly apply and evaluate R Squared when comparing two equations with different explanatory variables and identical dependent variables. The measure is, in this case, a convenient goodness-of-fit statistic, providing a rough way to assess model specification and sensitivity.

#### Dichotomous Independent Variables

* Consider a case where there are two populations with means p1and p2,from which random samples sizes nl and n2 are taken. The populations could be male and female, agree and disagree, Republican and Democrat or anything that could be represented by a meaningful explanatory dichotomous variable. A common problem is to test the hypothesis that the means are equal ( p I- p2 = 0) To do this you can use

	1. Difference in means test
	1. Anova
	1. Linear Regression

* ANOVA,regression,and the difference of means test are all special cases of the general linear model. The assumptions required of one are required of the others as well. If there are dichotomous dependent variables, none of the techniques are appropriate. If there is a dichotomous independent variable, any one of the three will do.

#### Dichotomous Dependent Variables

* Do not use dichotomous dependent variables in regression, ANOVA, or other linear models. Doing this can lead to probabilities greater than one or less than zero (heteroskedasticity), inefficient estimates, bias standard errors, and useless test statistics.
* A linear model applied to this data is the wrong function form, and conceptually incorrect
* The solution is usually to model the relationship with a logic or pro bit (or some other non-linear model)
* In factor analysis, there are many observed variables from which the goal is to derive the underlying (unobserved) factors. A common mistake is to view the observed variables as causing the factor. This is incorrect. The correct model has observable dependent variables as functions of the underlying and unobservable factors. 
* Variables like race, gender, and age should never be observed variables in a political scientist's factor analysis
* Since most factor analysis models are linear, they can no more handle dichotomous dependent (observed) variables than can regression analysis models.