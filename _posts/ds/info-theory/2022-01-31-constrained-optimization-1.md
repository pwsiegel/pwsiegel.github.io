---
layout: post
permalink: /ds/info-theory/constrained-optimization-1/
title: Computational constrained optimization 1: Lagrange multipliers
abstract: In my post on the principle of maximum entropy I showed how choosing good priors in Bayesian modeling can be expressed as a constrained optimization problem, using (relative) entropy as the objective function. This is the first in a series of posts on computational methods for solving constrained optimization problems, using entropy as a source of examples. This post covers the method of Lagrange multipliers.
date: 2022-01-27
categories: [math, data-science]
tags: live
comments: true
---

In my post on the [principle of maximum entropy][1], we began with the following problem: given an otherwise normal looking six sided die whose average roll is known to be $4.5$, what should we guess is the probability of rolling a $2$?
This is an example of the more general problem of constructing uninformative priors in Bayesian modeling, and we argued that we should solve it by maximizing the entropy function:

$$H(p_1, \ldots, p_6) = -\sum_{i=1}^6 p_i \log p_i$$

subject to the constraints:

$$\sum_{i=1}^6 p_i = 1, \quad \sum_{i=1}^6 i p_i = 4.5$$

The first constraint ensures that $(p_1, \ldots, p_n)$ is a probability vector, and the second ensures that the average die roll is $4.5$.
(Strictly speaking we should also include $p_i \geq 0$ for each $i$ as a constraint; it will turn out that this is satisfied automatically, but it is possible to impose this sort of constraint using similar techniques as in this post.)

We did not go into detail about how to actually solve constrained optimization problems of this sort.
In this post, the first in a series on the computational aspects of constrained optimization, we will use the method of Lagrange multipliers.

## Lagrange multipliers

Let us consider the general problem of optimizing an objective function $f(x_1, \ldots, x_k)$ with a finite set of constraints $g_j(x_1, \ldots, x_k) = C_j$ for $j = 1, \ldots, m$.
Our goal is to find _necessary_ conditions for $f$ to attain its maximum or minimum value at $x = (x_1, \ldots, x_k)$ - these conditions will not be _sufficient_, but they will narrow down the search for optima significantly.
This is similar to using calculus to optimize $f$ without constraints: the equation $\nabla f(x) = 0$ is a necessary but not sufficient condition for $f$ to attain its maximum or minimum value at $x$.

We will assume that $f$ and the $g_j$'s are smooth functions, and that the vector $C = (C_1, \ldots, C_m)$ is a regular value of the function $G \colon \R^k \to \R^m$ with components $g_j$.
The latter condition ensures that the constraint space $G(x) = C$ is a smooth $k-m$-dimensional submanifold of $\R^k$; otherwise we would have to check the values of $f$ at the singular locus for this space.

### One constraint

Let's start with the case $m = 1$, so that we have a single constraint equation $g(x_1, \ldots, x_k) = C$.
Let $N$ denote the solution space for the constraint equation; assuming $C$ is a regular value of $g$, $N$ is a submanifold of $\R^k$ of dimension $k-1$.
Assume $a = (a_1, \ldots, a_k)$ is an extremal point for $f \vert_N$.

Choose an arbitrary smooth curve $r \colon \R \to N$ which satisfies $r(0) = a$, so that $g(r(t)) = C$ for all $t$.
Differentiating both sides with respect to $t$ and applying the chain rule, we get:

$$\nabla g(a) \cdot r'(0) = 0$$

Since $a$ is an extremal point for the restriction of $f$ to $N$ it must also be an extremal point for $f$ restricted to the range of $r$, and hence the derivative of $f(r(t)$ at $t=0$ must vanish.
Applying the chain rule again, we get:

$$\nabla f(a) \cdot r'(0) = 0$$

Thus $\nabla f(a)$ and $\nabla g(a)$ are both orthogonal to the vector $r'(0)$, a tangent vector to $N$ at $a$.
Let $\varphi \colon \R^{k-1} \to U \subseteq N$ be a local coordinate system for $N$ satisfying $\varphi(0) = a$, and consider the coordinate rays $r_i(t) = (0, \ldots, t, \ldots, 0)$ with $t$ in the $i$th slot and $0$ everywhere else.
The tangent vectors $r_i'(0)$ form a basis for the tangent space $T_a N$, and as above $\nabla g(a)$ and $\nabla f(a)$ are both orthogonal to all of them.

Thus $\nabla g(a)$ and $\nabla f(a)$ both lie in the orthogonal complement of $T_a N$ in $\R^k$.
Since $T_a N$ has dimension $k-1$ the orthogonal complement is one dimensional and hence $\nabla g(a)$ and $\nabla f(a)$ are proportional.
Thus there is a constant $\lambda$ such that

$$\nabla f(a) = \lambda \nabla g(a)$$

The components of this equation give $k$ equations with $k+1$ unknowns, and the constraint $g(x) = C$ gives a $k+1$st equation, so generally speaking there will be a discrete set of solutions which must contain the points which maximize and minimize $f$.
$\lambda$ is called the _Lagrange multiplier_ for the constrained optimization problem.

### Multiple constraints

Now let us return to the case where we have $m$ constraints $g_j(x_1, \dlots, x_k) = C_j$ for $j = 1, \ldots m$, and we assume that $C = (C_1, \ldots, C_m)$ is a regular value for this system of equations.
Let $N$ denote the common solution space for these $m$ equations as before; now $N$ is a $k-m$-dimensional submanifold of $\R^k$.

As before, assume $a = (a_1, \ldots, a_k)$ is an extremal point for $f \vert_N$, and consider a smooth curve $r \colon \R \to N$ which satisfies $r(0) = a$.
Applying the chain rule to each constraint gives:

$$\nabla g_j(a) \cdot r'(0) = 0$$

for each $j$.
Using the fact that $a$ is extremal for the restriction of $f$ to the image of $r$ yields:

$$\nabla f(a) \cdot r'(0) = 0$$

So now we know that each of the $m+1$ vectors $\nabla f(a), \nabla g_1(a), \ldots \nabla g_m(a)$ is orthogonal to $r'(0)$.
Working in a local coordinate system for $N$ near $a$, we conclude as before that these vectors all lie in the orthogonal complement to the tangent space $T_a N$ in $\R^k$; this orthogonal complement has dimension $m$ because $N$ has dimension $k-m$.
Thus there is a dependence relation:

$$\mu_0 \nabla f(a) + \mu_1 \nabla g_1(a) + \ldots + \mu_m \nabla g_m(a) = 0$$

The coefficient $\mu_0$ cannot be $0$ because then there would be a dependence relation among the $\nabla g_j(a)$'s, contradicting the assumption that $C = (C_1, \ldots, C_m)$ is a regular value for the system of constraint equations.
So we can divide through by $\mu_0$ and obtain a dependence relation of the form:

$$\nabla f(a) = \lambda_1 \nabla g_1(a) + \ldots + \lambda_m \nabla g_m(a)$$

The components of this equation give $k$ equations with $k + m$ unknowns, and the constraints $g_j(x) = C_j$ give $m$ more equations, so once again we can expect a discrete set of solutions which must contain the maxima and minima of $f$.
The $m$ constants $\lambda_1, \ldots, \lambda_m$ are still called Lagrange multipliers for the constrained optimization problem.

[1]: {{ site.baseurl }}{% post_url /ds/info-theory/2022-01-27-max-entropy %} "The principle of maximum entropy"
