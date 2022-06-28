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

- It should _generalize_ well to inputs which don't look that much like known data
- It should be _accurate_, in the sene that it reproduces known outputs most of the time

The aim of this post is to build a mathematical framework which makes these properties precise, and then use it to construct the standard single layer perceptron from first principles.
The framework is an application of the principle of maximum entropy laid out in one of my [previous posts][1].

## Definitions and notation

Let $\mathcal{F} \colon \R^d \to L$ be the unknown function that we wish to approximate, where $L = \set{1, 2, \ldots, m}$ is a finite set of labels.
Let $S \subseteq \R^d$ of inputs on which the values of $\mathcal{F}$ are known, and let $f \colon S \to L$ be the restriction of $\mathcal{F}$ to $S$.
We will refer to $f$ as the _training data_ for the supervised classification task, and we will enumerate the elements of $S$ as:

$$S = \set{x_1, \ldots, x_n}$$

We will refer to a generic element of $S$ using the index $i$, as in $x_i \in S$, and we will use $f_i$ as shorthand for $f(x_i)$.

The value of $\mathcal{F}$ outside of $S$ cannot be inferred precisely from just $f$.
In acknowledgement of this fact, we will aim to approximate the values of $\mathcal{F}$ with probability distributions.
Let $P^m$ denote the set of all probability vectors in $\R^m$, where $m = \abs{L}$.
Thus a vector $p = (p_1, \ldots, p_m) \in \R^m$ lies in $P^m$ if and only if:

$$0 \leq p_j \leq 1, \quad \sum_j p_j = 1$$

Given a fixed label $k \in L$, let $\delta^k$ denote the probability vector:

$$\delta^k_j = \begin{cases} 1 & j = k \\ 0 & j \neq q \end{cases}$$

We will occasionally abuse notation and identify the label $k \in L$ with the probability distribution $\delta^k \in P^m$, and in particular we will speak of $\delta^k$ as if it were an element of $L$.
This is often called the _one-hot encoding_ of $L$.

Our goal is to construct a function $p \colon \R^d \to P^m$ which approximates $f$ in a sense to be explained in the next section.
We will denote the values of $p$ on the known inputs $x_i \in S$ as $p_i = p(x_i)$, and we will denote the components of these probability vectors using double index notation:

$$p_{ij} = p(x_i)_j$$

According to the definition of probability vector, we have:

$$0 \leq p_{ij} \leq 1, \quad \sum_j p_{ij} = 1$$

for each $i$.

## Classifier of maximal entropy

Given a probability-valued function $p \colon \R^d \to P^m$, we can attempt to approximate $\mathcal{F}$ by assigning to $x \in \R^d$ the index (or one of the indices) $j$ which maximizes $p(x)_j$.
This defines a function $F \colon \R^d \to L$ defined by:

$$F(x) = \argmax_{j \in L} p(x)_j$$

Depending on how we construct $p$, the function $F$ may or may not be a good approximation of $\mathcal{F}$.
There are a lot of bad ways to construct $p$; for instance, we could define $p(x)_j = \frac{1}{m}$ for all $x$ and $j$, meaning any label has equal probability of being assigned to any input.
On the plus side this avoids the problem of overfitting the model to the training data, but it has no hope of meeting any standard of accuracy.

In the other extreme, we could define $p(x) = \delta^{f_i}$ where the index $i$ is chosen so that $x_i$ is as close as possible to $x$ in $\R^d$.
This model accurately reproduces the training data in the sense that the $p(x_i)$ is completely concentrated at the label $f(x_i)$ for all $i$, but we can't expect that it will generalize well to unseen inputs.
This is because $p$ is completely certain about the correct label for every input, no matter how chaotic the training data is; a good model should be uncertain when trained on noisy data.

To strike a balance between these two extremes, we introduce the _total entropy_ function on the space of possible models $p$.
It is defined by:

$$H(p) = -\sum_{i=1}^n \sum_{j=1}^m p_{ij} \log p_{ij}$$

The inner sum is the ordinary Shannon entropy of the probability distribution $p(x_i)$, a measure of how concentrated $p(x_i)$ is.
$H$ takes its maximum value $n \log m$ when each $p(x_i)$ is as diffuse as possible, meaning $p_{ij} = \frac{1}{m}$ for all $i$ and $j$.
It takes its minimum value $0$ when each $p(x_i)$ is as concentrated as possible, meaning $p(x_i) = \delta^k$ for some label $k$.






[1]: {{ site.baseurl }}{% post_url /ds/info-theory/2022-01-27-max-entropy %} "Principle of maximum entropy"
