---
layout: post
permalink: /category-theory/eilenberg-moore/
title: Eilenberg-Moore Algebras
abstract: In my post on monads I showed that if $F$ is a functor with right adjoint $G$ then $GF$ is a monad, and I asserted without proof that every monad arises in this way from at least one adjunction.  The answers to the question "Which adjunctions induce a given monad $M$?" form a category in a natural way, and in this post I will use the theory of Eilenberg-Moore algebras to construct some interesting objects in this category.
level: hard
date: 2017-09-16
categories: stage
comments: true
---

Let $F \colon \D \to \C$ and $\G \colon \C \to \D$ be a pair of adjoint functors with a unit $u \colon 1\_\C \to FG$ and counit $v \colon GF \to 1\_\D$.
In my [post][1] on monads we saw that the functor $GF \colon \D \to \D$ equipped with unit $u$ and join $GvF$ is a monad, but we largely suppressed the question of whether or not all monads have this form.
In fact every monad is induced by an adjunction, but that adjunction is far from unique in general.

The plan of this post is as follows:

1. Construct the category of adjunctions which induce a given monad.
2. Exhibit a terminal object in this category with the aid of the **Eilenberg-Moore category**.
3. Exhibit an initial object in this category with the aid of the **Kleisli category**.

In particular, steps 2 and 3 will affirm that the category described in step 1 is nonempty.
But these constructions are of independent interest in applications of monads - the Eilenberg-Moore category plays an important role in descent theory and the Kleisli category arises naturally in functional programming, for instance.

## The category of adjunctions

The goal of this section is to construct a category of adjunctions which induce a given monad.
This category will arise as a subcategory of the category of all adjunctions, and so we shall begin by constructing this category.

An object in this category is just an adjunction between two categories; the key is to define a notion of morphism between adjunctions.

<div class="definition">
Let $(F, G, u, v)$ be an adjunction between categories $C$ and $D$ and let $(F', G', u', v')$ be an adjunction between categories $C'$ and $D'$.  A **morphism of adjunctions** from $(F, G, u, v)$ to $(F', G', u', v')$ is a pair of functors $K \colon C \to C'$ and $L \colon D \to \D'$ with the following properties:

- The functors $F$, $F'$, $G$, and $G'$ fit into a commuting diagram with $L$ and $K$:

$$
\begin{CD}
D @>F>> C @>G>> D \\
@VLVV @VKVV @VLVV \\
D' @>F'>> C' @>G'>> D'
\end{CD}
$$

- $L$ and $K$ intertwine the units and counits, in the sense that:

$$
Lu = u'L \quad \text{and} \quad Kv = v'K
$$

</div>

It is worth noting that there is some redundancy in this definition:

<div class="exercise">
In the setting of the previous definition, show that the pair of equations $Lu = u'L$ and $Kv = v'K$ is equivalent to the single equation $Lu = u'L$ (or just $Kv = v'K$).
</div>
<div class="proof">
Suppose $Lu = u'L$.  We have:

$$
\begin{align*}
Kv = Kv
\end{align*}
$$

</div>
