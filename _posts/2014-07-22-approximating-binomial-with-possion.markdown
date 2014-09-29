---
layout: post
title:  "Approximating a Binomial Random Variable Using A Poisson Random Variable" 
date:   2014-7-22
---

A random variable X, taking on values 0,1,2,... is said to be a Poisson random variable with parameter lambda if for some lambda >0,

$$ 
\begin{align}
p(i) = P[X=i] = e^{-\lambda}(\frac{\lambda^i}{i!}) \quad\quad \text{ for i=0,1,2 ...}

\end{align}
$$


A commonly known (or maybe not commonly known, depending on who you are) property of a Poisson random variable is that it can be used to approximate a Binomial random variable when n is large and p is small. The following is the derivation of why this is mathematical true:

First, lets set: 

$$ 
\begin{align}
\lambda = np
\end{align}
$$

