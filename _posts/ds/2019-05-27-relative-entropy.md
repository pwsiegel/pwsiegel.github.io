---
layout: post
permalink: /ds/relative-entropy/
title: Relative Entropy
abstract: Relative entropy - also known as the *Kullback-Leibler divergence* - measures the information gained by replacing one random model with another.  In this post and posts to come I will argue that relative entropy provides a good foundation for information theory in particular and statistical machine learning more generally.
date: 2019-05-27
categories: [math, data-science]
comments: true
---

Some time ago I [wrote][1] a bit about information theory as defined in Claude Shannon's seminal paper _A mathematical theory of communication_.
Since then I have come to view information theory as a near-universal organizing principle in data science and machine learning, and I am going to try to develop this perspective in an ongoing series of blog posts.

A key part of my current thinking on the subject is that _relative entropy_, rather than traditional Shannon entropy, is the fundamental object of study in the field.
I was initially led to this perspective by the observation that relative entropy, unlike ordinary entropy, can be defined on an abstract probability space - this immediately guarantees that it has nice properties like coordinate invariance and various notions of continuity, and it also allows for a unified treatment of the discrete and continuous cases.
As often happens in mathematics these theoretical advantages yield dividends in downstream applications.

## Relative entropy

Let $(\Omega, \Sigma)$ be a measurable space with two probability measures $p$ and $q$.
In the spirit of Bayesian statistics, let us view $q$ as a prior model for a random process and $p$ as a posterior model formulated by updating $q$ in response to new data.
In this view, relative entropy will correspond to the average amount of information gained by updating our model from $q$ to $p$.

We will assume that $p$ is _absolutely continuous_ with respect to $q$, written $p \ll q$, meaning that $p(E) = 0$ whenever $q(E) = 0$ for any $E \in \Sigma$.
Intuitively, we would gain an infinite amount of information by learning that an event which we previously believed has probability $0$ actually has positive probability, so it is natural to expect that the absolute continuity condition would be required to ensure that relative information is finite.

<div class="definition">
Let $p$ and $q$ be probability measures on a measurable space $(\Omega, \Sigma)$ and assume that $p \ll q$.
The _relative entropy_, also called the _Kullback-Liebler divergence_, of $p$ from $q$ is the quantity:

$$D(p \| q) = \int_\Omega \log \frac{dp}{dq}\, dp$$

where $\frac{dp}{dq}$ is the [Radon-Nikodym derivative][2] of $p$ with respect to $q$.
</div>

Equivalently, $D(p \| q)$ is the expected value of $\log \frac{dp}{dq}$ viewed as a random variable on $(\Omega, \Sigma, p)$.
It is not actually necessary that $p$ and $q$ are probability measures; the definition makes sense as long as the hypotheses of the Radon-Nikodym theorem are satisfied, so any pair of $\sigma$-finite measures would do the job.

This definition is perhaps a bit intimidating, at least in part because of the presence of the Radon-Nikodym derivative.
I will try to make it a bit more concrete shortly, but first let us note some of its nice theoretical properties so that it feels worth the effort.

1. Coordinate invariance: the definition makes no mention of a coordinate system, and so relative entropy is automatically coordinate independent.
2. Positive definiteness ([Gibbs' inequality][3]): $D(p \| q) \geq 0$ with equality if and only if $p = q$.

As in my [previous post][1], these properties are lacking in Shannon's differential entropy.
It has a lot of other nice properties as well which may come up in subsequent posts; for instance relative entropy is continuous with respect to the vague topology on the space of measures.

## The finite case

Let us explicitly compute relative entropy on a finite probability space $\Omega$ with $n$ elements and the power set $\sigma$-algebra.
A probability measure on $\Omega$ is just a vector in $\R^n$ with nonnegative entries which sum to $1$, so we may write $p = (p_1, \ldots, p_n) \in \R^n$ and $q = (q_1, \ldots, q_n) \in \R^n$.
In my [post][2] on the Radon-Nikodym theorem, I showed that the condition $p \ll q$ is equivalent to the statement that $p_i = 0$ whenever $q_i = 0$ and that $\frac{dp}{dq}$ is the vector whose $i$th coordinate is given by:

$$\left. \frac{dp}{dq} \right|_i = \begin{cases} \frac{p_i}{q_i} & q_i \neq 0 \\ 0 & \text{otherwise} \end{cases}$$

Viewing this as a function on $\Omega$, note that we can ignore the $i$'s for which $q_i = 0$ for the purposes of integrating against the measure $p$ because $p_i = 0$ by absolute continutiy.
So by contracting $\Omega$ if necessary we can assume without loss of generality that $q_i \neq 0$ for all $i$.
We obtain:

$$D(p \| q) = \sum_i p_i \log \frac{p_i}{q_i} = \sum_i p_i \log p_i - \sum_i p_i \log q_i = -H(p) - \sum_i p_i \log q_i$$

where $H(p)$ is the ordinary Shannon entropy.
This gives a nice explicit formula for computing relative entropy, and this formula leads us to introduce another important definition:

<div class="definition">
The _cross entropy_ of the pair $(p, q)$ is the quantity:

$$H(p, q) = \E_p(-\log q) = -\sum_i p_i \log q_i$$

</div>

Cross entropy comes up often machine learning, often as a sort of distance measure between two probability distributions (even though it isn't symmetric!)
For instance, it is typical to use it as a loss function when training machine learning models.
But cross entropy has many of the same theoretical problems as Shannon entropy, particularly when introducing continuous distributions, and so it has started to fall out of favor in some circles in favor of relative entropy.
As above, the three notions of entropy are related in the finite case by the simple formula:

$$H(p, q) = H(p) + D(p \| q)$$

## The continuous case

[1]: {{ site.baseurl }}{% post_url ds/2017-03-05-entropy-defn %} "On the definition of Shannon entropy"
[2]: {{ site.baseurl }}{% post_url ds/2019-05-27-radon-nikodym %} "The Radon-Nikodym Theorem"
[3]: {{ site.baseurl }}{% post_url ds/2019-05-27-gibbs-inequality %} "Gibbs' inequality""

