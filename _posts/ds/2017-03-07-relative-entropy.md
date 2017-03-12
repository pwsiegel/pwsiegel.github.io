---
layout: post
permalink: /relative-entropy/
title: Relative Entropy
abstract: Relative entropy - also known as the *Kullback-Leibler divergence* - measures the information gained by replacing one random model with another.  This makes it an important tool in applications where one wishes to update a prior model in response to new observations.
level: hard
date: 2017-03-05
categories: stage
comments: true
---

This is my third post in a series of unknown length on entropy and information theory.  The [first post][1] was an introduction to the subject based on Shannon's original paper on the subject *A Mathematical Theory of Communication*.  The [second post][2] examined the foundations of the subject and found them a bit wanting; I presented part of Jayne's argument that the standard definition of entropy for continuous distributions is unsatisfactory, in spite of the fact that it continues to show up in serious applications.

In this post I will take a second pass at the foundations and argue that the information content of a probability distribution should be measured *relative to another distribution*.  This perspective is captured by the *Kullback-Leibler divergence*, a tool which resolves my theoretical uneasiness without disturbing the conceptual and computational successes of Shannon's original version of the theory.  That said, this post will in places involve further escalation in technical depth.

## Relative surprise

In my previous posts I motivated the definition of information entropy using the language of "surprise": a signal carries information precisely if it tells us something that we didn't expect.  But if we take this metaphor seriously then we quickly encounter a problem: surprise, as we normally understand it, is relative.  An ancient observer witnessing a solar eclipse for the first time might be so shocked by the experience that he or she would invent a religion to explain what could only have been a message from the divine, while a modern astronomer equipped with a precise model of the solar system would be more surprised if such an event failed to occur as predicted.

In other words surprise, and correspondingly information, lives not in the observation of an event but in the revision of one's underlying model in response to new data.  To formulate this properly, let us suppose we are repeatedly flipping a coin which we presume to be fair, so that the distribution of the $i$th coin flip is given by

$$Q(C_i = H) = Q(C_i = T) = \frac{1}{2}$$

Suppose that after the first 100 coin flips 90 of them come up heads; this is quite surprising given our model $Q$, so we build a new model $P$:

$$P(C_i = H) = \frac{9}{10},\: P(C_i = T) = \frac{1}{10}$$

We can't prove that our new model $P$ is correct; after all a fair coin could have produced an abnormal number of heads through random chance.  But nevertheless the fact that the experiment caused us to change our model is an indication that we took the event as a source of information about the underlying structure of the coin.  How much information did we gain?  Or in other words, just how surprised must we have been to make such a drastic change to our model?  If we work through the axioms for surprise articulated in my [last post][2] on entropy (but this time in the relative setting) the right formula is:

$$\log P(\omega) - \log Q(\omega) = \log \frac{P(\omega)}{Q(\omega)}$$

where $\omega$ is the event above.  Just as we defined entropy to be average surprise, the main quantity of interest is the average *relative* surprise:

<div class="definition">
Let $P$ and $Q$ be discrete probability measures on the same measurable space $(\Omega, \Sigma)$, and suppose that $P(\omega) = 0$ whenever $Q(\omega) = 0$.  The *Kullback-Leibler divergence* of $P$ from $Q$ is defined to be:

$$D_{KL}(P||Q) = \E_P \left( \log \frac{P}{Q} \right) = \sum_\omega P(\omega) \log \frac{P(\omega)}{Q(\omega)}$$

</div>
