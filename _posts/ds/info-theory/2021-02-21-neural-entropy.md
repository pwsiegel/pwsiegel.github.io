---
layout: post
permalink: /ds/info-theory/neural-entropy/
title: Neural Models and Entropy Optimization
abstract: We show how neural networks are solutions to an information-theoretic variational principle.
date: 2021-02-21
categories: [math, data-science]
comments: true
---

In this post we will study the standard multi-class classification problem, where we are given a function

$$f \colon S \subseteq \R^d \to L$$

with $S = \br{x_1, \ldots, x_n}$ is a finite set of vectors and $L = \br{1, \ldots, k}$ is a finite set of labels.
This function is often called the _training data_ for the classification problem, often represented as the set of tuples $(x_1, \ell_1), \ldots (x_n, \ell_n)$ where $\ell_i = f(x_i)$ are the training labels.
Our aim is to approximate the values of $f$ with probability distributions on $L$ in a way that generalizes to vectors in $\R^d$ outside of $S$.

To be more precise, we seek to approximate $f$ with a continuous map

$$F \colon \R^d \to \mathcal{P}_L \subseteq \R^k$$

where $\mathcal{P}_L$ denotes the space of probability distributions on $L$.
Key question: what should it mean for $F$ to be a good approximation of $f$?

On one hand, we want $F$ to accurately reproduce $f$ with high probability, meaning $\P(F(x) = \ell)$ should be large if $\ell = f(x)$ and small otherwise, for $x \in S$.
On the other hand, we want $F$ to generalize $f$ well on vectors outside of $S$, in the sense that $F(y)$ should agree with $f(x)$ with high probability if $y$ is close to $x$.
It is not always obvious how to reconcile these two desires, particularly if $f$ takes distinct values on nearby inputs.

In this post we will show how information theory can help us resolve this tension, and how this perspective leads naturally to the theory of neural networks.

## Relative entropy

The information theoretic input
