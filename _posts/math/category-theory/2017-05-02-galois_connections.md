---
layout: post
permalink: /category-theory/galois-connections/
title: Galois Connections
abstract: In this post we shall introduce more or less from scratch the concept of a Galois connection, developing the theory of posets and adjunctions along the way.
level: medium 
date: 2017-05-02
categories: category-theory 
comments: true
---

Consider the set $\Z$ of all integers and the set $\Q$ of all rational numbers (i.e. ratios of integers).
These sets each come equipped with a relation $\leq$, where in both sets $a_1 \leq a_2$ simply means that $a_1$ is less than or equal to $a_2$.
This relation is satisfies a few simple axioms:

1. Reflexivity: $a_1 \leq a_1$ for all $a_1$
2. Antisymmetry: if $a_1 \leq a_2$ and $a_2 \leq a_1$ then $a_1 = a_2$
3. Transitivity: if $a_1 \leq a_2$ and $a_2 \leq a_3$ then $a_1 \leq a_3$

A relation $\leq$ on a set $S$ which satisfies these three properties is called a *partial ordering* on $S$, and $S$ equipped with $\leq$ is called a *poset* (short for "partially ordered set").

<div class="exercise">
Let $G$ be a simple graph.
Define a relation $\leq$ on the vertex set of $G$ by declaring that $v \leq w$ if either $v = w$ or there is a path of (directed) edges starting at $v$ and ending at $w$.
Show that $\leq$ is a partial ordering if and only if $G$ is acyclic.
</div>

Posets admit a natural category structure as we shall see below, and they provide a convenient source of examples for coming to terms with categorical structures like adjoints and monads.  
In this post we will introduce the notion of a _Galois connection_ (viewed as an adjunction between two posets) and briefly discuss an application to the theory of abstract interpretation in computer science.

## Posets as categories

The main point of this post is that any poset has the structure of a category, and that it can be useful and interesting to view it as such.

So let $(S, \leq)$ be a poset.
Consider the category (which, abusing notation, we will also refer to as $S$) whose objects are the elements of $S$ and which has exactly one morphism from $a_1$ to $a_2$ whenever $a_1 \leq a_2$.
We define composition as follows: if $f \colon a_1 \to a_2$ and $g \colon a_2 \to a_3$ are morphisms then by definition $a_1 \leq a_2$ and $a_2 \leq a_3$, so by transitivity $a_1 \leq a_3$ and thus we can define the composition $g \circ f$ to be the unique morphism from $a_1$ to $a_3$.

Let us check that these objects and morphisms really do determine a category; in other words, we must show that composition is associative and that each object has an identity morphism.

- **Associativity**

Given $f \colon a_1 \to a_2$, $g \colon a_2 \to a_3$, and $h \colon a_3 \to a_4$, the possible triple compositions are:

$$
h \circ (g \circ f) \quad \text{and} \quad (h \circ g) \circ f
$$

These compositions are equal since each are the unique morphism from $a_1$ to $a_4$.

- **Identity**

Given an object $a$ we have that $a \leq a$ by reflexivity of $\leq$, so define $1_a \colon a \to a$ to be the unique morphism from $a$ to itself.
Let us check that $1_a$ satisfies the axioms of an identity morphism.
Given a morphism $f \colon a \to a_0$, we have that

$$
f \circ 1_a
$$

is by definition of composition the unique morphism from $a$ to $a_0$.
But $f$ is a morphism from $a$ to $a_0$, so by uniqueness $f = f \circ 1_a$.
This shows that $1_a$ is a right identity; a similar argument shows that $1_a$ is also a left identity.

## Functors between posets

Let $(A, \leq_A$ and $(B, \leq_B)$ be two posets, viewed as categories according to the previous section.
Viewing these posets as categories, let us unravel the meaning of a functor $F \colon (A, \leq_A) \to (B, \leq_B)$.

A functor must map objects in $A$ to objects in $B$; since the objects in $A$ and $B$ correspond to elements of the underlying sets, this means that $F$ is a function from the set $A$ to the set $B$.
But $F$ also maps each morphism $f \colon a_1 \to a_2$ to a morphism $F(f) \colon F(a_1) \to F(a_2)$.
The condition that there is a morphism $a_1 \to a_2$ means $a_1 \leq_A a_2$, and the condition that there is a morphism $F(a_1) \to F(a_2)$ means $F(a_1) \leq_B F(a_2)$.

So a $F$ determines a function $A \to B$ which preserves the partial ordering, in the sense that $F(a_1) \leq_B F(a_2)$ whenever $a_1 \leq_A a_2$.
The reader is invited to check that any such function determines a functor $(A, \leq_A) \to (B, \leq_B)$, so a functor between posets is the same thing as a function between the underlying sets which preserves the partial ordering.

<div class="example">
Consider the integers $\Z$ and the rational numbers $\Q$ equipped with the standard partial ordering $\leq$.
The inclusion $I \colon \Z \to \Q$ which sends an integer $n$ to itself (viewed as the fraction $\frac{n}{1}$) preserves $\leq$ and hence defines a functor from the poset $\Z$ to the poset $\Q$.

Moreover the function $R \colon \Q \to \Z$ which rounds down each rational number also preserves $\leq$ and hence defines a functor from $\Q$ to $\Z$.
</div>

<div class="exercise">
Let $\phi \colon G_1 \to G_2$ be a homomorphism between directed acyclic graphs and let $\phi_V \colon V_1 \to V_2$ be the restriction of $\phi$ to the vertex sets.
Show that $\phi_V$ is a functor of posets where $V_1$ and $V_2$ are equipped with the partial ordering defined in the previous exercise.
</div>

## Adjoint functors

The concept of an adjoint is a powerful organizing principle throughout mathematics, though its origins lie in linear algebra rather than category theory.
Informally, an adjunction between two categories $\C$ and $\D$ is a pair of functors $F \colon \D \to \C$ and $G \colon \C \to D$ which transform computations in $\C$ into computations in $\D$ as efficiently as possible (and vice-versa).
For instance, one can use the inclusion functor $\Z \to \Q$ from the previous section to transform a computation with integers into a computation with rational numbers, and the existence of the restriction functor $\Q \to \Z$ guarantees that this is the "best" transformation possible in an appropriate sense.

There are a variety of equivalent ways to formulate the axioms for a pair of adjoint functors; we will choose a formulation which is well-adapted to the important special case of functors between posets.

<div class="definition">
Let $\C$ and $\D$ be categories.  An _adjunction_ between $\C$ and $\D$ consists of a pair of functors $F \colon \D \to \C$ and $G \colon \C \to \D$ together with natural transformations

$$
u \colon FG \to 1_\C \quad \text{and} \quad v \colon 1_\D \to GF
$$

which satisfy the following identities:

$$
uF \circ Fv = 1_F \quad \text{and} \quad Gu \circ v G = 1_G
$$

$F$ is said to be the _left adjoint_ of $G$ and $G$ is said to be the _right adjoint_ of $F$.  The natural transformations $u$ and $v$ are called the _counit_ and _unit_ of the adjunction, respectively.
</div>

A few remarks on notation are in order.
The symbol $1$ is used in two different ways: $1\_\C$ and $1\_\D$ are the identity functors for the categories $\C$ and $\D$, respectively, whereas $1_F$ and $1_G$ are the identity natural transformations for $F$ and $G$, respectively.

The objects $uF$, $Fv$, $Gu$, and $vG$ also require some explanation.
Given functors $S, T \colon \mathcal{A} \to \mathcal{B}$ (we depart from our usual notation for functors and categories to avoid confusion with the notation in the definition above), a natural transformation $\mu \colon S \to T$, and a functor $U \colon \mathcal{B} \to \mathcal{Z}$, we can define a new natural transformation $U \mu \colon US \to UT$ by:

$$
(U \mu)_A = U \mu_A \colon US(A) \to UT(A)
$$

Similarly, given a functor $V \colon \mathcal{Z} \to \mathcal{A}$, we define a natural transformation $\mu V \colon SV \to TV$ by:

$$
(\mu V)_Z = \mu_{V(Z)} \colon SV(Z) \to TV(Z)
$$

Thus in the setting of the definition above $uF$ is the natural transformation $FGF \to F$ given by $(uF)\_D = u\_{F(D)}$ while $Fv$ is the natural transformation $F \to FGF$ given by $(Fv)_D = Fv_D$, and similarly for $Gu$ and $vG$.

<div class="example">
Consider the product functor $P \colon \Set^2 \to \Set$ defined by $P(X,Y) = X \times Y$ on objects and $P(f,g) = f \times g$ on morphisms.
In this example we will construct a natural adjoint for $P$.

Specifically, we will show that the "diagonal" functor $\Delta \colon \Set \to \Set^2$ defined by $\Delta(X) = (X, X)$ on objects and $\Delta(f)(x, x) = (f(x), f(x))$ on morphisms is a right adjoint for $P$.
In order to do this we must construct a counit and a unit, i.e. natural transformations

$$
u \colon \Delta P \to 1_{\Set^2} \quad \text{and} \quad v \colon 1_\Set \to P \Delta
$$

which satisfy the appropriate axioms.

We begin with the counit.
$\Delta P$ is the functor $\Set^2 \to \Set^2$ which sends a pair of sets $(X, Y)$ to the pair $(X \times Y, X \times Y)$.
On the other hand $1_{\Set^2}(X, Y)$ is just $(X, Y)$.
So the component of the counit at $(X,Y)$ must be a mapping

$$
u_{(X,Y)} \colon (X \times Y, X \times Y) \to (X, Y)
$$

Define $u\_{(X,Y)}((x_1, y_1), (x_2, y_2)) = (x_1, y_2)$, i.e. $u\_{(X,Y)} = (\pi_X, \pi_Y)$ where $\pi_X$ and $\pi_Y$ are the projection maps on $X \times Y$.
Exercise for the reader: verify that $u$ defined this way really is a natural transformation.

Now for the unit.
$1\_\Set$ of course sends a set $X$ to itself, while $P \Delta$ sends $X$ to $X \times X$.
So the component of the unit at $X$ must be a mapping

$$
v_X \colon X \to X \times X
$$

Simply define $v_X(x) = (x, x)$; again, we leave it to the reader to check that $v$ so defined is a natural transformation.

Finally, we must show that $P$, $\Delta$, $u$, and $v$ satisfy the adjunction identities:

$$u \Delta \circ \Delta v = 1_\Delta \quad \text{and} \quad P u \circ v P = 1_P$$

Let us begin with the identity for $\Delta$.
The left- and right-hand sides are both natural transformations from $\Delta$ to itself, so the component of each side at a set $X$ is a morphism from $\Delta(X)$ to itself. 
But $\Delta(X) = (X, X)$ by definition, so we can evalute the left- and right-hand sides at a point $(x_1, x_2) \in (X, X)$.

$$
\begin{align*}
   (u \Delta \circ \Delta v)_X(x_1, x_2) &= u_{\Delta(X)}(\Delta(v_X)(x_1, x_2)) \\
   &= u_{\Delta(X)}(v_X(x_1), v_X(x_2)) \\
   &= u_{\Delta(X)}((x_1, x_1), (x_2, x_2)) \\
   &= u_{(X, X)}((x_1, x_1), (x_2, x_2)) \\
   &= (x_1, x_2)
\end{align*}
$$

Thus the component of $u \Delta \circ \Delta v)$ at any set $X$ is the identity morphism from $\Delta(X)$ to itself, as desired.

The adjunction identity for $P$ proceeds similarly; the left- and right-hand sides are both natural transformatios from $P$ to itself, so the component of each at a pair $(X, Y)$ is a morphism from $P(X,Y) = X \times Y$ to itself.  We calculate:

$$
\begin{align*}
   (Pu \circ vP)_{(X,Y)}(x,y) &= P(u_{(X,Y)})(v_{P(X,Y)}(x,y)) \\
   &= P(u_{(X,Y)})(v_{X \times Y}(x,y)) \\
   &= P(u_{(X,Y)})((x,y), (x,y)) \\
   &= u_{(X,Y)}((x,y), (x,y)) \\
   &= (x,y)
\end{align*}
$$

Thus $Pu \circ vP = 1\_{\Set^2}$, and the proof is complete
</div>
