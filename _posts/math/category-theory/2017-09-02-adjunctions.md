---
layout: post
permalink: /category-theory/adjunctions/
title: Adjunctions
abstract: Adjunctions are among the most fundamental constructions in category theory, and in mathematics more broadly.  In this post we shall go carefully through the definition and some basic examples.  There isn't much computer science specifically in this post, save for a few remarks about the category of graphs at the end, but adjunctions will make crucial appearances in future posts.
level: hard 
date: 2017-09-02
categories: category-theory 
comments: true
---

The promise of category theory is that the right abstractions will help us extend our intuition for simple objects to nontrivial theorems about more complex objects.
The concept of an adjunction - along with a few other fundamental tools such as limits/colimits or the Yoneda lemma - are critical to actually fulfilling that promise.

Adjunctions are an abstraction of the concept of duality, wherein one translates a problem into a new context where it becomes simpler, solves it, and then translates the solution back.
This strategy is ubiquitous in algebra and geometry - in number theory, for instance it is standard practice to solve problems about integers by extending them to complex numbers.
And in computer science there are a variety of dualities embedded in the tools that one uses to construct complex transformations between data sets, and certain kinds of adjunctions emerge explicitly to organize these dualities (more on this in a future post).

That said, working with adjunctions requires great care and patience - there are a variety of different but equivalent definitions in common use, and all of them take some effort to parse.

## Review of Natural Transformations

Before diving into the definition of an adjunction, let us revisit the concept of a natural transformation, defined as follows:

<div class="definition">
Let $F$ and $G$ be functors from a category $\C$ to a category $\D$.
A **natural transformation** $\eta$ from $F$ to $G$ is a family of morphisms $\eta_C \colon F(C) \to G(C)$ in $\D$ parametrized by the objects $C$ of $\C$ which is compatible with the morphisms of $\C$ in the sense that: 

$$\eta_{C_2} \circ F(f) = G(f) \circ \eta_{C_1}$$

for every morphism $f \colon C_1 \to C_2$ in $\C$.
The morphism $\eta_C$ is called the **component** of $\eta$ at $C$.
</div>

There are a variety of different ways to manufacture new natural transformations from old; we will need two specific examples of this.

<div class="example">
Let $F$, $G$, and $H$ be functors from $\C$ to $\D$ and let $\eta \colon F \to G$ and $\theta \colon G \to H$ be natural transformations.  Then there is a new natural transformation 

$$\theta \circ \eta \colon F \to H$$

called the **composition** of $\theta$ and $\eta$ whose component at an object $C$ of $\C$ is given by composition of components:

$$(\theta \circ \eta)_C = \theta_C \circ \eta_C$$

</div>

<div class="example">
Let $\eta \colon F \to G$ be a natural transformation between functors $F, G \colon \C \to \D$ and let $H \colon \D \to \mathcal{E}$ be a functor.
Then there is a new natural transformation

$$
H \eta \colon H \circ F \to H \circ G
$$

whose component at an object $C$ of $\C$ is the morphism $H(\eta_C)$.

Similarly, if $E \colon \mathcal{B} \to \C$ is a functor then there is a natural transformation

$$
\eta E \colon F \circ E \to G \circ E
$$

whose component at an object $B$ of $\C$ is the morphism $\eta_{E(B)}$.
</div>

<div class="exercise">
Show that the natural transformations in the previous two examples - $\theta \circ \eta$, $H \eta$, and $\eta E$ - really are natural transformations.  (In other words, check the compatibility condition in the definition of natural transformation.)
</div>

## Definition of Adjunction

We are now ready to define the notion of an adjunction.
Informally, an adjunction between two categories is a pair of functors between them which are "almost inverses" in a certain precise sense.
Often adjunctions arise because one seeks a proxy for the inverse of a non-invertible functor, and the adjoint construction is "as close as possible".
For instance, the inclusion $I: \Z \to \Q$ of the integers in to the rational numbers is not invertible, but the pair $I, R$ where $R \colon \Q \to \Z$ is the "rounding down" function determines an adjunction between $\Z$ and $\Q$ (viewed as poset categories - see my post on [Galois connections][1] for more detail on this example).

<div class="definition">
Let $\C$ and $\D$ be categories.  An **adjunction** between $\C$ and $\D$ consists of a pair of functors $F \colon \D \to \C$ and $G \colon \C \to \D$ together with natural transformations

$$
u \colon 1_\D \to GF \quad \text{and} \quad v \colon FG \to 1_\C 
$$

which satisfy the following identities:

$$
vF \circ Fu = 1_F \quad \text{and} \quad Gv \circ uG = 1_G
$$

$F$ is said to be the **left adjoint** of $G$ and $G$ is said to be the **right adjoint** of $F$.  The natural transformations $u$ and $v$ are called the **unit** and **counit** of the adjunction, respectively.
</div>

Note that the symbol $1$ is used in two different ways: $1\_\C$ and $1\_\D$ are the identity functors for the categories $\C$ and $\D$, respectively, whereas $1_F$ and $1_G$ are the identity natural transformations for $F$ and $G$, respectively.

Let us take a moment to unravel the adjunction identities a little bit before moving on to an example.
The component of the natural transformation $vF \circ Fu$ at an object $D$ in $\D$ is by definition:

$$
(vF \circ Fu)_D = (vF)_D \circ (Fu)_D = v_{F(D)} \circ F(u_D)
$$

Similarly, for any object $C$ in $\C$ we have:

$$
(Gv \circ vG)_C = (Gv)_C \circ (uG)_C = G(v_C) \circ u_{G(C)}
$$

## Example: Products and Diagonals

In order to make things a little more concrete we shall construct a left adjoint for the familiar cartesian product functor and work through the definitions in some detail.
(This example has the added advantage of being quite fundamental - many more sophisticated constructions can be viewed as generalizations of this one.)

In this example we will work extensively with the category $\Set^2$ of pairs of sets (i.e. the product of the category $\Set$ with itself).
The objects of this category are pairs $(X, Y)$ of sets and the morphisms from $(X_1, Y_1)$ to $(X_2, Y_2)$ are pairs $(f, g)$ where $f \colon X_1 \to X_2$ and $g \colon Y_1 \to Y_2$ are functions.
The reader is warned to keep in mind the distinction between the pair of sets $(X, Y)$ and the product of sets $X \times Y$ - the former is an object in $\Set^2$ while the latter is an object in $\Set$.
In particular, $(X, Y)$ doesn't have "elements" - sets have elements, not pairs of sets!

Consider the product functor $P \colon \Set^2 \to \Set$ defined by $P(X,Y) = X \times Y$ on objects and $P(f,g) = f \times g$ on morphisms.  We will show that the "diagonal" functor $\Delta \colon \Set \to \Set^2$ defined by $\Delta(X) = (X, X)$ on objects and $\Delta(f) = (f, f)$ on morphisms is a left adjoint for $P$.
In order to do this we must construct a unit and a counit, i.e. natural transformations

$$
u \colon 1_\Set \to P \Delta \quad \text{and} \quad v \colon \Delta P \to 1_{\Set^2}
$$

which satisfy the appropriate axioms.

We begin with the counit.
$\Delta P$ is the functor $\Set^2 \to \Set^2$ which sends a pair of sets $(X, Y)$ to the pair $(X \times Y, X \times Y)$.
On the other hand $1_{\Set^2}(X, Y)$ is just $(X, Y)$.
So the component of the counit at $(X,Y)$ must be a morphism

$$
v_{(X,Y)} \colon (X \times Y, X \times Y) \to (X, Y)
$$

Define $v\_{(X,Y)}$ to be the pair $(\pi_1, \pi_2)$ where $\pi_1 \colon X \times Y \to X$ and $\pi_2 \colon X \times Y \to Y$ are the projection maps on to the first and second factor, respectively.

<div class="exercise">
Verify that $v$ as defined above is a natural transformation.
</div>

Now for the unit.
$1\_\Set$ of course sends a set $X$ to itself, while $P \Delta$ sends $X$ to $X \times X$.
So the component of the unit at $X$ must be a mapping

$$
u_X \colon X \to X \times X
$$

Simply define $u_X(x) = (x, x)$, the "diagonal function".

<div class="exercise">
Verify that $u$ as defined above is a natural transformation.
</div>

We conclude by showing that $u$ and $v$ give $P$ and $\Delta$ the structure of an adjoint pair.

<div class="proposition">
The diagonal functor $\Delta \colon \Set \to \Set^2$ is left adjoint to the product functor $P \colon \Set^2 \to \Set$ via the unit/counit pair $u$, $v$ defined above.
</div>
<div class="proof">
It remains only to verify the adjunction identities:

$$v \Delta \circ \Delta u = 1_\Delta \quad \text{and} \quad P v \circ u P = 1_P$$

Let us begin with the identity for $\Delta$.
The left- and right-hand sides are both natural transformations from $\Delta$ to itself, so the component of each side at a set $X$ is a morphism from $\Delta(X) = (X, X)$ to itself. 
We have:

$$
\begin{align*}
   (v \Delta \circ \Delta u)_X &= v_{\Delta(X)} \circ \Delta(u_X) \\
   &= v_{(X, X)} \circ (u_X, u_X) \\
   &= (\pi_1, \pi_2) \circ (u_X, u_X) \\
   &= (\pi_1 \circ u_X, \pi_2 \circ u_X)
\end{align*}
$$

For any point $x \in X$ we have:

$$
(\pi_1 \circ u_X)(x) = \pi_1(x, x) = x
$$

and similarly for $\pi_2 \circ u_X$, so we conclude:

$$
(v \Delta \circ \Delta u)_X = (1_X, 1_X) = 1_{(X,X)} = 1_{\Delta(X)}
$$

as desired.

The adjunction identity for $P$ proceeds similarly; the left- and right-hand sides are both natural transformatios from $P$ to itself, so the component of each at a pair $(X, Y)$ is a morphism from $P(X,Y) = X \times Y$ to itself.  We calculate:

$$
\begin{align*}
   (Pv \circ uP)_{(X,Y)} &= P(v_{(X,Y)}) \circ u_{P(X,Y)} \\
   &= P(\pi_1, \pi_2) \circ u_{X \times Y} \\
	&= \pi_1 \times \pi_2 \circ u_{X \times Y}
\end{align*}
$$

For any point $(x, y) \in X \times Y$ we have:

$$
\begin{align*}
	(\pi_1 \times \pi_2 \circ u_{X \times Y})(x,y) &= \pi_1 \times \pi_2(u_{X \times Y}(x,y)) \\
	&= \pi_1 \times \pi_2((x,y), (x,y)) \\
	&= (\pi_1(x,y), \pi_2(x,y)) \\
	&= (x, y)
\end{align*}
$$ 

Thus:

$$
(Pv \circ uP)_{(X,Y)} = 1_{X \times Y} = 1_{P(X,Y)}
$$ 

This completes the proof.
</div>

## Adjunctions and Graph Theory

Adjunctions can be used to prove nontrivial results about graph colorings - this post probably contains sufficient background for reading a paper due to [Foniok and Tardif][3] on the subject, for example.
I may make a pass at explaining some of these results in the future, but for now I will conclude with some simple exercises.

<div class="exercise">
Let $\textbf{Gph}$ denote the category whose objects are graphs and whose morphisms are graph homomorphisms.
Consider the functor $F \colon \textbf{Gph} \to \Set$ which sends a graph to its vertex set and a graph homomorphism to its restriction to vertex sets.
Construct a left adjoint for $F$.
</div>

[1]: {{ site.baseurl }}{% post_url math/category-theory/2017-09-02-galois_connections %} "Galois Connections"
[3]: https://arxiv.org/pdf/1304.2215.pdf "Adjoint Functors in Graph Theory"
