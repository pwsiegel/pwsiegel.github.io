---
layout: post
permalink: /math/higher-homotopy-calculus/
title: Higher Homotopy Groups via Vector Calculus
abstract: Calculating higher homotopy groups of spheres is infamously hard.  Here is an example which uses only undergraduate vector calculus.
level: hard
date: 2014-12-09
categories: math
tags: live
comments: true
---

This semester I taught an undergraduate course on topology (continuity, compactness, connectedness, and basic homotopy theory) and on the last day of class I decided to give a brief introduction to the theory of higher homotopy groups. For motivation, consider the classical Brouwer fixed point theorem:

<div class="theorem">
Every continuous function $f \colon B^2 \to B^2$ has a fixed point, where $B^2$ is the closed unit ball in the plane.
</div>
<div class="proof">
Suppose $f$ has no fixed point point, meaning $x$ and $f(x)$ are distinct for every $x \in B^2$. Define a function $r \colon B^2 \to S^1$ (where $S^1$ is the boundary circle of $B^2$) as follows. Given $x \in B^2$ there is a unique line in the plane containing both $x$ and $f(x)$, so there is a unique line segment containing $x$ whose endpoints consist of $f(x)$ and a point on $S^1$. Define $r(x)$ to be the endpoint on $S^1$. Explicit calculations (using the continuity of $f$) show that $r(x)$ is continuous, and moreover if $x \in S^1$ then $r(x) = x$. A continuous function from a topological space $X$ to a subset $A \subseteq X$ which restricts to the identity on $A$ is called a <em>retraction</em>; we have shown that if there is a continuous function $f \colon B^2 \to B^2$ with no fixed points then there is is a retraction $r \colon B^2 \to S^1$.

Let us use algebraic topology to prove that there is no such retraction. Let $i \colon S^1 \to B^2$ denote the inclusion map, so that $r \circ i \colon S^1 \to S^1$ is the identity. Passing to the induced homomorphism on fundamental groups, this shows that $r_* \circ i_* \colon \pi_1(S^1) \to \pi_1(S^1)$ is the identity and hence $r_*$ is surjective. But $\pi_1(B^2)$ is the trivial group since $B^2$ is contractible and $\pi_1(S^1) \cong \Z$, so $r_* \colon \pi_1(B^2) \to \pi_1(S^1)$ could not possibly be surjective, a contradiction.
</div>

One might wonder if the argument above works for the closed unit ball $B^{n+1} \subseteq \R^{n+1}$. Indeed, the first part of the argument works in higher dimensions almost verbatim, and one gets that any continuous function $f \colon B^{n+1} \to B^{n+1}$ gives rise to a retraction $r \colon B^{n+1} \to S^n$ onto the boundary sphere. But the second part of the argument fails: the fundamental group of $S^n$ is trivial for $n > 1$, so there is no contradiction. The solution is to replace the fundamental group $\pi_1$ with the higher homotopy group $\pi_n$; whereas $\pi_1(X)$ is the group of homotopy classes of continuous maps $S^1 \to X$, $\pi_n(X)$ is the group of homotopy classes of continuous maps $S^n \to X$ (of course, all spaces, maps, and homotopies must have base points).

In the proof of the Brouwer fixed point theorem above, we only needed three properties of the fundamental group:

* Every continuous map $f \colon X \to Y$ induces a group homomorphism $f_\ast \colon \pi_1(X) \to \pi_1(Y)$.
* If $f_1, f_2 \colon X \to Y$ are homotopic continuous maps then $${f_1}_\ast = {f_2}_\ast \colon \pi_1(X) \to \pi_1(Y)$$
* $\pi_1(S^1)$ is not the trivial group.

The first two of these properties generalize to higher homotopy groups with almost identical proofs. The counterpart of the third property, namely that $\pi_n(S^n)$ is not the trivial group, is considerably more difficult. One typically computes $\pi_1(S^1)$ using covering space theory, but there is no counterpart of covering space theory for higher homotopy groups. (Well, such a theory does exist in a manner of speaking, but it is much more complicated than covering space theory.)

To actually compute $\pi_n(S^n)$ one needs some rather powerful tools in algebraic topology, such as the Freudenthal suspension theorem or the Hurewicz isomorphism. The difficulty of this computation is still a bit mysterious to me, and was the subject of one of my recent [Mathoverflow questions](http://mathoverflow.net/questions/184920/why-is-it-so-hard-to-compute-pi-nsn "Mathoverflow"). Even the more modest goal of proving that $\pi_n(S^n)$ is non-trivial is quite a bit more challenging for $n > 1$ than for $n = 1$. Nevertheless, I came up with an argument in the case $n = 3$ based on vector calculus which is suitable for undergraduates; I don't think I've seen this exact argument written down anywhere else, so I thought I would write it up here. It is adapted from a more standard argument involving Stokes' theorem on manifolds which works in any dimension (but which requires a semester's worth of manifold theory to understand).

I will prove the following statement:

<div class="theorem">
There is no continuous retraction $f \colon B^3 \to S^2$.
</div>

This alone is enough to prove the Brouwer fixed point theorem for $B^3$ without having to worry about higher homotopy groups, but in fact it implies that $\pi_2(S^2)$ is nontrivial. Pick a base point $p \in S^2$ and consider the identity map $I \colon S^2 \to S^2$. This determines a class $[I] \in \pi_2(S^2,p)$, so if $\pi_2(S^2,p)$ is the trivial group then there is a base point preserving homotopy between $I$ and then constant map $e_p \colon S^2 \to S^2$ given by $e_p(x) = p$. This homotopy is a continuous map $H \colon S^2 \times [0,1] \to S^2$ which satisfies:

* $H(x,0) = x$
* $H(x,1) = p$
* $H(p,t) = p$ for all $t$

Given such a homotopy, define $r \colon B^3 \to S^2$ by $r(x) = H\left(\frac{x}{\abs{x}}, \abs{x}\right)$ if $x \neq 0$ and $r(0) = 0$. It is not hard to check that $r$ is a retraction, contradicting the main theorem.

To prove the main theorem we need a technical lemma:

<div class="lemma">
If there is a continuous retraction $r \colon B^3 \to S^2$ then there is a smooth retraction.
</div>

The proof of this lemma uses some slightly complicated analysis, but ultimately it is fairly standard; see the final chapter of Gamelin and Greene's "Introduction to Topology", for example. The only other non-trivial input required to prove the main theorem is the divergence theorem from vector calculus:

<div class="theorem">
Let $E$ be a compact subset of $\R^3$ whose boundary is a piecewise smooth surface $\partial E$, let $n$ denote the outward unit normal field on $\partial E$, and let $F$ be a smooth vector field on $E$. Then:

$$\int_{\partial E} F \cdot n\, dS = \int_E div F\, dV$$
</div>

Here $div$ ("divergence") is the differential operator $div(P,Q,R) = P_x + Q_y + R_z$.

We are now ready to prove the main theorem, that there is no continuous retraction $B^3 \to S^2$.

<div class="proof">
By the previous lemma it suffices to show that there is no smooth retraction from $B^3$ to $S^2$, so suppose $r \colon B^3 \to S^2$ is such a retraction and denote its component functions by $r = (P,Q,R)$. Thus $r$ may be viewed as a smooth vector field on $B^3$; since $r(v) = v$ for $v \in \partial B^3 = S^2$ we have $P(x,y,z) = x$, $Q(x,y,z) = y$, and $R(x,y,z) = z$ for every $(x,y,z) \in S^2$.

Consider the smooth vector field $F = P(\nabla Q \times \nabla R)$ where $\nabla$ is the gradient operator. We will compute the integral of $F$ over $S^2$ in two different ways and get two different answers, giving a contradiction. Both computations will use the divergence theorem:

$$\int_{S^2} F \cdot n\, dS = \int_{B^3} div F\, dV$$

The first computation uses a bit of vector calculus. By the product rule for the divergence of a function multiplied by a vector field, we have:

$$div(P (\nabla Q \times \nabla R)) = \nabla P \cdot (\nabla Q \times \nabla R) + P div(\nabla Q \times \nabla R)$$

The second term on the right-hand side vanishes by the product rule for the divergence of the cross product of two vector fields:

$$div(\nabla Q \times \nabla R) = \nabla R \cdot curl(\nabla Q) + \nabla Q \cdot curl(\nabla R) = 0$$

Here we used the fact that the curl of the gradient of any smooth function is the zero vector.

According to the standard "triple product" formula from vector algebra, the first term $\nabla P \cdot (\nabla Q \times \nabla R)$ is the determinant of the Jacobian matrix $J_F$ associated to $F$ whose rows consist of $\nabla P$, $\nabla Q$, and $\nabla R$. I claim that this determinant is zero. Since $F$ takes values in $S^2$ we have that $P^2 + Q^2 + R^2 = 1$; differentiating both sides of this equation with respect to $x$ gives $P P_x + Q Q_x + R R_x = 0$, or equivalently $F \cdot F_x = 0$. Similarly $F \cdot F_y = 0$ and $F \cdot F_z = 0$, so the vectors $F_x$, $F_y$, and $F_z$ are all orthogonal to the same nonzero vector $F$ and hence there is a nontrivial dependence relation between them. But $F_x$, $F_y$, and $F_z$ are the columns of $J_F$, so it follows that $\det(J_F) = 0$. We conclude:

$$\int_{S^2} F \cdot n\, dS = 0$$

by the divergence theorem. (The various identities used in this argument all appear in the Wikipedia page on [vector calculus identities](http://en.wikipedia.org/wiki/Vector_calculus_identities "vector calculus identities"), with the notation $div F = \nabla \cdot F$ and $curl F = \nabla \times F$.)

Now let us compute the same integral using the fact that $F(x,y,z) = (x,y,z)$ on $S^2$. Using $P = x$, $Q = y$, and $R = z$ we calculate that $P (\nabla Q \times \nabla R) = (x,0,0)$ and hence $div(P (\nabla Q \times \nabla R)) = 1$. By the divergence theorem we get:

$$\int_{S^2} F \cdot n\, dS = \int_{B^3} 1\, dV = vol(B^3) \neq 0$$

This is a contradiction.
</div>
