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


$$ 
\begin{align}
P[X=i] = \frac{n!}{(n-i)!i!}p^i(1-p)^{n-i}
\end{align}
$$

$$ 
\begin{align}
P[X=i] = \frac{n!}{(n-i)!i!}\frac{\lambda}{n}^i(1-\frac{\lambda}{n})^{n-i}

\end{align}
$$

$$ 
\begin{align}
P[X=i] = \frac{n!}{(n-i)!i!}\frac{\lambda^i}{i!}\frac{ (1-\frac{\lambda}{n})^n }{ (1-\frac{\lambda}{n})^i }

\end{align}
$$

Now, for a large n and small p


$$ 
\begin{align}
 \frac{n!}{(n-i)!i!} \quad\quad \text{ converges to 1}
\end{align}
$$

$$ 
\begin{align}
 (1-\frac{\lambda}{n})^n \quad\quad \text{ converges to } \quad\quad e^{-\lambda}
\end{align}
$$


$$ 
\begin{align}
 (1-\frac{\lambda}{n})^i \quad\quad \text{ converges to } \quad\quad 1
\end{align}
$$

Hence for large n and small p

$$ 
\begin{align}
P[X=i] = e^{-\lambda}\frac{\lambda^i}{i!}
\end{align}
$$