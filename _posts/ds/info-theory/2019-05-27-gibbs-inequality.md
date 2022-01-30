---
layout: post
permalink: /ds/info-theory/gibbs-inequality/
title: Gibbs' Inequality
abstract: It is a fairly standard fact that relative entropy (KL-divergence) is positive definite, but I was unsatisfied with the proofs of this fact that I saw when I glanced through the literature.  In this post I will provide a complete proof which works on a general probability space.
date: 2019-05-27
categories: [math, data-science]
tags: live
comments: true
---

Given two probability distributions $p$ and $q$ defined on the same sample space, the relative entropy $\KL{p}{q}$ measures how probable events drawn from $p$ are from the perspective of $q$, on average.
It is a crucial fact for applications that relative entropy is always nonnegative, and that it is $0$ precisely when $p = q$ up to sets of measure $0$.
This is called _Gibbs' inequality_, and the purpose of this post is to prove it rigorously for probability distributions on an arbitrary measure space.

## Definition of relative entropy

First, let us recall the definition of relative entropy for probability distributions on an arbitrary measure space.
Recall that a measure $p$ is _absolutely continuous_ with respect to a measure $q$, written $p \ll q$, if every set with $q$-measure $0$ also has $p$-measure $0$.

<div class="definition">
Let $p$ and $q$ be probability measures on an arbitrary measure space such that $p$ is absolutely continuous with respect to $q$.
Then the _relative entropy_ of $p$ given $q$ is:

$$\KL{p}{q} = \E_p \left( \log \frac{dp}{dq} \right)$$

where $\E_p$ is the expectation with respect to the measure $p$ and $\frac{dp}{dq}$ is the Radon-Nikodym derivative of $p$ with respect to $q$.
</div>

See my [blog post][1] for the definition of the Radon-Nikodym derivative and a proof of its existence and uniqueness.

<div class="example">
If the measure space is finite, then the relative entropy is:

$$\KL{p}{q} = \sum_i p_i \log \frac{p_i}{q_i}$$

where $p = (p_1, \ldots, p_k)$ and $q = (q_1, \ldots, q_k)$ are probability vectors in $\R^k$.
</div>

<div class="example">
If the measure space is $\R$, then the relative entropy is:

$$\KL{P}{Q} = \int_{-\infty}^\infty p(x) \log \frac{p(x)}{q(x)}\, dx$$

where $p(x)$ and $q(x)$ are the density functions for $P$ and $Q$, respectively.
</div>

## Properties of Radon-Nikodym derivatives

Our proof of Gibbs' inequality will require us to invert the Radon-Nikodym derivative $\frac{dp}{dq}$.
This isn't too hard in priciple, but we must exercise some care because there is no guarantee that $q$ is absolutely continuous with respect to $p$.

To address this we use the _Lebesgue decomposition_ of $q$ with respect to $p$; this consists of a pair of measures $q_0$ and $q_1$ with the following properties:
- $q = q_0 + q_1$
- $q_0 \ll p$
- $q_1$ is _mutually singular_ with $p$

The latter condition means that the sample space can be written as the disjoint union of measurable sets $A$ and $B$ such that $q_1 = 0$ on $A$ and $p = 0$ on B.
The existence and uniqueness of $q_0$ and $q_1$ can be established using similar techniques as in the [proof][1] of the Radon-Nikodym theorem; roughly, the Lebesgue decomposition corresponds to the orthogonal decomposition of one vector relative to another in an appropriately chosen Hilbert space.

Now, whenever we have three $\sigma$-finite measures which satisfy $\nu \ll \mu \ll \lambda$ there is a chain rule for Radon-Nikodym derivatives:

$$\frac{d\nu}{d\lambda} = \frac{d\nu}{d\mu} \frac{d\mu}{d\lambda}$$

This follows from the uniqueness statement in the Radon-Nikodym theorem.
In our case, we have $q_0 \ll p \ll q$; for any measurable set $E \subseteq A$, we have:

$$q(E) = q_0(E) = \int_E \frac{dq_0}{dq}\, dq$$

since $q_1 = 0$ on $A$.
It follows that $\frac{dq_0}{dq} = 1$ on $A$, so we conclude from the chain rule that:

$$\frac{dp}{dq} = \frac{dq_0}{dp}^{-1}$$

almost everywhere with respect to $p$.

## Proof of Gibbs' inequality

We are almost ready to prove Gibbs inequality.
The last required tool is a workhorse result from convex geometry:

<div class="theorem" text="Jensen's inequality">
Let $X$ be a random variable defined on a probability space and let $f \colon I \to \R$ be a convex function defined on an interval $I \subseteq \R$ containing the range of $X$.
Then:

$$f(\E(X)) \leq \E(f(X))$$

with equality if and only if either $X$ is constant or $f$ coincides with a line on an interval which contains the range of $X$ (up to sets of measure zero).
</div>
<div class="proof">
See my [blog post][2].
</div>

We are now ready to prove the main theorem:

<div class="proposition" text="Gibb's Inequality">
Let $p$ and $q$ be probability measures on a measurable space $(X, \Sigma)$ and assume that $p \ll q$.
Then:

$$\KL{p}{q} \geq 0$$

with equality if and only if $p = q$.
</div>
<div class="proof">
Let $q = q_0 + q_1$ denote the Lebesgue decomposition of $q$ with respect to $p$, and let $X = A \cup B$ be the decomposition of $X$ into disjoint mutually singular sets for $q_1$ and $p$.
We have:

$$
\begin{align*}
   \KL{p}{q} &= \E_p \left( \log \frac{dp}{dq} \right) \\
   &= \E_p \left( -\log \frac{dq_0}{dp} \right) \\
   &\geq -\log \E_p \left( \frac{dq_0}{dp} \right) \quad \text{by Jensen's inequality} \\
   &= -\log \int_X \frac{dq_0}{dp}\, dp \\
   &= -\log q_0(X) \quad \text{by the definition of Radon-Nikodym derivative} \\
\end{align*}
$$

Here we used the fact that $\phi(x) = -\log x$ is a convex function to apply Jensen's inequality.
Note that $\log q_0(X) \leq \log q(X)$ because $\log$ is an increasing function and $q = q_0 + q_1 \geq q_0$.
Since $q(X) = 1$, we conclude:

$$\KL{p}{q} \geq -\log q_0(X) \geq -\log q(X) = 0$$

It remains only to analyze the equality case.
Certainly if $p = q$ then $\frac{dp}{dq} = 1$ and $\KL{p}{q} = \E(\log 1) = 0$.

Conversely, if $\KL{p}{q} = 0$ then the two inequalities in the calculation above must be equalities.
The equality $q_0(X) = q(X)$ holds if and only if $q = q_0$, i.e. $q \ll p$.
The other inequality was Jensen's inequality, and that gives equality if and only if the function $\phi(x) = -\log x$ restricts to a linear function almost everywhere on the range of the random variable $\frac{dq}{dp}$.
But this forces $\frac{dq}{dp}$ to be some constant $C$ on $X$, meaning:

$$q(E) = \int_E \frac{dq}{dp}\, dp = \int_E C\, dp = C p(E)$$

for all measurable sets $E$.
Since $p$ and $q$ are probability measures we have $p(X) = q(X) = 1$, so we must have $C = 1$ and hence $p = q$.
</div>

[1]: {{ site.baseurl }}{% post_url math/2019-05-27-radon-nikodym %} "The Radon-Nikodym Theorem"
[2]: {{ site.baseurl }}{% post_url math/2019-09-01-jensen-inequality %} "Jensen's Inequality and Statistics"
