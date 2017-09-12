---
layout: post
permalink: /math/basic-bayes/
title: Basic Bayes
abstract: The goal of mathematical modelling is to make predictions based on limited data. The Bayesian approach to this problem is to organize the data we have into a probabilistic model which is "most likely" to explain it. This post introduces Bayes rule and the language of conditional probability in which it is expressed.
level: medium
date: 2017-09-03
categories: stage
comments: true
---

You are asked to referee a dispute by flipping a coin, but the only coin you can find is a bit damaged and you're not sure if it's fair.
You flip it ten times and record that it comes up heads six times.
What can you conclude?

Six out of ten heads wouldn't be completely unreasonable for a fair coin - intuitively, 60 heads out of 100 flips or 600 heads of 1000 flips would be much more damning evidence against the hypothesis that the coin is fair.
But regardless of the size of the experiment one would ideally like a systematic way to infer something about the underlying structure of the coin based on past observations.
This is a classical example of an _induction problem_ in mathematical modelling: how can one build a probabilistic model of a system based on empirical data?

## Bayes' rule

If there is a standard way to solve induction problems in statistics, it is undoubtedly based on Bayes' rule.
Bayesian analysis proceeds as follows:

1. Construct a "reasonable" space of possible underlying models for the system.
2. Solve the _deduction problem_ for each model: compute the probability of the observed data given the model. 
3. Express the probability of each model given the observed data in terms of the probability of the observed data given the model, and choose the model with the highest probability.

We'll work through all three steps in the context of the coin flip example, but first let us discuss a bit of basic mathematics necessary in step 3.

At the heart of the matter is the ability to express the probability of an event $A$ given an event $B$ in terms of the probability of $B$ given $A$.
So let us begin with a probability space $(\Omega, \Sigma, \P)$, and let $A, B$ be events in $\Sigma$.
Recall that the _conditional probability_ of $A$ given $B$ is by definition:

$$\P(A \vert B) = \frac{\P(A \cap B)}{\P(B)}$$

assuming $\P(B) \neq 0$.
(There are ways to handle the case where $\P(B) = 0$, but some surprisingly sophisticated mathematics is required and so we will defer it to a future post.)

There is of course a similar formula for the probability of $B$ given $A$, and rearranging it we obtain $\P(A \cap B) = \P(B \vert A) \P(A)$.
This gives:

$$
\P(A \vert B) = \frac{\P(B \vert A) \P(A)}{\P(B)}
$$

This formula, which expresses the probability of $A$ given $B$ in terms of the probability of $B$ given $A$, is called _Bayes' rule_.
Though Bayes' rule is quite simple, it is difficult to overstate its importance.
It is important enough, in fact, to merit the introduction of a bunch of jargon:

- $\P(A)$ is called the _prior_: in the coin flip example, $A$ would represent a collection of possible models for the coin's behavior, and $\P(A)$ would represent our initial assumption about what models are reasonable before we look at coin flip data.
For instance, if the coin isn't too damaged then we might build into our prior the assumption that a model in which tails never comes up is extremely unlikely.

- $\P(A \vert B)$ is called the _posterior_: in the coin flip example, this would represent the probability that one of the models in $A$ is correct after we have accounted for coin flip data (represented by $B$).

- $\frac{\P(B \vert A)}{\P(B)}$ is called the _support_ that $B$ provides for $A$: the support determines how we modify the prior to obtain the posterior.

## 
