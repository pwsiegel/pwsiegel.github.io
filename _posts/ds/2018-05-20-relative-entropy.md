---
layout: post
permalink: /ds/relative-entropy/
title: Relative Entropy
abstract: Relative entropy - also known as the *Kullback-Leibler divergence* - measures the information gained by replacing one random model with another.  This makes it an important tool in applications where one wishes to update a prior model in response to new observations.
level: hard
date: 2018-05-20
categories: [math, data-science]
comments: true
---

Last year I wrote a couple posts ([here][1] and [here][2]) about Shannon's information theory with the intention of exploring how it can be used as a framework for organizing ideas in statistics and machine learning.
I got busy, but I've been doing some reading on tuning classifiers using entropy considerations and so this project as attained renewed importance.

At the end of my last post I reviewed some conceptual problems with Shannon's definition of the entropy of a continuous random variable (the _differential entropy_), first noticed by Edwin Jaynes.
The first hint that something might be wrong is that the definition doesn't fit in any obvious way into the measure theoretic framework for probability theory - this is usually a bad sign.
More damning is the observation that differential entropy is not coordinate invariant, a property which nearly precludes it from being the right object of study.

Fortunately, both objections can be addressed elegantly by viewing entropy as a relative quantity.
In my previous posts I motivated the definition of entropy by arguing that it is the right way to measure the "expected surprisingness" of a random variable.
But surprise depends on one's prior beliefs: a rainy day is surprising to someone who was expecting the sun but not to someone who was expecting rain.
So surprise - and therefore entropy - should be viewed as a property of the _transition_ from one model to another rather than an intrinsic property of a single model.

The plan for this post is to define relative entropy properly, establish some of its basic properties, and connect it to other standard constructions in statistics.

## Relative entropy

Let $(\Omega, \Sigma)$ be a measurable space with two probability measures $p$ and $q$.
In the spirit of Bayes' rule, it may help to view $q$ as a prior model for a random process and $p$ as a posterior model formulated by updating $q$ in response to new data.

We will assume that $p$ is _absolutely continuous_ with respect to $q$, written $p \ll q$, meaning that $p(E) = 0$ whenever $q(E) = 0$ for any $E \in \Sigma$.
This will ensure that the relative entropy is finite; intuitively, if we observe an event that we previously believed to occur with probability zero then we will be infinitely surprised.

<div class="definition">
Let $p$ and $q$ be probability measures on a measurable space $(\Omega, \Sigma)$ and assume that $p \ll q$.
The _relative entropy_, also called the _Kullback-Liebler divergence_, of $p$ from $q$ is the quantity:

$$D(p \| q) = \int_\Omega \log \frac{dp}{dq}\, dp$$

where $\frac{dp}{dq}$ is the [Radon-Nikodym derivative][3] of $p$ with respect to $q$.
</div>

Equivalently, $D(p \| q)$ is the expected value of $\log \frac{dp}{dq}$ viewed as a random variable on $(\Omega, \Sigma, p)$.
The Radon-Nikodym derivative can be a bit intimidating, so let us work out what the relative entropy is in the case where $\Omega$ is finite.
(The continuous case is not much different.)

<div class="example">
Let $\Omega$ be a set with $n$ elements and declare all subsets of $\Omega$ to be measurable.
A probability measure on $\Omega$ is just a vector in $\R^n$ whose entries sum to $1$, so we may write $p = (p_1, \ldots, p_n) \in \R^n$ and $q = (q_1, \ldots, q_n) \in \R^n$.
In my [post][3] on the Radon-Nikodym theorem, I showed that the condition $p \ll q$ is equivalent to the statement that $p_i = 0$ whenever $q_i = 0$ and that $\frac{dp}{dq}$ is the vector whose $i$th coordinate is given by:

$$\left. \frac{dp}{dq} \right|_i = \begin{cases} \frac{p_i}{q_i} & q_i \neq 0 \\ 0 & \text{otherwise} \end{cases}$$

Viewing this as a function on $\Omega$, note that we can ignore the $i$'s for which $q_i = 0$ for the purposes of integrating against the measure $p$ because $p_i = 0$ by absolute continutiy.
We obtain:

$$D(p \| q) = \sum_{i \colon q_i \neq 0} p_i \log \frac{p_i}{q_i} = \sum_{i \colon q_i \neq 0} p_i(\log p_i - \log q_i)$$

</div>

It is worth philosophizing about this formula for a moment.
The values $\log p_i - \log q_i$ hint at the surprise functions in my previous [post][2], and indeed we could write down axioms for the "relative surprise function" $S \colon \Sigma \to \R^\pm$ given by $S(E) = \log p(E) - \log q(E)$.
For instance, if $E_1$ and $E_2$ are independent then we have $S(E_1 \cap E_2) = S(E_1) + S(E_2)$ as before.

The relative entropy, then, is just the $p$-average of some sort of relative surprise function.
It makes sense that we take the $p$-average rather than the $q$-average: we have updated our model from the prior $q$ to the posterior $p$, and we are reflecting on how surprised we are given our current (hopefully better) understanding of the world.
In particular, $D(p \| q)$ is very far from being symmetric in $p$ and $q$: setting aside signs, the average is taken from the point of view of $p$ rather than $q$.

Intuitively one expects that $p$ is less surprised on average by itself than any other probability distribution $q$, or in otherwords that $D(p \| q) \geq 0$ with equality if and only if $p = q$.
This fact, known as _Gibbs' inequality_, is not completely trivial, so I wrote up the details in a separate [post][4].
Gibb's inequality has some technical significance which we may encounter in future posts, but mostly it is a good sanity check for the definitions.

A final remark: while the abstract definition of relative entropy presented here using Randon-Nikodym derivatives may carry a bit of tecchnical overhead, it has a lot of advantages.
The same definition works for both discrete and continuous probability spaces, and the ingredients are fairly robust with respect to limits; this makes it possible to estimate relative entropy in the continuous case using discrete approximations.
The definition doesn't make any mention of a coordinate system, so we get coordinate invariance for free - this alone sets it apart from differential entropy.
And the definition already makes sense on any Euclidean space of any dimension.
So whatever we might have lost in accessbility we gained back in flexibility.

## Relative entropy and information theory
 
For these reasons and others it is desireable to place relative entropy rather than Shannon's notion of entropy at the foundations of information theory.
In this section we shall show how some of the fundamental constructions in information theory can be recovered from relative entropy.

### Shannon entropy

First, we will recover the classical Shannon entropy itself.
Let $p = (p_1, \ldots, p_n)$ be a probability measure on a finite set $\Omega$ with $n$ elements, and let $u = (\frac{1}{n}, \ldots, \frac{1}{n})$ be the uniform measure on $\Omega$.
We have:

$$
\begin{align*}
D(p \| u) &= \sum_i p_i \left( \log p_i - \log \frac{1}{n} \right) \\
&= \sum_i p_i \log p_i + \sum_i p_i \log n \\
&= \log n - H(p) \\
&= H(u) - H(p)
\end{align*}
$$

where $H(\cdot)$ is the Shannon entropy.
Note that this calculation depends crucially on the uniform distribution; it is not in general true that $D(p \| q) = H(q) - H(p)$.
It shows that the fundamental information theoretic quantity associated to a single probability distribution $p$ is not really its entropy but the difference between its entropy and that of the uniform distribution.
Indeed, this is the conceptual reason why differential entropy causes so much trouble: there is no uniform probability distribution on $\R$.

### Mutual information

The mutual information of two random variables quantifies how far they are from being independent; it is commonly used in computational linguistics and machine learning as an independence test.
It has a particularly nice interpretation in terms of relative entropy.
As usual I will present the construction using abstract measure theoretic language, with the usual tradeoff between flexibility and accessibility.

Let $X \colon (A, \mathcal{A}, p) \to (B, \mathcal{B}, \mu)$ be a random variable on a probability space $A$ taking values in a measure space $B$.
The probability measure $p$ pushes forward along $X$ to give a measure $X_\ast p$ on $B$ defined by:

$$X_\ast p(B) = p(X^{-1}(B))$$

for any set $B \in \mathcal{B}$.
Let us assume that $X_\ast p \ll \mu$, i.e. every set of $\mu$-measure zero pulls back along $X$ to a set of $p$-measure zero.
Then there is a Radon-Nikodym derivative $\frac{dX_\ast p}{d\mu}$ called the _density function_ of $X$.
It is uniquely characterized by the property:

$$p(X \in B) = \int_B \frac{dX_\ast p}{d\mu}\, d\mu$$

In the cases where $X$ is discrete or continuous the target measure space is $\R$ equipped with Lebesgue meassure, and one recovers the usual formulas relating probability and density functions.

Now, let $X_1 \colon (\Omega_1, \Sigma_1, p_1) \to (B, \mathcal{B}, \mu)$ and $X_2 \colon (\Omega_2, \Sigma_2, p_2) \to (B, \mathcal{B}, \mu)$ be two random variables taking values in the same measure space $B$.
Assume that $(X_i)_\ast p_i \ll \mu$ for $i = 1,2$ and let $f_i$ denote the density function of $X_i$.

