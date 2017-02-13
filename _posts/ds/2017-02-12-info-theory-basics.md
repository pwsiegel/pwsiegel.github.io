---
layout: post
title: Information Theory - the Basics
abstract: Claude Shannon invented information theory in the 1940's to answer practical questions about the design of communication systems.  Today it is part of the foundation of computational linguistics and machine learning, as well as the theory of dynamical systems and the very idea of computation itself.
date: 2017-02-12
categories: math data_science
---

Claude Shannon's 1948 paper [*A Mathematical Theory of Communication*][1] is undoubtedly near the top of my list of most important mathematics papers.  (Not just because Shannon is an alumnus of the University of Michigan math department.)  The paper doesn't contain the proofs of any particularly deep theorems; indeed, it is probably accessible to anyone with some background in basic probability.  But theorems aren't really the most important products of mathematical labor: true progress is made through the creation of good definitions, and theorems and their proofs are just evidence that the right definitions have been chosen.  Shannon's achievement was to precisely formulate many of the key questions that arise from practical considerations in the design of robust communication systems, and in the process he invented a language that today's engineers and data scientists still use to extract usable signals from noisy data.

## Communication as a Stochastic Process
Shannon arrived to the theory of communication via his work in cryptography at Bell Labs for the war effort in the 1940's.  The basic problem of communication is simple: one aims to take a message produced in one location and reproduce it with some minimum level of precision in another location.  This encompasses low-tech communication systems like spoken language, which uses sounds produced by our mouths to reproduce a message created in one person's mind in the mind of another, or high-tech systems like a telegraph network in Shannon's time or the internet in ours.

To simplify matters somewhat, Shannon restricted his attention to discrete information sources in which each message is a finite sequence of characters chosen from a finite alphabet.  Written English (or any other human language) provides the prototypical example of such a system.  Shannon modeled such an information source as a *Markov chain* (or *stochastic process*), i.e. a finite set $S_1, \ldots S_N$ of "states" together with probabilities $p_{ij}$ of transitioning between states.  Specifically, he treated a sequence of $n$ characters drawn from the alphabet (an *$n$-gram*) as a state, and the transition probabilities between states represented predictions for the next letter in the sequence.  Let's look at how this plays out in a couple examples:

* **$1$-grams.** A $1$-gram is just a single character, so there is one state for every character in the alphabet.  For instance, there would be $27$ states for English language messages - 26 letters and a whitespace character.  The probability of a transition from, say, the letter 't' to the letter 'h' simply represents the chance that the next letter will be 'h' given that the current letter is 't'.  So the transition probabilities can be calculated by looking at the frequencies of $2$-grams (e.g. 'th') in the English language.
* **$2-grams.** A $2$-gram is a pair of characters, so if the alphabet has $k$ letters then there are $k^2$ states.  Now the transition probabilities correspond to predictions of the next character given the current character and the last one, so they can be calculated by looking at the frequencies of $3$-grams in the language.

The same sort of model can be constructed in which the states are words instead of characters; here is an example that Shannon gave of a random sentence produced using only the word transition probabilities for English:

$$\text{THE HEAD AND IN FRONTAL ATTACK ON AN ENGLISH WRITER THAT THE CHARACTER
OF THIS POINT IS THEREFORE ANOTHER METHOD FOR THE LETTERS THAT
THE TIME OF WHO EVER TOLD THE PROBLEM FOR AN UNEXPECTED.}$$

One can envision randomly generating comprehensible English sentences from a suitably parametrized model of word $n$-grams, and indeed this sort of technique was supplanted as the state-of-the-art for machine learning systems which work with language data only within the past 5-10 years.

## Information and Entropy
I'm not actually sure if *A Mathematical Theory of Communication* is the origin of the stochastic model of language described above or even if it was invented by Shannon, but it was destined to be foundational in the field which would later be known as computational linguistics.  Even so, it was at best the second most important idea in the paper.  After laying out the basic model, Shannon asked a simple question: how much information does the model produce?

For Shannon, information is the product of *choice*.  There are many meaningless sentences which are more or less recognizable as legitimate English - Shannon's randomly generated sentence above is a good example - so information is found in the choices that one makes beyond what is forced by the rules of the language.  In other words, a source which is producing actual information will "surprise" us a lot more by generating unlikely events than a source which is not.

Stepping back from the theory of communication, let us imagine how we would produce a measurement $S(E)$ of how "surprised" we are that an event $E$ occurred.  Such a measurement ought to have two properties:

1. Low probability events are more surprising than high probability events, so $S(E_1) < S(E_2)$ whenever $P(E_1) > P(E_2)$.
2. If two independent events are equally surprising, then it is twice as surprising when both of them happen as it is when either one of them happens.  More generally, $S(E_1 \cap E_2) = S(E_1) + S(E_2)$ whenever $E_1$ and $E_2$ are independent.

<div class="proposition">
If the surprise function $S$ satisfies the two conditions above then $S(E) = -C \log P(E)$ for some constant $C$.
</div>








[1]: http://math.harvard.edu/~ctm/home/text/others/shannon/entropy/entropy.pdf "Shannon's original paper"
