---
layout: post
permalink: /ds/info-theory/max-entropy/
title: The principle of maximum entropy
abstract: What is the correct way to select a prior in Bayesian modeling? This is a deep question which leads naturally to the principle of maximum entropy, a fundamental tool in statistics, machine learning, and beyond.
date: 2022-01-27
categories: [math, data-science]
comments: true
---

*Problem:* You encounter an apparently normal six-sided die, but you are told that the average roll is $4.5$. The die must be weighted, or else its average roll would be $3.5$. What is your best estimate for the probability that the face $2$ is rolled?

## Bayesian updating

Bayes' rule is one standard approach to the task of statistically modeling a system with limited information.
It relates the random variable $H$ representing a hypothesis about the behavior is the system to the random variable $D$ representing empirical data about how the system behaves in practice.
The relationship can be expressed as follows:

$$\P(H | D) = \frac{\P(H) \P(D | H)}{\P(D)}$$

- $\P(H)$ is called the _prior_: it is the probability that our beliefs about the system our correct before we have seen any data
- $\P(D | H)$ is called the _likelihood model_, or sometimes just the _likelihood_: it is the probability that a given experimental outcome would be observed assuming our beliefs are correct
- $\P(H | D)$ is called the _posterior_ distribution: it is the probability that our beliefs are correct given the data we have observed

The Bayesian approach to statistical analysis proceeds as follows:

1. Choose a prior using whatever knowledge you may have about the system
2. Construct a likelihood model using standard techniques in probability theory
3. Perform an experiment, compute the posterior distribution, and use this distribution as your new prior

Often we don't care about the full posterior distribution, and we just want the model to which the posterior assigns the highest probability.
In this case we don't need to compute $\P(D)$; it is enough to know that $\P(H | D)$ is proportional to $\P(H) \P(D | H)$.

<div class="example">
You have a die which you know to either be fair or to be weighted so that the face $5$ comes up one third of the time and the face $6$ comes up half of the time.
You roll the die $10$ times and see $5$ come up twice and $6$ come up $3$ times.
Is the die more likely to be fair or weighted?
</div>
<div class="solution">
Let $F$ denote the belief that the die is fair, and let $W$ denote the belief that the die is weighted, so that $\P(F) = \P(W) = \frac{1}{2}$.
Let $p$ denote the probability that $5$ comes up, let $q$ denote the probability that $6$ comes up, and let $r$ denote the probability that any other face comes up.
Then the probability of the observed outcome is given by the multinomial distribution:

$$\frac{10!}{2! 3! 5!} p^2 q^3 r^5$$

This formula computes $\P(D | F)$ via the choices $p = q = \frac{1}{6}$, $r = \frac{2}{3}$, and it computes $\P(D | W)$ via the choices $p = \frac{1}{3}$, $q = \frac{1}{2}$, and $r = \frac{1}{6}$.
So we have:

$$\P(F | D) = \frac{\P(F) \P(D | F)}{P(D)} = \frac{1}{\P(D)} \left( \frac{1}{2} \frac{10!}{2! 3! 5!} \frac{1}{6^2} \frac{1}{6^3} \frac{2^5}{3^5} \right)$$

$$\P(W | D) = \frac{\P(W) \P(D | W)}{P(D)} = \frac{1}{\P(D)} \left( \frac{1}{2} \frac{10!}{2! 3! 5!} \frac{1}{3^2 2^3 6^5} \right)$$

Using a calculator, the second expression is slightly larger than the first, so the die is slightly more likely to be weighted.
</div>

## Asymptotic likelihood

To summarazing our problem solving strategy in the example above: we translated our hypothesis into a statement about the probabilities for the faces, and used the multinomial distribution to express the probability of an observed event in terms of the face probabilities.
