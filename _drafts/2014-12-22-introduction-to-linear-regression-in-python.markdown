---
layout: post
title:  "An Introduction To Linear Regression Using Python"
date:   2014-12-22
---

Linear regression is a tool every statistian / data scientist / whatever should have in their tool kit. Although it uses some very simple assumptions (the model you are building data on contains a linear relationship), it can a very useful tool for both classification/prediction and explainting the strength of the relationship between the regressors and the response variable.

### The Assumptions

One of the most important things you need to understand before you use **any** model are what are the assumptions the model is making.

1. Linearity

1. Constant Variance

1. Independence of Errors

1. No Multicollinearity

### A Real World Example

{% highlight python %}

import pandas as pd
import numpy as np
import scipy as sp
import statsmodels.api as sm
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
%matplotlib inline


""" lets start the analysis! """
data = pd.read_csv("data/Advertising.csv")

{% endhighlight %}


### Summary Statistic Definitions

{% highlight python %}

In [30]: results.summary()
Out[30]:
<class 'statsmodels.iolib.summary.Summary'>
"""
                            OLS Regression Results
==============================================================================
Dep. Variable:                      y   R-squared:                       0.612
Model:                            OLS   Adj. R-squared:                  0.610
Method:                 Least Squares   F-statistic:                     312.1
Date:                Mon, 22 Dec 2014   Prob (F-statistic):           1.47e-42
Time:                        12:46:33   Log-Likelihood:                -519.05
No. Observations:                 200   AIC:                             1042.
Df Residuals:                     198   BIC:                             1049.
Df Model:                           1
Covariance Type:            nonrobust
==============================================================================
                 coef    std err          t      P>|t|      [95.0% Conf. Int.]
------------------------------------------------------------------------------
const          7.0326      0.458     15.360      0.000         6.130     7.935
x1             0.0475      0.003     17.668      0.000         0.042     0.053
==============================================================================
Omnibus:                        0.531   Durbin-Watson:                   1.935
Prob(Omnibus):                  0.767   Jarque-Bera (JB):                0.669
Skew:                          -0.089   Prob(JB):                        0.716
Kurtosis:                       2.779   Cond. No.                         338.
==============================================================================

Warnings:
[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
"""

{% endhighlight %}
