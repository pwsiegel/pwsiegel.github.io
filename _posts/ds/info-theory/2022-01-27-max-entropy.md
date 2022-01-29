---
layout: post
permalink: /ds/info-theory/max-entropy/
title: The principle of maximum entropy
abstract: What is the correct way to select a prior in Bayesian modeling? This is a deep question which leads naturally to the principle of maximum entropy, a fundamental tool in statistics, machine learning, and beyond.
date: 2022-01-27
categories: [math, data-science]
comments: true
---

You wander into a sketchy casino in the wrong part of town, and you find yourself drawn to the craps table.
Before you can place a bet, a stranger takes you aside and warns you: one of the two dice is rigged, and instead of rolling an average of 3.5 like a fair six-sided die it instead rolls an average of 4.5.
With only that to go on, you need to estimate the probabilities for each pip so that you can adapt your betting strategy correctly.
The corrupted die looks perfectly normal - what should you do?

This is a fundamental problem in Bayesian statistics: how do you choose a set of prior assumptions about a random process with only a limited amount of information about it?
This is a surprisingly subtle and deep mathematics problem with far-reaching implications, particularly in statistics and machine learning.
I will follow an approach pioneered by physicist Edwin Jaynes, building on Josiah Gibb's foundational work in statistical physics and Claud Shannon's theory of information.

## Bayesian updating

When trying to model a system with limited information, Bayes' rule is a valuable guide.
It relates the random variable $M$ representing our beliefs about what models are likely or unlikely to explain the behavior of the system to the random variable $D$ representing empirical data about how the system behaves in practice.
The relationship can be expressed as follows:

$$\P(M | D) = \frac{\P(M) \P(D | M)}{\P(D)}$$

- $\P(M)$ is called the _prior_: it is the probability that our beliefs about the system our correct before we have seen any data
- $\P(D | M)$ is called the _likelihood model_, or sometimes just the _likelihood_: it is the probability that a given experimental outcome would be observed assuming our beliefs are correct
- $\P(M | D)$ is called the _posterior_ distribution: it is the probability that our beliefs are correct given the data we have observed

The Bayesian approach to statistical analysis proceeds as follows:

1. Choose a prior using whatever knowledge you may have about the system
2. Construct a likelihood model using standard techniques in probability theory
3. Perform an experiment, compute the posterior distribution, and use this distribution as your new prior

In step 3 we often don't need to bother with the term $\P(D)$ on the right hand side of Bayes' rule; just knowing the prior and the likelihood are enough to determine which model is most probable according to the posterior distribution, even if we don't know that distribution exactly.
