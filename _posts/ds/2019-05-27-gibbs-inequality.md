---
layout: post
permalink: /ds/gibbs-inequality/
title: Gibbs' Inequality
abstract: It is a fairly standard fact that relative entropy (KL-divergence) is positive definite, but I was unsatisfied with the proofs of this fact that I saw when I glanced through the literature.  In this post I will provide a complete proof which works on a general probability space.
date: 2019-05-27
categories: [math, data-science]
tags: live
comments: true
---

In the course of writing a recent [post][1] on relative entropy I wanted to include a proof that it is always positive definite, a result known as Gibbs' inequality.
As I argued in that post, relative entropy can be thought of as the change in information when updating a statistical model from one probability distribution to another in response to new data, and with this intuition in mind positive definiteness means:

- Updating a model never destroys information.
- The only "update" that doesn't increase information is the trivial update which doesn't change the distribution.

Note that this does not say that the new model is in any way "better" than the old model - relative entropy is after all agnostic about how the model was updated.
The more accurate intuition might be something like "a statistical model $p$ is surprised by every other model $q$ except $q = p$".
The relative entropy of $p$ from $q$ can then be interpretted as a quantitative measure of how surprised $p$ is by $q$ on average.

There are various proofs lying around the internet in the discrete or continuous case, but for a number of reasons (many of them ideological) I wanted an argument which uses the general measure theoretic formalism for probability theory.
So I worked out the details, and now I'm sharing them here.

The key ingredient is Jensen's inequality, which we will prove along the way just for fun.

<div class="proposition" text="Jensen's Inequality">
Let $F$ be a random variable on a probability space $(X, \Sigma, p)$ and let $\varphi \colon \R \to \R$ be a convex function, meaning $\phi(tx + (1-t)y) \leq t\phi(x) + (1-t)\phi(y)$ for all $x, y \in \R$ and all $t \in [0,1]$.
Then 

$$\phi(\E(F)) \leq \E(\phi(F))$$

with equality if and only if $\phi$ agrees almost everywhere on the range of $F$ with a linear function.
</div>
<div class="proof">
We will use the following property of a convex function $\phi$: for every $x_0 \in \R$ there is a _supporting line_ for the graph of $\phi$, i.e. a line $\ell$ such that $\ell(x_0) = \phi(x_0)$ and $\ell(x) \leq \phi(x)$ for all $x$.
Let $\ell(x) = ax + b$ be a supporting line for $\phi$ at the point $x_0 = \E(F)$.
By monotonicity and linearity of expectation, we have:

$$\E(\phi(F)) \geq \E(aF + b) = a \E(F) + b = \ell(\E(F)) = \phi(\E(F))$$

as desired.

To handle the equality case, we must understand when $\E(\phi(F)) = \E(\ell(F))$ for some supporting line $\ell$ at $x_0 = \E(F)$.
We have:

$$\E(\phi(F)) - \E(\ell(F)) = \int_X (\phi - \ell) \circ F\, dp$$

The integrand is nonnegative so we get equality if and only if it is zero almost everywhere.
But $(\phi - \ell) \circ F = 0$ almost everywhere if and only if $\phi = \ell$ almost everywhere on the range of $F$.

</div>

To prove that relative entropy is positive definite we will need a few additional properties of the Radon-Nikodym derivative.
Assume as usual that $p$ and $q$ are probability measures on $(X, \Sigma)$ and that $p \ll q$.
It certainly need not be the case that $q \ll p$, but $q$ does have a decomposition $q = q_0 + q_1$ where $q_0 \ll p$ and $q_1$ is _mutually singular_ with $p$, meaning $X$ is the disjoint union of of measurable sets $A$ and $B$ such that $q_1 = 0$ on $A$ and $p = 0$ on $B$.
This is called the Lebesgue decomposition theorem, and it can be proved using similar techniques as in the [proof][2] that I wrote up for the Radon-Nikodym theorem; roughly, the Lebesgue decomposition corresponds to the orthogonal decomposition of one vector relative to another in an appropriately chosen Hilbert space.

Now, whenever we have three $\sigma$-finite measures which satisfy $\nu \ll \mu \ll \lambda$ there is a chain rule for Radon-Nikodym derivatives:

$$\frac{d\nu}{d\lambda} = \frac{d\nu}{d\mu} \frac{d\mu}{d\lambda}$$

This follows from the uniqueness statement in the Radon-Nikodym theorem.
In our case, we have $q_0 \ll p \ll q$; for any measurable set $E \subseteq A$, we have:

$$q(E) = q_0(E) = \int_E \frac{dq_0}{dq}\, dq$$

since $q_1 = 0$ on $A$.
It follows that $\frac{dq_0}{dq} = 1$ on $A$, so we conclude from the chain rule that:

$$\frac{dp}{dq} = \frac{dq_0}{dp}^{-1}$$

almost everywhere with respect to $p$.
This is the property that we will need to show that relative entropy is nonnegative.

<div class="proposition" text="Gibb's Inequality">
Let $p$ and $q$ be probability measures on a measurable space $(X, \Sigma)$ and assume that $p \ll q$.
Then $D(p \| q) \geq 0$ with equality if and only if $p = q$.
</div>
<div class="proof">
As above, let $q = q_0 + q_1$ denote the Lebesgue decomposition of $q$ with respect to $p$, and let $X = A \cup B$ be the decompositionof $X$ into disjoint mutually singular sets for $q_1$ and $p$.
We have:

$$
\begin{align*}
   D(p \| q) &= \E\left( \log \frac{dp}{dq} \right) \\
   &= \E\left( -\log \frac{dq_0}{dp} \right) \\
   &\geq -\log \E\left( \frac{dq_0}{dp} \right) \quad \text{by Jensen's inequality} \\
   &= -\log \int_X \frac{dq_0}{dp}\, dp \\
   &= -\log q_0(X) \quad \text{by the definition of Radon-Nikodym derivative} \\
\end{align*}
$$

Here we used the fact that $\phi(x) = -\log x$ is a convex function to apply Jensen's inequality.

Now $q_0 \leq q$ since $q = q_0 + q_1$, $-\log x$ is a decreasing function of $x$, and $q(X) = 1$ since $q$ is a probability measure, so we conclude:

$$D(p \| q) \geq -\log q_0(X) \geq -\log q(X) = 0$$

It remains only to analyze the equality case.
Certainly if $p = q$ then $\frac{dp}{dq} = 1$ and $D(p \| q) = \E(\log 1) = 0$.

Conversely, if $D(p \| q) = 0$ then the two inequalities in the calculation above must be equalities.
The equality $q_0(X) = q(X)$ holds if and only if $q = q_0$, i.e. $q \ll p$.
The other inequality was Jensen's inequality, and that gives equality if and only if the function $\phi(x) = -\log x$ restricts to a linear function almost everywhere on the range of the random variable $\frac{dq}{dp}$.
But this forces $\frac{dq}{dp}$ to be some constant $C$ on $X$, meaning:

$$q(E) = \int_E \frac{dq}{dp}\, dp = \int_E C\, dp = C p(E)$$

for all measurable sets $E$.
Since $p$ and $q$ are probability measures we have $p(X) = q(X) = 1$, so we must have $C = 1$ and hence $p = q$.
</div>

[1]: {{ site.baseurl }}{% post_url ds/2019-05-27-relative-entropy %} "Relative Entropy"
[2]: {{ site.baseurl }}{% post_url math/2019-05-27-radon-nikodym %} "The Radon-Nikodym Theorem"
