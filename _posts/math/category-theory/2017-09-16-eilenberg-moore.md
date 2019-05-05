---
layout: post
permalink: /category-theory/eilenberg-moore/
title: Eilenberg-Moore Algebras
abstract: In my post on monads I showed that if $F$ is a functor with right adjoint $G$ then $GF$ is a monad, and I asserted without proof that every monad arises in this way from at least one adjunction.  The answers to the question "Which adjunctions induce a given monad $M$?" form a category in a natural way, and in this post I will use the theory of Eilenberg-Moore algebras to construct some interesting objects in this category.
date: 2017-09-16
categories: math
comments: true
---

Let $F \colon \D \to \C$ and $G \colon \C \to \D$ be a pair of adjoint functors with a unit $u \colon 1\_\C \to FG$ and counit $v \colon GF \to 1\_\D$.
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
Let $(F, G, u, v)$ be an adjunction between categories $\C$ and $\D$ and let $(F', G', u', v')$ be an adjunction between categories $\C'$ and $\D'$.  A **morphism of adjunctions** from $(F, G, u, v)$ to $(F', G', u', v')$ is a pair of functors $K \colon \C \to \C'$ and $L \colon \D \to \D'$ with the following properties:

- The functors $F$, $F'$, $G$, and $G'$ fit into a commuting diagram with $L$ and $K$:

$$
\begin{CD}
\D @>F>> \C @>G>> \D \\
@VLVV @VKVV @VLVV \\
\D' @>>F'> \C' @>>G'> \D'
\end{CD}
$$

- $L$ and $K$ intertwine the units and counits, in the sense that:

$$
Ku = u'K \quad \text{and} \quad Lv = v'L
$$

</div>

As the name suggests, morphisms of adjunctions satisfy the axioms of morphisms in a category.
There are no particular surprises in verifying these axioms, so we leave it as an exercise for the reader.

<div class="exercise">
- If

$$(K, L) \colon (F,G,u,v) \to (F',G',u',v')$$

and

$$(K', L') \colon (F',G',u',v') \to (F'',G'',u'',v'')$$

are morphisms of adjunctions then verify that

$$(K' \circ K, L' \circ L) \colon (F,G,u,v) \to (F'',G'',u'',v'')$$

is as well.

- With the notation above, define composition of morphisms of adjunctions by 

$$(K', L') \circ (K, L) = (K' \circ K, L' \circ L)$$

Verify that this composition law is associative.

- Verify that $(1\_\C, 1\_\D)$ is a morphism from any adjunction between $\C$ and $\D$ to itself.
Show that this morphism is an identity for the composition law above, meaning 

$$(1_{\C'}, 1_{\D'}) \circ (K, L) = (K, L) \circ (1_\C, 1_\D) = (K, L)$$

for any morphism of adjunctions $(K, L)$.
</div>

We can now define the category $\tbf{Adj}$ of adjunctions to be the category whose objects are adjunctions and whose morphisms are morphisms of adjunctions.
For any pair of categories $\C$ and $\D$, there is a full subcategory $\tbf{Adj}(\C, \D)$ whose objects are are adjunctions between $\C$ and $\D$.

As explained in my [post][1] on monads, every adjunction induces a monad and a comonad.
Given a monad $(M,u,j)$ on a category $\D$, there is another full subcategory $\tbf{Adj}(M,u,j)$ of $\tbf{Adj}$ whose objects are adjunctions $(F,G,u,v)$ between $\C$ and $\D$ such that $M = GF$ and $j = GvF$ (the $u$'s are the same), and whose morphisms are morphisms of adjunctions.
It is not obvious that $\tbf{Adj}(M,u,j)$ has even a single object.
The goal for the remainder of this post is to construct an initial object called a _Kleisli adjunction_ and a family of terminal objects called _Eilenberg-Moore algebras_.
Aside from establishing the nontriviality of $\tbf{Adj}(M,u,j)$, these objects are useful tools for understanding the underlying monad.
