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
The framework is an application of the principle of maximum entropy laid out in one of my [previous posts][1]




Let $F \colon \R^d \to L$ be the unknown function, where $L$ is a finite set of labels.
Let $S \subseteq \R^d$ of inputs on which the values of $F$ are known, and let $f \colon S \to L$ be the restriction of $F$ to $S$.
We'll refer to $f$ as the _training data_ for the supervised classification task.

Certainly $f$ has infinitely many possible extensions to all of $\R^d$, so we cannot say with any certainty what the value of $F$ is on any point outside of $S$.
To accommodate this uncertainty, we aim to approximate the values of $F$ with probability distributions on the label set $L$ as follows.

Embed $L$ in $\R^k$, $k = \abs{L}$, by identifying the elements of $L$ with the indicator vectors $\delta_j = (0, 0, \ldots, 1, \ldots, 0)$ - this is the so-called _one-hot_ encoding of $L$.
Let $P^k$ denote the space of probability vectors in $\R^k$.
Our goal is to construct a continuous map

$$p \colon \R^d \to P^k$$

which approximates $F$ as well as possible.

This is progress, but we are still left with some philosophical problems.
How do we measure how well a given $p$ approximates $F$, particularly given that we can't observe $F$ directly except on $S$?

## The classifier of maximum entropy

We confronted an analogous question in my post on the [principle of maximum entropy][1].
In that context we were asked to construct a probability distribution which best models an ordinary-looking six sided die which is known to be biased so that its average roll comes up $4.5$ instead of $3.5$.
We argued that the best choice - meaning, the choice which makes as few unjustified assumptions as possible - is the distribution $p$ which maximizes the entropy function $H(p)$ (or equivalently minimizes the relative entropy given the uniform distribution).

So in our setting we shall seek the function $p$ of maximum total entropy, which we should define as the integral of $H(p(x))$ over $\R^d$.
I believe that it is possible to carry this analysis out using the calculus of variations, but I have not yet worked this out.
Instead, let us define the total entropy to be the sum of $H(p(x))$ over $x$ in the sample set $S$:

$$H(p) = \sum_{x \in S} H(p(x)) = -\sum_{x \in S} \sum_{j=1}^k p_j(x) \log p_j(x)$$

where $p_j$ denotes the $j$th component function of $p$, using the one-hot encoding of $L$ chosen above.
Strictly speaking this optimization criterion will only fix the values of $p$ on $S$ rather than all of $\R^d$, but we will discover a formula for $p$ which naturally extends to all of $\R^d$.

Of course, with no further restrictions the optimal $p$ is simply the function which assigns the uniform distribution to every $x \in S$, since this is the distribution of maximum entropy.
We need a constraint which ensures that $p$ approximates $F$ in some loose sense.



[1]: {{ site.baseurl }}{% post_url /ds/info-theory/max-entropy %} "Gibbs' Inequality"
