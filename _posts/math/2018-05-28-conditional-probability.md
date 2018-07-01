---
layout: post
permalink: /math/conditional-probability/
title: Conditional probability and measure theory
abstract: Conditional probability is one of the fundamental tools in probability and statistics, but it is surprisingly difficult to get the foundations right.  In this post I will build up the theory from scratch using the conditional expectation as an entry point.
level: hard
date: 2018-05-28
categories: math
comments: true
---

Conditional probability is one of the most important tools in probability theory and statistics.
It lies at the heart of Bayesian statistics, it is used to analyze confounding variables in observational studies, and it is used to construct important statistical models like Markov chains or martingales.

For many purposes the definitions are relatively straightforward.
Given two events $E_1$ and $E_2$, we define the conditional probability of $E_1$ given $E_2$ to be:

$$\P(E_1 \vert E_2) = \frac{\P(E_1 \cap E_2)}{\P(E_2)}$$

This is fine so long as $\P(E_2) \neq 0$, but that requirement is much too stringent for many important applications.
For instance, it is common to condition one random variable against the values of another, and if the second random variable is continuous then it takes each of its values with probability zero.
As usual in probability theory it is not hard to write down formulas in important special cases, but it is rather hard to provide more conceptual foundations for the subject.

The [Borel paradox][1] illustrated these difficulties by showing that two natural ways to condition against the membership of a point on the sphere in a great circle give different probability distributions.
This calculation and others like it were part of what motivated Kolmogorov to use measure theory as a foundation for probability theory in  general.

## Conditional expectation

The setup is as follows: let $(\Omega, \Sigma, \P)$ be a probability space, let $X$ be a random variable on this space, and let $\Sigma_0$ be a sub-$\sigma$-algebra of $\Sigma$.
Our aim is to define the conditional expectation of $X$ with respect to $\Sigma_0$; this will be a $\Sigma_0$-measurable random variable, and by choosing $\Sigma_0$ carefully we will be able to replicate more familiar formulas from conditional probability.
To begin, note that $\P$ restricts to a measure $\P_0$ (not necessarily a probability measure!) on $\Sigma_0$.
Define a new measure $\P_X$ on $\Sigma_0$ by:

$$\P_X(E) = \int_E X\, d\P$$

(Note that $X$ is not $\Sigma_0$-measurable in general so we cannot integrate with respect to $\P_0$, but we can integrate $X$ over sets in $\Sigma_0$ with respect to $\P$.)
For any $E \in \Sigma_0$, if $\P_0(E) = 0 $ then clearly $\P(E) = 0$ and thus $\P_X(E) = 0$, so $\P_X$ is absolutely continuous with respect to $\P_0$.

<div class="definition">
With the notation above, the *conditional expectation* of $X$ with respect to $\Sigma_0$ is defined to be the Radon-Nikodym derivative:

$$\E(X \vert \Sigma_0) = \frac{d\P_X}{d\P_0}$$

</div>

(See my [post][2] on the Radon-Nikodym theorem for the appropriate definitions.)

This definition is a bit brutal, and it is not at all obvious how it is related to the more down-to-earth computations with coin flips or normal distributions that one normally encounters in probability.
First, note that the object $\E(X \vert \Sigma_0)$ is a $\Sigma_0$-measurable function $\Omega \to \R$.
By the Radon-Nikodym theorem this function is uniquely characterized by the equation:

$$\int_E X\, d\P = \P_X(E) = \int_E \E(X \vert \Sigma_0)\, d\P_0$$

The uniqueness statement is quite convenient for calculations: to compute $\E(X \vert \Sigma_0)$ we just need to come up with a candidate and check that it satisfies the equation above.
But it is still crucial to choose a $\Sigma_0$ well-adapted to the problem at hand, and it is not so clear how to do this.
In a great many cases $\Sigma_0$ comes from a second random variable $Y$ against which we condition $X$; this is the subject of the next section.

## Conditioning against a random variable

Let $Y$ be another random variable on $(\Omega, \Sigma, \P)$ taking values in a measurable space $(\mathcal{B}, \Sigma_{\mathcal{B}})$.
Since $Y$ is measurable, the pullback $\Sigma_Y = Y^{-1}\Sigma_{\mathcal{B}}$ is a sub-$\sigma$-algebra of $\Sigma$.
Define a probability measure $\P_Y$ on $\mathcal{B}$ by 

$$\P_Y(B) = \P(Y^{-1}(B))$$

and define a measure $\P_X$ on $\mathcal{B}$ by

$$\P_X(B) = \int_{Y^{-1}(B)} X\, d\P$$ 

As before, $\P_X$ is absolutely continuous with respect to $\P_Y$.

<div class="definition">
With the notation above, the _conditional expectation_ of $X$ with respect to $Y$ is defined to be the Radon-Nikodym derivative

$$E(X \vert Y) = \frac{d\P_X}{d\P_Y}$$

</div>

$\E(X \vert Y)$ is a measurable function $\mathcal{B} \to \R$ which is characterized by the equation:

$$\int_{Y^{-1}(B)} X\, d\P = \int_B \E(X \vert Y)\, d\P_Y$$

This function allows us to condition $X$ against the values of $Y$: we simply define $\E(X \vert Y = y_0)$ to be the value of $\E(X \vert Y)$ at the point $y_0 \in \mathcal{B}$.

The conditional expectation $\E(X \vert Y)$ of $X$ with respect to $Y$ is not the same as the conditional expectation $\E(X \vert \Sigma_Y)$ of $X$ with respect to $\Sigma_Y$, but they are quite closely related.
Using the notation $\P_{\Sigma_Y}$ for the restriction of $\P$ to $\Sigma_Y$, we can pull back the integral on the right-hand side to $\Omega$:

$$\int_{Y^{-1}(B)} X\, d\P = \int_{Y^{-1}(B)} \E(X \vert Y) \circ Y\, d\P_{\Sigma_Y}$$

But this is precisely the equation which characterizes $\E(X \vert \Sigma_Y)$, so we conclude:

$$\E(X \vert \Sigma_Y) = \E(X \vert Y) \circ Y$$

This equation holds almost everywhere with respect to $\P_{\Sigma_Y}$.

<div class="example">
Let $E_1$ and $E_2$ be events in $\Sigma$.
Let us use the theory we have developed to recover the standard formula for the conditional probability of $E_1$ with respect to $E_2$.
Consider the random variables $X = 1\_{E_1}$ and $Y = 1\_{E_2}$ on $\Omega$; we have:

$$\E(1_{E_i}) = \int_\Omega 1_{E_i}\, d\P = \P(E_i)$$

Thus these random variables together with the ordinary expected value computes the probabilities of $E_1$ and $E_2$.
With this in mind, it is natural to define $\P(E_1 \vert E_2)$ to be $\E(X \vert Y = 1)$; let us compute the conditional expectation and see what happens.

To begin, let us compute the measures $\P_X$ and $\P_Y$.
These are both measures on $\R$ which factor through $Y$, so they are supported on the set $\br{0,1}$, the range of $Y$.
To compute $\P_Y$ we just evaluate $\P$ on the level sets of $Y$:

$$\P_Y(0) = \P(Y^{-1}(0)) = \P(E_2^c), \quad \P_Y(1) = \P(Y^{-1}(1)) = \P(E_2)$$

To compute $\P_X$ we integrate $X$ over the level sets of $Y$:

$$\P_X(0) = \int_{Y^{-1}(0)} X\, d\P = \int_{E_2^c} 1_{E_1}\, d\P = \P(E_1 \cap E_2^c)$$

Similarly, $\P_X(1) = \P(E_1 \cap E_2)$.
If $\P(E_2) \neq 0$ then these are discrete nonvanishing random variables, and their Radon-Nikodym derivative is obtained by just dividing their coordinates.
We get:

$$\P(E_1 \vert E_2) = \E(X \vert Y=1) = \E(X \vert Y)(1) = \frac{d\P_X}{d\P_Y}(1) = \frac{\P_X(1)}{\P_Y(1)} = \frac{\P(E_1 \cap E_2)}{\P(E_2)}$$

</div>

So the vast machinery that we have developed so far really does recover the traditional definition of conditional probability.
But our more general language can accommodate conditioning against events of probability zero: it all depends on whether or not the Radon-Nikodym derivative exists!
To construct some concrete examples of this sort we will want the concept of a density function, so we shall develop it in the next section.

## Density functions

Density functions are computational tools used all the time in standard probabity theory, though typically they are defined specially for discrete and continuous random variables.
Keeping with the spirit of the rest of this post I will present the definition of density function on a general probability space.
The hero is once again the Radon-Nikodym theorem.

As before let $(\Omega, \Sigma, \P)$ be a probability space and let $Y$ be a random variable on $\Omega$ taking values in a measurable space $(\mathcal{B}, \Sigma_{\mathcal{B}})$.
As before let $\P_Y$ denote the pushforward of $\P$ along $Y$, so that $\P_Y(B) = \P(Y^{-1}(B))$ for $B \in \Sigma_{\mathcal{B}}$.

<div class="definition">
Suppose $(\mathcal{B}, \Sigma_{\mathcal{B}})$ is equipped with a measure $\mu$ such that $\P_Y$ is absolutely continuous with respect to $\mu$.
Then the _density function_ $f_Y$ of $Y$ (with respect to $\mu$) is the Radon-Nikodym derivative

$$f_Y = \frac{d\P_Y}{d\mu}$$

</div>

By the Radon-Nikodym theorem the density function is uniquely characterized by the equation

$$\P(Y \in B) = \int_B f_Y\, d\mu$$

for $B \in \Sigma_{\mathcal{B}}$.
Note that the conditional expectation $\E(X \vert Y)$ is the special case where $\mu$ is obtained by integrating $X$ over the level sets of $Y$.

<div class="example">
Assume that $Y$ is a continuous random variable, meaning it takes values in $\R$ equipped with Lebesgue measure $\lambda$ and $\P_Y$ is absolutely continuous with respect to $\lambda$.
Then it has a density function $f_Y$ which is characterized by the equation

$$\P(Y \in B) = \int_B f_Y\, d\lambda$$

for every Lebesgue measurable set $B \subseteq \R$.
Apply this to sets of the form $B = (-\infty, y]$ for $y \in \R$ and get:

$$\P(Y \leq y) = \int_{-\infty}^y f_Y\, d\lambda$$

By the Lebesgue differentiation theorem $\P(Y \leq y)$ is differentiable almost everywhere, and:

$$\frac{d}{dy}\P(Y \leq y) = f_Y(y)$$

The function $\P(Y \leq y)$ is called the _cumulative distribution function_ of $Y$.
</div>

## Joint and marginal density functions

Let $(\Omega_1, \Sigma_1, \P_1)$ and $(\Omega_2, \Sigma_2, \P_2)$ be probability spaces and consider the product 

$$(\Omega, \Sigma, \P) = (\Omega_1 \times \Omega_2, \Sigma_1 \otimes \Sigma_2, \P_1 \otimes \P_2)$$

Let $Y \colon (\Omega, \Sigma, \P) \to (\mathcal{B}, \Sigma_\mathcal{B}, \mu)$ be a random variable.

