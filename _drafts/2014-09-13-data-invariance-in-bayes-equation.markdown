---
layout: post
title:  "Data Order Invariance And Bayes Equation"
date:   2014-09-13
---

One interesting question that may come up when you practicing Bayesian Inference is the following:

Since you can use Bayes equation to update your belief (posterior) each time a **new** data point comes in, does the order that the data comes in affect the posterior probabililty distribution?

The short answer is, it might, but for most problems you are probably dealing with, especially if you are just starting out, it most likley does not. The answer depends on the definition of the likelihood function:  

$$ P(D|\theta) $$

Starting out with Bayes equation (model=theta,data=D)

$$ P(\theta|D) = \frac{P(D|\theta)P(\theta) }{ P(D)} $$ 

Now let's introduce and additional data point D prime and refactor the equatioN:

$$ P(\theta|D,D') = \frac{P(D|\theta,D')P(\theta,D') }{ P(D,D')} $$ 

