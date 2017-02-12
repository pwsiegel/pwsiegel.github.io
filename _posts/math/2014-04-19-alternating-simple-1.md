---
layout: post
title: The Alternating Group is Simple I
date: 2014-12-09
---

This past week I covered an abstract algebra course at Columbia, and I decided to prove that the alternating group $A_n$ is simple.  I in fact did this in the same algebra class last year, but in the intervening months I almost entirely forgot how the argument goes.  So I decided to write it up here while it's still fresh in my mind.  It's a very nice - and fairly elementary - little application of some important ideas in group theory.  In this post I'm going to give some background and explain the significance of the simplicity of $A_n$, and in the sequel I will go through the proof.

## The Alternating Group
I am forced to assume that the reader is comfortable with basic group theory, but I'll begin by reviewing some of the key ideas.  Recall that the symmetric group $S_n$ is the group of permutations of $n$ symbols $1, 2, \ldots, n$.  A _transposition_ is a permutation which swaps exactly two symbols and leaves the others fixed; it is not hard to see that any permutation can be expressed as the product of transpositions.  A permutation is said to be _even_ (respectively, _odd_) if it can be written as the product of an even (respectively, odd) number of transpositions.

<div class="definition">
The _alternating group_ $A_n$ is the subgroup of $S_n$ consisting of all even permutations.
</div>

$A_n$ is a normal subgroup of $S_n$ of index $2$; the objective of this series of posts is to prove that $A_n$ is simple for $n \geq 5$, meaning its only normal subgroups are itself and the trivial group.  The significance of this property is that if a group $G$ has a normal subgroup $H$ then one can form the quotient group $G/H$, and often one can infer properties of $G$ from properties of $H$ and $G/H$.  So simple groups are in a sense the "atoms" from which all other groups are built, though it should be noted that $H$ and $G/H$ alone do not uniquely determine $G$.

## The Classification of Simple Groups
<a href="http://en.wikipedia.org/wiki/Classification_of_finite_simple_groups">Classifying all finite simple groups</a> was one of the great achievements of 20th century mathematics, and like many great mathematical achievements it went almost completely unnoticed by the rest of the world.  The classification theorem asserts that all finite simple groups fit into a few infinite families (one of which is the family of alternating groups) with precisely 26 exceptions, the so-called _sporadic simple groups_.  A shameless plug: when I was an undergraduate I did an REU project with Igor Kriz which involved making little computer games based on the sporadic simple groups; later we wrote a <a href="http://www.scientificamerican.com/article/puzzles-simple-groups-at-play/">Scientific American article</a> about them.  

In any event, the classification program took decades to complete and spans thousands of pages written by dozens of mathematicians, and its completion seems to have essentially killed off finite group theory as an active area of research (though from what I understand there are lots of open problems in representation theory for finite groups).  Given how monumental the effort was and how few people are still working in finite group theory, I worry that in a few decades all the experts will retire or die and there will be nobody left who understands the proof.  It's a good illustration of the principle that mathematicians tend to care much more about questions than answers.

## The Alternating Group and Galois Theory
Aside from their role in the classification program, the alternating groups play a crucial role in the theory of polynomial equations.  Indeed, the very notion of a group was invented to understand the structure of solutions to polynomial equations, and the group $A_5$ is the star of the show.

Everyone learns in high school algebra that there is a formula for the roots of a quadratic equation $ax^2 + bx + c = 0$:

$x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}$

Less well known is that there is also a <a href="http://en.wikipedia.org/wiki/Cubic_formula#General_formula_for_roots">cubic formula</a> and <a href="http://en.wikipedia.org/wiki/Quartic_formula">quartic formula</a> for degree three and four equations, respectively.  These formulas date back to the 16th century, and it was a frustratingly difficult open problem to find a formula for the roots of a polynomial equation of degree five.  It wasn't until the 19th century that Abel and Galois independently realized that no such formula exists!  Abel's proof came first, but I don't know what it was; Galois' argument is the one that survived.  Here is a brief sketch.

Galois' key idea was to focus on the symmetries exhibited by the roots of a polynomial equation.  More precisely, he considered their symmetries relative to the rational numbers; there are well-known techniques for finding rational roots of polynomials, so he was interested in the structure of the irrational roots.  Let's look at a couple examples:

* The roots of $x^2 - 2$ are $\pm \sqrt{2}$, so you can get from one root to the other by multiplying by $-1$.  Thus the cyclic group $C_2$ naturally exhibits the symmetries of the roots.
* The roots of $x^4 - 1$ are $i$, $-i$, $1$, and $-1$.  Notice that you can cycle through the roots just by looking at powers of $i$: $i^0 = 1$, $i^1 = i$, $i^2 = -1$, $i^3 = -1$ and $i^4 = 1$.  Thus the symmetries of the roots are given by the cyclic group $C_4$.
* The roots of $(x^2 - 2)(x^2 - 3)$ are $\pm \sqrt{2}$ and $\pm \sqrt{3}$.  The roots $\sqrt{2}$ and $-\sqrt{2}$ are interchangeable, as are $\sqrt{3}$ and $-\sqrt{3}$, but over the rational numbers there is a sort of asymmetry between $\sqrt{2}$ and $\sqrt{3}$.  Thus the symmetry group is $C_2 \times C_2$.

Of course, one can make all this precise using the language of field extensions.  The upshot is that the symmetry groups help characterize what it means to find a formula for the roots of a polynomial equation.  As in the example above, equations of the form $x^n - a = 0$ have cyclic symmetry group $C_n$.  So if the quintic formula had the form $\sqrt[5]{a + \sqrt[3]{b}}$, for instance, then the symmetry group could be decomposed into a $C_5$ part and a $C_3$ part corresponding to the fifth root and cube root, respectively.  More precisely, a polynomial equation can be solved by radicals if and only if its symmetry group $G$ has a decomposition

$G = G_0 \supseteq G_1 \supseteq \ldots \supseteq G_n = \{1\}$

where $G_i$ is a normal subgroup of $G_{i-1}$ and $G_{i-1}/G_i$ is cyclic.  Groups with this property are said to be _solvable_ due to the connection with solving equations.  

Now, there exist polynomials of degree $5$ whose symmetry group is the full symmetric group $S_5$ (in fact there are many).  $S_5$ contains $A_5$ as a normal subgroup with quotient $C_2$, but once we have proved that $A_5$ is simple we will know that it is not solvable: it has no nontrivial normal subgroups whatsoever, let alone one with a cyclic quotient.  This argument shows that there cannot be a general formula in the spirit of the quadratic, cubic, or quartic formulas, but it also shows even more: it gives a criterion (solvability of the symmetry group) to determine when there is a formula for the roots of a specific polynomial.
