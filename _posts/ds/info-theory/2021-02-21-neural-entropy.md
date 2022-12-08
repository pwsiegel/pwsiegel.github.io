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

Supervised machine learning begins with a training dataset, consisting a finite set of tuples:

$$(x_1, y_1), \ldots, (x_n, y_n)$$

where the $x_i$'s are vectors in some Euclidean space $\R^d$ and the $y_i$'s lie in some finite set $L$ of labels.
Let $m = \abs{L}$ and let $\set{e_1, \ldots, e_m}$ denote the standard basis of $\R^m$, meaning the coordinates of $e_j$ are all $0$ except for a $1$ in the $j$th slot.
Choosing an enumeration of $L$ yields a bijection between $L$ and this basis, so for the remainder of this post we will identify these two sets and view the elements of $L$ as standard basis vectors in $\R^m$.
This is called the _one-hot encoding_ of $L$.

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
