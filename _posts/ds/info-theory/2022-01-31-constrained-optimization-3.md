---
layout: post
permalink: /ds/info-theory/constrained-optimization-3/
title: "Computational constrained optimization 3: Gradient projections"
abstract: "In my post on the principle of maximum entropy I showed how choosing good priors in Bayesian modeling can be expressed as a constrained optimization problem, using (relative) entropy as the objective function. This is the second in a series of posts on computational methods for solving constrained optimization problems, using entropy as a source of examples. This post shows how to adapt the numerical technique of gradient descent to handle linear constraints."
date: 2022-01-27
categories: [data-science]
comments: true
---

In my post on the [principle of maximum entropy][1], we began with the following problem: given an otherwise normal looking six sided die whose average roll is known to be $4.5$, what should we guess is the probability of rolling a $2$?
This is an example of the more general problem of constructing uninformative priors in Bayesian modeling, and we argued that we should solve it by maximizing the entropy function:

$$H(p_1, \ldots, p_6) = -\sum_{i=1}^6 p_i \log p_i$$

subject to the constraints:

$$\sum_{i=1}^6 p_i = 1, \quad \sum_{i=1}^6 i p_i = 4.5$$

The first constraint ensures that $(p_1, \ldots, p_6)$ is a probability vector, and the second ensures that the average die roll is $4.5$.
In this post we will review how to use gradient descent to solve optimization problems, and then adapt it to linear constraints such as those above.

## Gradient descent
