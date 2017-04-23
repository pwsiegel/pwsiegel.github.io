---
layout: post
permalink: /radon-nikodym/
title: The Radon-Nikodym Theorem
abstract: The Radon-Nikodym theorem is a workhorse result in measure theory, with numerous applications to probability dynamics (such as the existence of conditional expectations and the existence of KL-divergence).
I will give a simple proof using Hilbert spaces.
level: hard
date: 2017-03-13
categories: stage
comments: true
---

Let $\mu$ and $\nu$ be two measures on the same measurable space $(\Omega, \Sigma)$.
We say that $\mu$ is *absolutely continuous* with respect to $\nu$ (written $\mu \ll \nu$) if $\mu(E) = 0$ whenever $\nu(E) = 0$ for $E \in \Sigma$.
Given $\nu$, there is a simple way to generate a new measure which is absolutely continuous with respect to $\nu$: take any measurable function $g \colon \Omega \to \R$ and define

$$\mu_g(E) = \int_E g\, d\nu$$

It is a standard exercise in measure theory that $\mu_g$ is a measure and that $\mu_g(E) = 0$ whenever $\nu(E) = 0$.

The Radon-Nikodym theorem asserts that under mild hypotheses *every* measure $\mu$ which is absolutely continuous with respect to $\nu$ has this form for a unique measurable function $g$, called the *Radon-Nikodym derivative* of $\nu$ with respect to $\mu$, often written $\frac{d\nu}{d\mu}$.
The theorem is an important part of the structure theory of measures, and the Radon-Nikodym derivative comes up frequently in applications.
Indeed, I was reminded of this theorem because of its role in another blog post that I was writing about KL-divergence.

The plan for this post is to give a proof of the Radon-Nikodym theorem and then discuss some applications and examples.
The proof, which I learned in graduate school, uses Hilbert spaces to do most of the work; I think it is originally due to von Neumann.

## Warm-up

The proof of the theorem in its full generality inevitably uses some reasonably serious measure theory and/or functional analysis.
But I think it is instructive to first work it out in a simple special case: measures on finite sets.

So let $\Omega = \br{x_1, \ldots x_n}$ be a finite set and let $\Sigma$ be the power set of $\Omega$.
A measure $\mu$ on $\Omega$ is determined by its values on each one point set $\br{x_i}$, so it is nothing more than a vector in $\R^n$ whose entries are all nonnegative.
Let us use the notation $\mu_i$ for the components of $\mu$, meaning $\mu_i = \mu(\br{x_i})$.

Given two measures $\mu$ and $\nu$, we have that $\mu$ is absolutely continuous with respect to $\nu$ if and only if $\mu_i = 0$ whenever $\nu_i = 0$.
We would like to find a measurable function $g \colon \Omega \to \R$ - which is also just a vector in $\R^n$ - with the property that:

$$\mu(E) = \int_E g\, d\nu$$

for every $E \in \Sigma$.
Applying this to the one point sets $E_i = \br{x_i}$, we get:

$$\mu_i = \mu(E_i) = \int_{E_i} g\, d\nu = g_i \nu_i$$

If $\nu_i = 0$ then necessarily $\mu_i = 0$ by absolute continuity, so we are free to pick any value for $g_i$; let us choose $g_i = 0$.
Otherwise we just set $g_i = \frac{\mu_i}{\nu_i}$, and we are done.

So the theorem is quite straightforward and explicit for measures on finite sets.
Moreover if we look at this calculation in the right way it gives us a hint for how to handle the general case.
The equation $\mu_i = g_i \nu_i$ implies that for any function $f$ on $\Omega$ we have

$$\int_\Omega f\, d\mu = \int_\Omega f g\, d\nu = \inner{f, g}_\nu$$

where $\inner{\cdot, \cdot}\_\nu$ is the $L^2$-inner product determined by $\nu$.
So another way to construct $g$ is to apply the Riesz representation theorem to the linear functional $f \mapsto \int_\Omega f\, d\mu$ on the finite dimensional Hilbert space $L^2(\Omega, \nu)$.
This idea doesn't quite work as stated in the general case because there is no guarantee that the linear functional is bounded, but it can be fixed by working instead over the Hilbert space $L^2(\Omega, \mu + \nu)$.
The details appear in the next section.

## Proof of the Radon-Nikodym theorem

Without further ado, let us jump into the proof.

<div class="theorem">
Let $\mu$ and $\nu$ be finite measures on a measurable space $(\Omega, \Sigma)$.
If $\mu$ is absolutely continuous with respect to $\nu$ then there is a unique $g \in L^1(\Omega, \nu)$ with the property that:

$$\mu(E) = \int_E g\, d\nu$$

for every $E \in \Sigma$.
</div>
<div class="proof">
Consider the Hilbert space $H = L^2(\mu + \nu)$.
Define a linear functional $\phi \colon H \to \R$ by

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

Consider the case $E = h^{-1}(-\infty, 0]$.
Then $\int_E h\, d(\mu + \nu) \leq 0$ since $h \leq 0$ on $E$, so we must have $\nu(E) = 0$.
A similar argument using the $h^{-1}[1, \infty)$ and \eqref{riesz2} implies that $0 < h < 1$ almost everywhere with respect to $\nu$.
Consequently the function $g = \frac{h}{1 - h}$ is $\nu$-measurable; I claim that $g$ satisfies the conditions in the statement of the theorem.

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

(This also proves that $g$ really is $\nu$-integrable: $g$ is the increasing limit of the $\nu$-integrable functions $g 1\_{E_n}$ where $E_n = h^{-1}(0, 1 - \frac{1}{n})$, and the computation above shows that $\int_\Omega g 1\_{E_n}\, d\nu = \mu(E_n) \to \mu(\Omega)$.
So $g$ is integrable by the monotone convergence theorem.)

This completes the proof that the desired function $g$ exists, so it remains only to prove that $g$ is unique.
Suppose $g'$ is another $\nu$-integrable function with the property that $\mu(E) = \int_E g'\, d\nu$ for every measurable set $E$.
Let $E$ denote the subset of $\Omega$ where $g > g'$; we have:

$$0 = \mu(E) - \mu(E) = \int_E (g - g')\, d\nu$$

This forces $\nu(E) = 0$.
Repeating this argument on the set where $g' > g$ implies that $g =  g'$ almost everywhere with respect to $\nu$.
</div>

The Radon-Nikodym theorem admits some modest generalizations.
It can be proved for $\sigma$-finite measures by applying the theorem to an increasing union of sets on which both $\mu$ and $\nu$ are finite.
It also extends to signed measures by decomposing them into their positive and negative parts, and it extends to complex measures by considering their real and imaginary parts.
But for the moment I am mostly interested in applications to probability theory, for which the formulation of the theorem presented here is sufficient.

## Application: conditional expectations

There are a number of constructions in measure theory which are straightforward in the discrete case but quite difficult to define more generally.
In these cases the Radon-Nikodym derivative is often the right tool for the job.
I will illustrate this with an important example from probability theory: the conditional expectation.

Conditional distributions arise in probability theory when one wishes to update a model in response to new information or constraints.
As we shall see some serious difficulties emerge when the new information has probability zero.

### Example: coin flips

Let us start with a basic example - independent coin flips - as a guide for what to expect.
Suppose we flip a fair coin twice, and we would like to predict how many times heads will come up.
The sample space in this case is simply $\Omega = \br{H,T}^2$ equipped with the uniform probability measure; the number $X$ of heads that come up is an integer-valued random variable defined on this space.
Its expectation is:

$$\E(X) = \sum_n n \P(X = n) = 0 \cdot \frac{1}{4} + 1 \cdot \frac{1}{2} + 2 \cdot \frac{1}{4} = 1$$

Now let us impose a constraint.
Let $E$ be the event in which the first coin flip comes up heads, and let us try to make sense of the quantity $\E(X|E)$.
A natural approach is to use the conditional density function for $X$ given $E$:

$$\P(X = n | E) = \frac{\P(X = n \cap E)}{\P(E)}$$

The conditional expectation is then:

$$
\begin{equation} \label{discrete_ce}
\E(X|E) = \sum_n n \P(X = n | E) = \frac{1}{\P(E)} \sum_n n \P(X = n \cap E)
\end{equation}
$$

(In fact, this formula is correct for any integer-valued random variable.)
To compute the probabilities $\P(X = n \cap E)$, let $X_1$ and $X_2$ represent the outcomes of the first and second coin tosses, respectively, so that $E$ is simply the event $X_1 = H$.
We get:

$$
\P(X = n | E) = \begin{cases}
\P(X_2 = T)\P(E) & n = 1 \\
\P(X_2 = H)\P(E) & n = 2 \\
0 & \text{otherwise}
\end{cases}
$$

Thus:

$$
\begin{align*}
\E(X|E) &= \frac{1}{\P(E)} \left( 1 \cdot \P(X_2 = T)\P(E) + 2 \cdot \P(X_2 = H)\P(E) \right) \\
&= 1 \cdot \P(X_2 = T) + 2 \cdot \P(X_2 = H) \\
&= \frac{3}{2}
\end{align*}
$$

This computation may seem overly detailed, especially if the reader guessed the answer based on intuition rather than rigorous foundations.
But the details were chosen to highlight the difficulties in generalizing to continuous distributions, as we shall see in the next section.

### The continuous case

The formula \eqref{discrete_ce} in the previous section more or less settles how to compute conditional expectations of discrete random variables. 
Note that the term $\frac{1}{\P(E)}$ could potentially cause problems if $\P(E) = 0$, but in the discrete case nothing is lost by simply defining $\E(X|E) = 0$ whenever $\P(E) = 0$; after all, the conditional probabilities $\P(X = n \cap E)$ are necessarily zero.

But for continuous distributions there are much more interesting probability zero events and conditioning against them requires more careful thought.
Consider the unit sphere $S^2 \subseteq \R^3$ equipped with the uniform probability measure.
Drawing points from the sphere at random, how can we condition against the event that a point $p$ lies on a prescribed great circle $C$?
Given a measurable set $A$ we would like to write:

$$\P(A|C) = \frac{\P(A \cap C)}{\P(C)}$$

but of course a great circle has measure zero in the sphere and thus the fraction on the right-hand side is ill-defined.
A natural work-around is to thicken $C$ to a small neighborhood $C_\eps$ which contains $C$ and pass to a limit as $\eps \to 0$ with the hope that $\frac{\P(A \cap C_\eps}{\P(C_\eps)}$ converges.
This procedure actually does work, but as we shall see it still causes problems.

Working in standard spherical coordinates $(\theta, \phi) \in [0,2\pi] \times [0, \pi]$, the uniform density function for a random point on the sphere is given by $f(\theta, \phi) = \frac{1}{4\pi} \sin \phi$.
(This is the Jacobian of the standard spherical coordinate transformation normalized to give total probability $1$.)
To begin, let us condition on the great circle $\phi = \frac{\pi}{2}$ (the "equator" of the sphere).  The natural way to thicken this circle is:

$$C_\eps = \br{(\theta, \phi) \colon \frac{\pi}{2} \leq \phi \leq \frac{\pi}{2} + \eps}$$

If we integrate a function $g$ over one of these slices, we get:

$$
\begin{align*}
\iint_{C_\eps} g\, dS &= \frac{1}{4\pi} \int_0^{2\pi} \int_{\pi/2}^{\pi/2 + \eps} g(\theta, \phi) \sin \phi\, d\phi\, d\theta \\
&\to \frac{1}{4\pi} \int_0^{2\pi} g\left(\theta, \frac{\pi}{2}\right)\, d\theta \\
\end{align*}
$$
