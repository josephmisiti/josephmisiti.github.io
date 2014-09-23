---
layout: post
title:  "The Chain Rule Of Probability"
date:   2014-09-23
categories: statistics
---

The chain rule of probability is a theory that allows one to calculate any member of a joint distribution of random variables using conditional probabilities. It is pretty important that you understand this if you are reading any type of Bayesian literature (you need to be able to describe probability distributions in terms of conditional probability), so I am going to derive it here.

Given the joint probability distribution:

$$P(A_1,A_2,...,A_N)$$

We can use the definition of conditional probability to factor the joint probability as follows:

$$P(A_1,A_2,...,A_N) = P(A_N|A_N-1,..,A_1)P(A_N-1,..,A_1)$$

So lets take N=4

$$P(A_1,A_2,A_3,A_4) = P(A_4|A_1,A_2,A_3)*P(A_1,A_2,A_3)$$

$$ P(A_1,A_2,A_3,A_4) = P(A_4|A_1,A_2,A_3)*P(A_3|A_1,A2)*P(A_1,A2) $$

$$ P(A_1,A_2,A_3,A_4) = P(A_4|A_1,A_2,A_3)*P(A_3|A_1,A2)*P(A2|A_1)P(A_1) $$

And it its generic form:

$$ P(\bigcap_{i=1}^n A_i) = \sum_{i=1}^n P(A_k|\bigcap_{j=1}^{k-1} A_j) $$

After you derive it, it's actually pretty easy to understand and remember.
