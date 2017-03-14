---
layout: post
permalink: /radon-nikodym/
title: The Radon-Nikodym Theorem
abstract: The Radon-Nikodym theorem is a workhorse result in measure theory, with numerous applications to probability dynamics (such as the existence of conditional expectations and the existence of KL-divergence).  I will give a simple proof using Hilbert spaces.
level: hard
date: 2017-03-13
categories: stage
comments: true
---

Let $\mu$ and $\nu$ be two measures on the same measurable space $(\Omega, \Sigma)$.  We say that $\mu$ is *absolutely continuous* with respect to $\nu$ (written $\mu \ll \nu$) if $\mu(E) = 0$ whenever $\nu(E) = 0$ for $E \in \Sigma$.  Given $\nu$, there is a simple way to generate a new measure which is absolutely continuous with respect to $\nu$: take any measurable function $f \colon \Omega \to \R$ and define

$$\mu_f(E) = \int_E f\, d\nu$$

It is a standard exercise in measure theory that $\mu_f$ is a measure and that $\mu_f(E) = 0$ whenever $\nu(E) = 0$.

The Radon-Nikodym theorem asserts that under mild hypotheses *every* measure $\mu$ which is absolutely continuous with respect to $\nu$ has this form for a unique measurable function $f$, called the *Radon-Nikodym derivative* of $\nu$ with respect to $\mu$, often written $\frac{d\nu}{d\mu}$.  The theorem is an important part of the structure theory of measures, and the Radon-Nikodym derivative comes up frequently in applications.  Indeed, I was reminded of this theorem because of its role in another blog post that I was writing about KL-divergence.

The plan for this post is to give a proof of the Radon-Nikodym theorem and then discuss some applications and examples.  The proof, which I learned in graduate school, uses Hilbert spaces to do most of the work; I think it is originally due to von Neumann.

## The Lebesgue Decomposition Theorem

The proof of the Radon-Nikodym theorem will pass through the Lebesgue decomposition theorem, and to state this theorem we need to review a little bit more language.  Recall that two measures $\mu$ and $\nu$ on $(\Omega, \Sigma)$ are *mutually singular* (written $\mu \perp \nu$) if $\Omega$ is the disjoint union of two measurable sets $A$ and $B$ such that $\mu(A) = \nu(B) = 0$.

<div class="definition">
Let $\mu$ and $\nu$ be two measures on $(\Omega, \Sigma)$.  A *Lebesgue decomposition* of $\nu$ with respect to $\mu$ is a pair of measures $\eta_0$ and $\eta_1$ with the following properties:
* $\nu = \eta_0 + \eta_1$
* $\eta_0 \ll \mu$
* $\eta_1 \perp \mu$
</div>

The Lebesgue decomposition theorem says that every pair of finite measures admits a unique Lebesgue decomposition.  Let us prove uniqueness first:

<div class="lemma">
Any pair of measures on a measure space $(\Omega, \Sigma)$ admits at most one Lebesgue decomposition.
</div>
<div class="proof">
Suppose a pair of measures $\mu$, $\nu$ admits two Lebesgue decompositions $\eta_0$, $\eta_1$ and $\tau_0$, $\tau_1$.  Write $\Omega$ as the disjoint union of sets $A_\eta$ and $B_\eta$ such that $\mu(A_\eta) = \eta_1(B_\eta) = 0$.  Note that $\eta_0(A_\eta) = \tau_0(A_\eta) = 0$ since $\eta_0$ and $\tau_0$ are absolutely continuous with respect to $\mu$.  Thus for any $E \in \Sigma$ we have:

$$
\begin{align*}
\eta_1(E) &= \eta_1(E \cap A_\eta) + \eta_1(E \cap B_\eta) \\
&= \eta_1(E \cap A_\eta) \\
&= \eta_0(E \cap A_\eta) + \eta_1(E \cap A_\eta) \\
&= \nu(E \cap A_\eta)
\end{align*}
$$

But we also have:

$$\nu(E \cap A_\eta) = \tau_0(E \cap A_\eta) + \tau_1(E \cap A_\eta) = \tau_1(E \cap A_\eta)$$

So we conclude:

$$\eta_1(E) = \nu(E \cap A_\eta) = \tau_1(E \cap A_\eta) \leq \tau_1(E)$$

This forces $\tau_0 \leq \eta_0$, so consider the measure:

$$\chi = \tau_1 - \eta_1 = \eta_0 - \tau_0$$

$\chi$ is absolutely continuous with respect to $\mu$ since both $\eta_0$ and $\tau_0$ are.  I claim that $\chi$ is also mutually singular with respect to $\mu$.  We have:
</div>

<div class="theorem">
Let $\mu$ and $\nu$ be finite measures on a measurable space $(\Omega, \Sigma)$.  Then there is a unique pair of finite measures $\eta_0$ and $\eta_1$ such that:
* $\nu = \eta_0 + \eta_1$
* $\eta_0 \ll \mu$
* $\eta_1 \perp \mu$
</div>
<div class="proof">
Consider the Hilbert space $H = L^2(\mu + \nu)$.  Define a linear functional $\phi \colon H \to \R$ by:

$$\phi(f) = \int_\Omega f\, d\nu$$

Using the Schwarz inequality and the fact that $\nu \leq \mu + \nu$, we get:

$$\abs{\phi(f)}^2 \leq \left( \int_\Omega 1 \cdot \abs{f}\, d\nu \right)^2 \leq \nu(\Omega) \norm{f}_{L^2(\nu)}^2 \leq \nu(\Omega) \norm{f}_H^2$$

This shows that $\phi$ is a bounded linear functional, so by the Riesz representation theorem there is a function $g \in H$ with the property that:

$$
\begin{equation} \label{riesz1}
\int_\Omega f\, d\nu = \phi(f) = \inner{f, g} = \int_\Omega fg\, d(\mu + \nu)
\end{equation}
$$

It follows that:

$$\int_\Omega f\, d(\mu + \nu) = \int_\Omega f\, d\mu + \int_\Omega fg\, d(\mu + \nu)$$

Rearranging, we get:

$$
\begin{equation} \label{riesz2}
\int_\Omega f\, d\mu = \int_\Omega f(g - 1)\, d(\mu + \nu)
\end{equation}
$$

Let $X$ be the subset of $\Omega$ where $g$ takes negative values; $X$ is measurable since $g$ is measurable, and by \eqref{riesz1} we have:

$$\nu(X) = \int_\Omega 1_X\, d\nu = \int_\Omega 1_X g\, d(\mu + \nu) = \int_X g d(\mu + \nu)$$

If $(\mu + \nu)(X) > 0$ then the integral on the right-hand side would be negative since $g$ is by definition negative on $X$, but $\nu(X) \geq 0$ since $\nu$ is a measure.  So we must have $(\mu + \nu)(X) = 0$.  Similarly, if we let $Y$ be the set where $g$ takes values greater than $1$ then using \eqref{riesz2} we get:

$$\mu(Y) = \int_Y (1 - g)\, d(\mu + \nu)$$

and the same reasoning forces $(\mu + \nu)(Y) = 0$.  It follows that $0 \leq g \leq 1$ almost everywhere with respect to $\mu + \nu$.

So let $A$ denote the measurable set $g^{-1}(1)$ and let $B$ be its complement.  Define measures $\eta_0$ and $\eta_1$ by:

$$\eta_0(E) = \nu(E \cap B), \quad \eta_1(E) = \nu(E \cap A)$$

It is clear that $\nu = \eta_0 + \eta_1$.  Using \eqref{riesz2} we get:

$$\mu(A) = \int_A (g - 1)\, d(\mu + \nu) = 0$$

since $g = 1$ on $A$ by definition.  Clearly $\eta_1(B) = 0$, so we have proved that $\eta_1 \perp \mu$.  It remains only to show that $\eta_0 \ll \mu$.

To that end, let $E \in \Sigma$ be any set of $\mu$-measure $0$.  Certainly $\eta_0(E \cap A) = 0$, so it suffices to show that $\eta_0(E \cap B) = \nu(E \cap B) = 0$, i.e. that any set $F \subseteq B$ of $\mu$-measure $0$ necessarily has $\nu$-measure $0$.  For any such $F$ we have:

$$\nu(F) = \int_F g d(\mu + \nu) = \int_F g\, d\nu$$

But $g < 1$ on all of $B$ by the definition of $B$, so if $\nu(F) > 0$ then we have:

$$\nu(F) = \int_F g\, d\nu < \int_F 1\, d\nu = \nu(F)$$

a contradiction.
</div>
