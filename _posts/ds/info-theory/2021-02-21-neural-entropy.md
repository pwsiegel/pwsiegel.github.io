---
layout: post
permalink: /ds/info-theory/supervised-ml-entropy/
title: Supervised machine learning and entropy optimization
abstract: We show how neural networks are solutions to an information-theoretic variational principle.
date: 2021-02-21
categories: [data-science]
comments: true
---

The goal of supervised machine learning is to approximate an unknown function given knowledge of its behavior on a finite set of inputs.
For instance, a supervised text image classifier might attempt to approximate the function which maps an input image to the set of objects which are present in the image.
Of course it is usually impossible to reconstruct a function from only finitely many values, so we have to specify what sorts of properties we want the approximation to have:

- The approximation should be _robust_ against errors or bias in the known data
- It should _generalize_ well to inputs which don't look that much like known data
- It should be _accurate_, in the sene that it reproduces known outputs most of the time

The aim of this post is to build a mathematical framework which makes these properties precise, and then use it to construct the standard single layer perceptron from first principles.
The framework is an application of the principle of maximum entropy laid out in one of my [previous posts][1].

## Definitions and notation

Let $F \colon \R^d \to L$ be the unknown function that we wish to approximate, where $L = \set{1, 2, \ldots, \ell}$ is a finite set of labels.
Let $S \subseteq \R^d$ of inputs on which the values of $F$ are known, and let $f \colon S \to L$ be the restriction of $F$ to $S$.
We will refer to $f$ as the _training data_ for the supervised classification task, and we will enumerate the elements of $S$ as:

$$S = \br{x_1, \ldots, x_n}$$

We will refer to a generic element of $S$ using the index $i$, as in $x_i \in S$, and we will use $f_i$ as shorthand for $f(x_i)$.

The value of $\mathcal{F}$ outside of $S$ cannot be inferred precisely from just $f$.
In acknowledgement of this fact, we will instead aim to approximate the values of $\mathcal{F}$ with probability distributions.
Let $\ell = \abs{L}$, and let $P^\ell$ denote the set of all probability vectors in $\R^\ell$.
Thus a vector $p = (p_1, \ldots, p_\ell) \in \R^\ell$ lies in $P^\ell$ if and only if:

$$0 \leq p_j \leq 1, \quad \sum_j p_j = 1$$

Given a fixed label $k \in L$, let $\delta^k$ denote the probability vector:

$$\delta^k_j = \begin{cases} 1 & j = k \\ 0 & j \neq q \end{cases}$$

We will occasionally abuse notation and identify the label $k \in L$ with the probability distribution $\delta^k \in P^\ell$, and in particular we will speak of $\delta^k$ as if it were an element of $L$.
This is often called the _one-hot encoding_ of $L$.

Our goal is to construct a function $p \colon \R^d \to P^\ell$ which approximates $f$ in a sense to be explained in the next section.
We will denote the values of $p$ on the known inputs $x_i \in S$ as $p_i = p(x_i)$, and we will denote the components of these probability vectors using double index notation:

$$p_{ij} = p(x_i)_j$$

According to the definition of probability vector, we have:

$$0 \leq p_{ij} \leq 1, \quad \sum_j p_{ij} = 1$$

for each $i$.

## Classifier of maximal entropy

Given a probability-valued function $p \colon \R^d \to P^\ell$, we can attempt to approximate $F$ with the function $\mathcal{F} \colon \R^d \to L$ via:

$$\mathcal{F}(x) = \argmax_{j \in L} p(x)_j$$

We could construct $p$ by extending the function

$$x_i \mapsto \delta^{f_i}$$

to all of $\R^d$.
With this approach $\mathcal{F}$ would exactly agree with $f$ on $S$, and so in that sense $p$ would be accurate.
But there is little hope that such a $p$ would generalize well to unseen data, or that it would be robust against errors in $f$.

Instead, let us impose a milder condition:

$$\sum_i p_{ij} x_i = \sum_{i \colon f_i = j} x_i$$




[1]: {{ site.baseurl }}{% post_url /ds/info-theory/2022-01-27-max-entropy %} "Principle of maximum entropy"
