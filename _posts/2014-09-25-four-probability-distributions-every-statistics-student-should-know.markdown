---
layout: post
title:  "Four Probability Distributions Every Statistics Student Should Know"
date:   2014-09-25
categories: statistics
---

If you open up a classical statistics book, you will see endless amounts of information about hypothesis testing, confidence intervals, p-values, etc. Although those topics are important, I have noticed that some people walk away not having a deep understanding of probability distributions (beyond the normal distribution of course). Here are four probability distributions every statistics students should know:

#### Gaussian distribution

![Example of munin graphs]({{ baseurl.url }}/images/posts/normal.png)


The Gaussian distribution, also known as the normal distribution. It describes the distribution of
sum of independent, identically distributed random variables.

Given

$$ 
\begin{align}
X_1, X_2, X_3, .... ,X_m 
\end{align}
$$

And

$$ 
\begin{align}
T = X_1 + X_2 + X_3 + .... + X_m 
\end{align}
$$

The **T** is said to be normally distributed. The average of this sample:

$$ 
\begin{align}
mu = (X_1 + X_2 + X_3 + .... + X_m)/m
\end{align}
$$

Is also normally distributed, and this is actually more important because the Gaussian distribution describes the **distribution of average**.

#### Chi-Squared distribution

![x]({{ baseurl.url }}/images/posts/chisquared.png)

The Chi Squared distribution describes the distribution of **squared sums** of  independent, identically distributed random variables.

Given

$$ 
\begin{align}
X_1, X_2, X_3, .... ,X_m 
\end{align}
$$

And

$$ 
\begin{align}
T = X_1^2 + X_2^2 + X_3^2 + .... + X_m^2 
\end{align}
$$

The **T** will follow a chi-squared distribution. The reason this is important because the variance is a sum of squares, hence the chi-squared distribution is used to describe the distribution of variances.



#### The T-distribution

![x]({{ baseurl.url }}/images/posts/tdistribution.png)

The student t-distribution describes the ratio of a Gauassian random variable with a  Chi-Squared random variable. A t-distribution is basically a Gaussain distribution with fatter tails.


#### Fisher's F distribution

The F-distribution describes the distribution of the ratio of two chi-squared variables. The is useful if you want to compare two variances against eachother.[1] It is used heavily in Analysis of Variance  (ANVOA).




[1] [Data Analysis with Open Source Tools](http://www.amazon.com/Data-Analysis-Open-Source-Tools/dp/0596802358)



