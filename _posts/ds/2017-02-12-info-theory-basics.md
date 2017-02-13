---
layout: post
title: Information Theory - the Basics
abstract: Claude Shannon invented information theory in the 1940's to answer practical questions about the design of communication systems.  Today it is part of the foundation of computational linguistics and machine learning, as well as the theory of dynamical systems and the very idea of computation itself.
date: 2017-02-12
categories: math data_science
---

Claude Shannon's 1948 paper [*A Mathematical Theory of Communication*][1] is undoubtedly near the top of my list of most important mathematics papers.  (Not just because Shannon is an alumnus of the University of Michigan math department.)  It isn't that theorems in the paper are particularly deep; indeed, it is probably accessible to anyone with some background in basic probability.  But theorems aren't really the most important products of mathematical labor: true progress is made through the creation of good definitions, and theorems and their proofs are just evidence that the right definitions have been chosen.  Shannon's achievement was to precisely formulate many of the key questions that arise from practical considerations in the design of robust communication systems, and in the process he invented a language that today's engineers and data scientists still use to extract usable signals from noisy data.

## Information and Communication
Shannon begins with a practical question: how can one take a message in one location and reproduce it accurately and efficiently in another location?  The scope of this question, and hence the applicability of his results, is extremely broad; it includes everything from spoken human language to modern encrypted communication protocols.  The medium Shannon had in mind was the telegraph, a system capable of transmitting sequences of characters over great distances albeit at a limited rate and with a high risk of error.  Vigorous research had already been done (in part by Shannon himself) on how to tackle the engineering challenges associated with the telegraph, but Shannon's paper was one of the first attempts to explore opportunities to optimize the message itself.  Sure there was a more or less hard limit on the rate that *characters* could be transmitted, but what really mattered was the rate at which *information* was transmitted.  How efficiently can the English language organize characters into sequences which contain information?

To begin, Shannon needed a mathematical model for how messages are constructed.  Shannon introduced what would later be known as the "Stochastic grammar" model in the field which would later be called "computational linguistics".  I don't know if this model predated *A Mathematical Theory of Communication*, but if not then the fact that it is only the second most important innovation in the paper is a bit staggering.  In any event, the model is quite simple: it assumes that given a sequence of characters, the next character is drawn from a probability distribution which depends only on the last $n$ characters for some fixed value of $n$.  For instance if the last two characters are "th" then the next character is much more likely to be 'e' than, say, 'd' or 'x'.  Thus Shannon treated language as a Markov chain in which the states are *$n$-grams* of characters and the transitions between states correspond to appending a new character to the end of the $n$-grams.

Shannon noted that one can play the same game with sequences of words instead of characters, and he generated a random sentence according to the word transition probabilities for English:

<center>
THE HEAD AND IN FRONTAL ATTACK ON AN ENGLISH WRITER THAT THE CHARACTER OF THIS POINT IS THEREFORE ANOTHER METHOD FOR THE LETTERS THAT THE TIME OF WHO EVER TOLD THE PROBLEM FOR AN UNEXPECTED.
</center>

It was only within the last decade or so that this technique for artificially generating language was supplanted as the state-of-the-art.

## Measuring Information
Armed with a reasonable model for the production of language, Shannon turned to the problem of measuring the rate at which it produces information.  I am going to express Shannon's idea using language and intuition invented later by Myron Tribus, but this description differs only cosmetically from Shannon's.  The basic idea is quite straightforward:

<center>
*The amount of information contained in a signal corresponds to how surprising it is.*
</center>

Stepping back from the theory of communication, let us imagine how we would produce a measurement $S(E)$ of how "surprised" we are that an event $E$ occurred.  Such a measurement ought to have two properties:

1. Low probability events are more surprising than high probability events, so $S(E_1) < S(E_2)$ whenever $P(E_1) > P(E_2)$.
2. If two independent events are equally surprising, then it is twice as surprising when both of them happen as it is when either one of them happens.  More generally, $S(E_1 \cap E_2) = S(E_1) + S(E_2)$ whenever $E_1$ and $E_2$ are independent.

<div class="proposition">
If the surprise function $S$ satisfies the two conditions above then $S(E) = -C \log P(E)$ for some constant $C$.
</div>








[1]: http://math.harvard.edu/~ctm/home/text/others/shannon/entropy/entropy.pdf "Shannon's original paper"
