---
layout: post
permalink: /math/conditional-probability-1/
title: Conditional Probability I - Bayes' Rule
abstract: The goal of mathematical modelling is to make predictions based on limited data. The Bayesian approach to this problem is to organize the data we have into a probabilistic model which is "most likely" to explain it. This post introduces Bayes rule and the language of conditional probability in which it is expressed; subsequent posts will explore the surprising subtleties of this language in more detail.
level: medium
date: 2017-05-31
categories: stage
comments: true
---

You are given a coin, and you are told that it has a bias in favor of heads: every time you flip the coin there is a 60% chance it will come up heads instead of 50% for a fair coin.
What is the probability that after flipping the coin ten times you will see an even split of five heads and five tails, in spite of the bias?
This sort of question can be answered using the standard binomial distribution: if a binary event occurs with probability $p$ and $n$ independent trials are performed then the probability that the event will occur $k$ times is given by:

$$
\binom{n}{k} p^k (1-p)^{n-k}
$$

There is good intuition for each term in this expression: $p^k$ is the probability of $k$ successes, $(1-p)^{n-k}$ is the probability of $n-k$ failures, and the coefficient $\binom{n}{k}$ ensures that the successes and failures can occur in any order.
Applying this formula to the coin flip example above, wherein $n = 10$, $k = 5$, and $p = 0.6$, the probability is:

$$
\binom{10}{5} (0.6)^5 (0.4)^5 \approx 0.2
$$

In other words there is about a 20% chance that the mildly unfair coin will exhibit the expected statistics of a fair coin after ten flips.
A similar calculation, with $p = 0.5$, shows that an even split of five heads and five tails occurs for a fair coin about 24.6% of the time, and indeed the statistics of a fair coin don't differ all that much from the statistics of a coin with a 60% bias in favor of heads (at least for a small number of coin flips).

This is a bit disturbing for those concerned with building mathematical models from data.
In real world modelling problems one typically has access only to the behavior of a system in experiments - in the example above, this corresponds to the number of times heads comes up after flipping the coin a bunch of times.
The underlying model - the actual probability that the coin will turn up heads - is to be inferred from the experimental data.

It is intuitively clear that doing more experiments (more coin flips) will lead to a better understanding of the underlying model, but there are a lot of questions that need to be answered to turn this intuition into practical inference methodology.
How many experiments are needed to to nail down a model with a given degree of confidence?
If new observations appear to conflict with our model, how can we update the model accordingly?
How can we incorporate prior understanding of the model (e.g. via metalurgical analysis of the coin) into our calculations?

Bayesian statistics is among the more influential and successful approaches to the sort of statistical inference problem described here.
Whereas ordinary probability theory asks questions like "What is the probability that we will observe five heads out of ten flips?", Bayesian statistics asks "Given that we observed five heads in ten flips, what is the probability that the coin was fair?"
As such it relies heavily on the language of conditional probability; indeed, this language was developed in no small part to accommodate Bayesian analysis.

## Conditional Probability and Bayes' Rule

We would like to answer questions of the form "What is the probability of a model given data?" and so we will begin by examining what it means to compute the probability of one event given another.
This is the language of conditional probability.
Given two events $A$ and $B$, we define the probability of $A$ given $B$ by the formula:

$$
\P(A \vert B) = \frac{\P(A \cap B)}{\P(B)}
$$

Note: this definition assumes that the event $B$ has nonzero probability.
This is actually quite unsatisfying: a lot of important problems require conditioning against events of probability zero, but doing so requires a much more sophisticated definition.
But for now we will get by conditioning against events of positive probability - this is good enough to handle problems involving discrete probability spaces, for instance.

As an immediate consequence of the definition, we have:

$$\P(A \cap B) = \P(A \vert B)\P(B)$$

But the left-hand side is symmetric in $A$ and $B$, so we also get:

$$\P(A \cap B) = \P(B \vert A)\P(A)$$

Combining the previous two equations gives:

$$
\P(A \vert B) = \frac{\P(B \vert A)}{P(B)} \P(A)
$$

This formula - little more than a rewrite of the basic definitions - is actually quite remarkable: it expresses the probability of $A$ given $B$ in terms of the probability of $B$ given $A$.
It is called *Bayes' rule*, and it is fundamental to modern statistical inference.

Bayes' rule can be interpretted as follows.
The event $A$ represents a collection of possible models which may or may not explain a set of observations represented by the event $B$.  For instance, $B$ might be an observed sequence of coin flips and $A$ might be the event 
