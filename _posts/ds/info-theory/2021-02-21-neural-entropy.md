---
layout: post
permalink: /ds/info-theory/neural-entropy/
title: Neural Models and Entropy Optimization
abstract: We show how neural networks are solutions to an information-theoretic variational principle.
date: 2021-02-21
categories: [math, data-science]
comments: true
---

In this post we will study the standard multi-class classification problem, where we are given input vectors $x_1, \ldots, x_n \in \R^d$ and output labels $\ell_1, \ldots, \ell_n$ taking values in a finite set $L = \br{1, \ldots, k}$.
The set of tuples $(x_1, \ell_1), \ldots, (x_n, \ell_n)$ is the _training data_, and our task is to construct a function $f \colon \R^d \to L$ which approximates the training data as well as possible.

With no additional restrictions this task is trivial: define $f(x_i) = \ell_i$ for $i = 1, \ldots, n$, and choose $f(x)$ arbitrarily for all other values of $x$.
But in problems of practical interest the training values $(x_i, \ell_i)$ are drawn from a fixed but unknown probability distribution, and we want to construct $f$ in a way that matches this distribution as closely as possible.

There are no good generic solutions to this problem, in the sense that any given strategy for constructing $f$ can be thwarted by adversarially chosen training data.
But we can find optimal solutions given assumptions about the probability distribution which generates the training data.

## Relative entropy minimization

In order to discover strategies for constructing the desired function $f$, we need to start with an empirical principle.
In this post we'll use the _principle of minimum discrimination information_ (PMDI) which uses the concept of relative entropy.
Given two probability distributions $p, q \in \R^d$, the relative entropy from $p$ to $q$ is by definition:

$$\KL{q}{p} = -\sum_i q_i \log \frac{p_i}{q_i}$$

(The notation $\text{KL}$ stands for _Kullback-Liebler_, the mathematicians who developed the theory of relative entropy.)

Relative entropy can be viewed as a sort of cost function for moving through the space of probability distributions; it represents the amount of new information must be introduced to revise one's understanding of the world from the probabilistic model $p$ to the probabilistic model $q$.
