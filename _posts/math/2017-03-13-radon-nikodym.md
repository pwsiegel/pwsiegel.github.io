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

Let $\mu$ and $\nu$ be two measures on the same measurable space $(\Omega, \Sigma)$.  We say that $\mu$ is *absolutely continuous* with respect to $\nu$ (written $\mu \ll \nu$) if $\mu(E) = 0$ whenever $\nu(E) = 0$ for $E \in \Sigma$.  Given $\nu$, there is a simple way to generate a new measure which is absolutely continuous with respect to $\nu$: take any measurable function $g \colon \Omega \to \R$ and define

$$\mu_g(E) = \int_E g\, d\nu$$

It is a standard exercise in measure theory that $\mu_g$ is a measure and that $\mu_g(E) = 0$ whenever $\nu(E) = 0$.

The Radon-Nikodym theorem asserts that under mild hypotheses *every* measure $\mu$ which is absolutely continuous with respect to $\nu$ has this form for a unique measurable function $g$, called the *Radon-Nikodym derivative* of $\nu$ with respect to $\mu$, often written $\frac{d\nu}{d\mu}$.  The theorem is an important part of the structure theory of measures, and the Radon-Nikodym derivative comes up frequently in applications.  Indeed, I was reminded of this theorem because of its role in another blog post that I was writing about KL-divergence.

The plan for this post is to give a proof of the Radon-Nikodym theorem and then discuss some applications and examples.  The proof, which I learned in graduate school, uses Hilbert spaces to do most of the work; I think it is originally due to von Neumann.

## Warm-up

The proof of the theorem in its full generality inevitably uses some reasonably serious measure theory and/or functional analysis.  But I think it is instructive to first work it out in a simple special case: measures on finite sets.

So let $\Omega = \br{x_1, \ldots x_n}$ be a finite set and let $\Sigma$ be the power set of $\Omega$.  A measure $\mu$ on $\Omega$ is determined by its values on each one point set $\br{x_i}$, so it is nothing more than a vector in $\R^n$ whose entries are all nonnegative.  Let us use the notation $\mu_i$ for the components of $\mu$, meaning $\mu_i = \mu(\br{x_i})$.

Given two measures $\mu$ and $\nu$, we have that $\mu$ is absolutely continuous with respect to $\nu$ if and only if $\mu_i = 0$ whenever $\nu_i = 0$.  We would like to find a measurable function $g \colon \Omega \to \R$ - which is also just a vector in $\R^n$ - with the property that:

$$\mu(E) = \int_E g\, d\nu$$

for every $E \in \Sigma$.  Applying this to the one point sets $E_i = \br{x_i}$, we get:

$$\mu_i = \mu(E_i) = \int_{E_i} g\, d\nu = g_i \nu_i$$

If $\nu_i = 0$ then necessarily $\mu_i = 0$ by absolute continuity, so we are free to pick any value for $g_i$; let us choose $g_i = 0$.  Otherwise we just set $g_i = \frac{\mu_i}{\nu_i}$, and we are done.

So the theorem is quite straightforward and explicit for measures on finite sets.  Moreover if we look at this calculation in the right way it gives us a hint for how to handle the general case.  The equation $\mu_i = g_i \nu_i$ implies that for any function $f$ on $\Omega$ we have

$$\int_\Omega f\, d\mu = \int_\Omega f g\, d\nu = \inner{f, g}_\nu$$

where $\inner{\cdot, \cdot}\_\nu$ is the $L^2$-inner product determined by $\nu$.  So another way to construct $g$ is to apply the Riesz representation theorem to the linear functional $f \mapsto \int_\Omega f\, d\mu$ on the finite dimensional Hilbert space $L^2(\Omega, \nu)$.  This idea doesn't quite work in the general case because the linear function is not necessarily bounded if $L^2(\Omega, \nu)$ is infinite dimensional, but as we shall see there is a clever adaptation of the idea which does the job.

## Proof of the Radon-Nikodym theorem

Without further ado, let us jump into the proof.

<div class="theorem">
Let $\mu$ and $\nu$ be finite measures on a measurable space $(\Omega, \Sigma)$.  If $\mu$ is absolutely continuous with respect to $\nu$ then there is an integrable function $g$ with the property that:

$$\mu(E) = \int_E g\, d\nu$$

for every $E \in \Sigma$.
</div>
<div class="proof">
Consider the Hilbert space $H = L^2(\mu + \nu)$.  Define a linear functional $\phi \colon H \to \R$ by

$$\phi(f) = \int_\Omega f\, d\mu$$

Using the Schwarz inequality and the fact that $\mu \leq \mu + \nu$, we get:

$$\abs{\phi(f)}^2 \leq \left( \int_\Omega 1 \cdot \abs{f}\, d\mu \right)^2 \leq \mu(\Omega) \norm{f}_{L^2(\mu)}^2 \leq \mu(\Omega) \norm{f}_H^2$$

This shows that $\phi$ is a bounded linear functional, so by the Riesz representation theorem there is a function $h \in H$ with the property that:

$$
\begin{equation} \label{riesz1}
\int_\Omega f\, d\mu = \phi(f) = \inner{f, h} = \int_\Omega fh\, d(\mu + \nu)
\end{equation}
$$

It follows that:

$$\int_\Omega f\, d(\mu + \nu) = \int_\Omega f\, d\nu + \int_\Omega fh\, d(\mu + \nu)$$

Rearranging, we get:

$$
\begin{equation} \label{riesz2}
\int_\Omega f\, d\nu = \int_\Omega f(1 - h)\, d(\mu + \nu)
\end{equation}
$$

Now, by \eqref{riesz1} we have for any measurable set $E$:

$$\nu(E) = \int_\Omega 1_E\, d\nu = \int_\Omega 1_E h\, d(\mu + \nu) = \int_E h\, d(\mu + \nu)$$

Consider the case $E = h^{-1}(-\infty, 0]$.  Then $\int_E h\, d(\mu + \nu) \leq 0$ since $h \leq 0$ on $E$, so we must have $\nu(E) = 0$.  A similar argument using the $h^{-1}[1, \infty)$ and \eqref{riesz2} implies that $0 < h < 1$ almost everywhere with respect to $\nu$.  Consequently the function $g = \frac{h}{1 - h}$ is $\nu$-measurable; I claim that $g$ is a Radon-Nikodym derivative of $\mu$ with respect to $\nu$.  

Indeed, for any measurable set $E$ we have:

$$
\begin{align*}
\int_E g\, d\nu &= \int_\Omega g 1_E\, d\nu \\
&= \int_\Omega g 1_E (1 - h)\, d(\mu + \nu) \quad \text{by \eqref{riesz2}} \\
&= \int_\Omega h 1_E\, d(\mu + \nu) \\
&= \int_\Omega 1_E\, d\mu \quad \text{by \eqref{riesz1}} \\
&= \mu(E)
\end{align*}
$$

(This also proves that $g$ really is $\nu$-integrable: $g$ is the limit of the $\nu$-integrable functions $g 1_{E_n}$ where $E_n = h^{-1}(0, 1 - \frac{1}{n}))$, and the computation above shows that $\int_\Omega g 1\_{E_n}\, d\nu = \mu(E_n) < \mu(\Omega)$.  So $g$ is integrable by the dominated convergence theorem.)
</div>


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

But the same argument, using a singular decomposition of $\Omega$ for $\mu$ and $\tau_1$ instead of $\mu$ and $\eta_1$, implies that $\tau_1(E) \leq \eta_1(E)$ for every $E \in \Sigma$.  It follows that $\eta_1 = \tau_1$ and hence $\eta_0 = \nu - \eta_1 = \nu - \tau_1 = \tau_0$.  
</div>
