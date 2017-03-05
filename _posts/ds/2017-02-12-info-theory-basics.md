---
layout: post
permalink: /info-theory-basics/
title: Information Theory - the Basics
abstract: Claude Shannon invented information theory in the 1940's to answer practical questions about the design of communication systems.  Today it is part of the foundation of computational linguistics and machine learning, as well as the theory of dynamical systems and the very idea of computation itself.
date: 2017-02-12
categories: math data_science
---

Claude Shannon's 1948 paper [*A Mathematical Theory of Communication*][1] is undoubtedly near the top of my list of best mathematics papers of all time.  (Not just because Shannon is an alumnus of the University of Michigan math department.)  It isn't that theorems in the paper are particularly deep; indeed, they are probably accessible to anyone with some background in basic probability.  But theorems aren't really the most important products of mathematical labor: true progress is made through the creation of good definitions, and theorems and their proofs are just evidence that the right definitions have been chosen.  Shannon's achievement was to precisely formulate many of the key questions that arise from practical considerations in the design of robust communication systems, and in the process he invented a language that today's engineers and data scientists still use to extract usable signals from noisy data.

## Information and Communication
Shannon begins with a simple question: how can one take a message in one location and reproduce it accurately and efficiently in another location?  The scope of this question, and hence the applicability of his results, is extremely broad; it includes everything from spoken human language to modern encrypted communication protocols.  The medium Shannon had in mind was the telegraph, a system capable of transmitting sequences of characters over great distances albeit at a limited rate and with a high risk of error.  Vigorous research had already been done (in part by Shannon himself) on how to tackle the engineering challenges associated with the telegraph, but Shannon's paper was one of the first attempts to explore opportunities to optimize the message itself.  There may have been a more or less hard limit on the rate that *characters* could be transmitted, but what really mattered was the rate at which *information* was transmitted.  How efficiently can the English language organize characters into sequences which contain information?

To begin, Shannon needed a mathematical model for how messages are constructed.  Shannon introduced what would later be known as the "Stochastic grammar" model in the field which would later be called "computational linguistics".  I don't know if this model predated *A Mathematical Theory of Communication*, but if not then the fact that it is only the second most important innovation in the paper is a bit staggering.  In any event, the model is quite simple: it assumes that given a sequence of characters, the next character is drawn from a probability distribution which depends only on the last $n$ characters for some fixed value of $n$.  For instance if the last two characters are "th" then the next character is much more likely to be 'e' than, say, 'd' or 'x'.  Thus Shannon treated language as a Markov chain in which the states are *$n$-grams* of characters and the transitions between states correspond to appending a new character to the end of the $n$-grams.

Shannon noted that one can play the same game with sequences of words instead of characters, and he generated a random sentence according to the word transition probabilities for English:

<center><p>
THE HEAD AND IN FRONTAL ATTACK ON AN ENGLISH WRITER THAT THE CHARACTER OF THIS POINT IS THEREFORE ANOTHER METHOD FOR THE LETTERS THAT THE TIME OF WHO EVER TOLD THE PROBLEM FOR AN UNEXPECTED.
</p></center>

This basic technique - with more sophisticated statistics - is to this day not far off from the state of the art in artificial language generation.

## Measuring Information
Armed with a reasonable model for the production of language, Shannon turned to the problem of measuring the rate at which it produces information.  I am going to express Shannon's idea using language and intuition invented later by Myron Tribus, but this description differs only cosmetically from Shannon's.  The basic idea is quite straightforward:

<center>
<p><em>A signal carries information if we would be surprised to learn that it was generated randomly.</em></p>
</center>

Stepping back from the theory of communication, let us imagine how we would produce a measurement $S(E)$ of how "surprised" we are that an event $E$ occurred.  Such a measurement ought to have three basic properties:

    1. $S(E)$ depends only on the probability $P(E)$ that the event occurs.
    2. Low probability events are more surprising than high probability events, so $S(E_1) < S(E_2)$ whenever $\P(E_1) > \P(E_2)$.
    3. If two independent events are equally surprising, then it is twice as surprising when both of them happen as it is when either one of them happens.  More generally, $S(E_1 \cap E_2) = S(E_1) + S(E_2)$ whenever $E_1$ and $E_2$ are independent.

<div class="proposition">
The function

$$S(E) = -\log_b \P(E)$$

(where the base $b$ is arbitrary) is a surprise function on any probability space.
</div>

Now, consider a discrete random variable $X$ with possible values $\br{x_1, \ldots, x_n\}$.  We can define a new random variable $I_X$ (the *information* of $X$) by $I_X(x_i) = S(X = x_i)$, and Shannon defines the *entropy* of X to be the expected value of this random variable:

$$H(X) = \E(I_X) = -\sum_i p_i \log p_i$$

where $p_i := \P(X = x_i)$ and we define $0 \log 0 = 0$.  In other words, the entropy of a random variable $X$ represents how surprising measurements drawn from $X$ are on average.  The base of the logarithm (omitted from the notation above) determines the units of entropy and is generally fixed at the outset; common choices include $2$, $10$, and $e$.

Returning to language, let $X$ be the random variable which represents the $n+1$st character in a message given the first $n$ characters (as per Shannon's stochastic model).  Let us work out the entropy for a couple of basic languages:

* Suppose $X$ takes the value 'a' with probability $1$ and all other characters with probability $0$.  A simple calculation shows that the entropy of this language is $0$, and this is the smallest possible value that $H$ can take.  Intuitively, we are never surprised by the output of $X$ because the output is always 'a'.
* Suppose $X$ outputs all 27 characters (English letters together with space) with equal probability.  Then:

$$H(X) = -\sum_{i=1}^{27} \frac{1}{27} \log \frac{1}{27} = \log 27$$

It can be shown that this is the largest possible value that $H(X)$ can take for a probability distribution $X$ on a $27$ point set.  Intuitively, we have no way to guess the next character so the output is always surprising.

The reader may already be familiar with the concept of entropy in statistical physics, which describes how "disordered" the system is.  The examples above suggest a connection between the information theoretic and physical notions of entropy: the languages with minimal entropy are the most structured (they always output the same character) while the language with maximal entropy is the most disordered (there is no way to predict the next character, even knowing the last $n$ characters).  Indeed, this connection can be formalized, but doing so is outside of the scope of this post.

## Applications
Let us return to Shannon's original problem: optimizing the transmission of information.  Shannon's fundamental theorem of communication is as follows:

<div class="theorem">
Consider a discrete information source with entropy $H$ transmitting over a medium with capacity $C$ (roughly: $C$ is the maximum amount of information per second that can be transmitted).  Then for any $\eps > 0$ there is an encoding of the information source which can be transmitted at an average rate of $\frac{C}{H} - \eps$ symbols per second.  Moreover there is no encoding which can be transmitted at an average rate higher than $\frac{C}{H}$ symbols per second.
</div>

The capacity can be regarded as an engineering constraint: there are physical limits to how much data a communication medium can handle and how quickly.  Meanwhile entropy can be regarded as a mathematical constraint: there are conceptual limits to how efficiently a message can be compressed without losing information.  Shannon's theorem says that these two constraints are sharp obstructions to the possibility of transmitting a message, and it validates his use of entropy as a measure of the intrinsic amount of information produced by a source.  (He also formulated a version of the theorem which controls the frequency of errors in case the transmission medium is noisy.)

Since 1948, Shannon's ideas have found applications in areas that he probably could not have envisioned - natural language processing, machine learning, artificial intelligence, etc.  The structures that Shannon's notion of entropy captures are found not just in human language but also a plethora of other data sources, and today data scientists use it to extract signals for a variety of classification and inference problems.  I will explore some of these applications in future posts, but I will list a few examples here.

* **The principle of maximum entropy.** Informally, this asserts that after all priors have been accounted for the distribution which best models a random process is the one which maximizes entropy.  The principle explains the effectiveness of many common models; for instance the normal distribution is the distribution of maximal entropy given a prescribed variance, and logistic regression is the classifier of maximal entropy for independent observations.
* **Feature selection.** Shannon also defined *relative* notions of information and entropy in his paper for the purpose of comparing the information content of jointly distributed random variables.  These tools can be used to extract small sets of features from data which describe different aspects of the data while still collectively explaining most of its variability.
* **Coding theory.** Though communication technology has advanced by leaps and bounds since 1948, the storage and bandwidth demands on communication networks have scaled comparably.  As a result it is just as important today as it was in Shannon's day to efficiently compress signals in a way which is robust to corruption and noise, and Shannon's framework still provides the theoretical and practical backbone for these tools.

## Fun aside
Consider a language with entropy $H$ (calculated according to Shannon's stochastic model) and let $H_{\max}$ denote the maximum possible entropy of any language which uses the same underlying alphabet.  The *redundancy* of the language is by definition $\frac{H_{\max} - H}{H_{\max}}$.  If the redundancy of a language is zero then nearly any sequence of characters in the language will carry meaning, and hence nearly any two dimensional grid of characters forms a legitimate crossword puzzle.  Shannon claims that English has redundancy of about .5, and moreover:

> A more detailed analysis shows that if we assume the constraints imposed by the language are of a rather chaotic and random nature, large crossword puzzles are just possible when the redundancy is 50%. If the redundancy is 33%, three-dimensional crossword puzzles should be possible, etc.

So crossword puzzles were not an inevitable pastime - they are made possible by the information theoretic structure of language!


[1]: http://math.harvard.edu/~ctm/home/text/others/shannon/entropy/entropy.pdf "Shannon's original paper"
