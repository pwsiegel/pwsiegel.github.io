---
layout: post
permalink: /conditional-expectation/
title: Conditional Probability 
abstract: Once a statistical model has been constructed it is usually important to understand how it behaves under certain constraints or assumptions.  The tool for carrying out this sort of analysis is the conditional distribution of a random variable; on the surface it seems quite straightforward to define, but upon closer examination it requires some fairly sophisticated theory to get it off the ground.
level: hard
date: 2017-04-23
categories: stage
comments: true
---

One of the most important challenges in building a good statistical model is ensuring that it behaves correctly under a variety of different realistic assumptions.
An economic forecasting model isn't much good if it breaks during a recession, nor is a natural language classifier if it can't handle formatting or spelling errors.
There are a number of specific tools for testing models with different constraints, but more or less all of them are based on the language of conditional probability.
But it is strangely difficult to provide this language with proper foundations; doing anything serious beyond the most basic examples calls for the full power of the modern measure-theoretic formalism for probability theory.

## Example: coin flips

Let us start with a basic example - independent coin flips - as a guide for what to expect.
Suppose we flip a fair coin twice, and we would like to predict how many times heads will come up.
Let $X$ be the random variable representing the number of heads that turn up; it is defined on the sample space $\Omega = \br{H,T}^2$.
The density function of $X$ is given by:

$$
\P(X = n) = 
   \begin{cases} 
      \frac{1}{4} & n = 0 \\ 
      \frac{1}{2} & n = 1 \\ 
      \frac{1}{4} & n = 2
   \end{cases}
$$

Thus its expectation is:

$$\E(X) = \sum_n n \P(X = n) = 1 \cdot \frac{1}{2} + 2 \cdot \frac{1}{4} = 1$$

Now let us impose the constraint that the first coin flip comes up heads.
The reader may be able to guess the expected value of $X$ under this constraint, but to be systematic we will use conditional distributions.
Let $Y$ be the random variable on $\Omega$ which takes the value $0$ if the first coin toss is tails and $1$ if it is heads.  The conditional density of $X$ given that $Y = 1$ is by definition:

$$
\P(X = n \vert Y = 1) = \frac{\P(X = n \cap Y = 1)}{\P(Y = 1)} = 
   \begin{cases}
      \frac{1}{2} & n = 1 \\
      \frac{1}{2} & n = 2
   \end{cases}
$$

The conditional expectation is therefore:

$$E(X \vert Y = 1) = 1 \cdot \frac{1}{2} + 2 \cdot \frac{1}{2} = \frac{3}{2}$$

This computation works more or less the same for any pair of discrete random variables $X$ and $Y$; it depends only on finding the conditional density function $\P(X = x \vert Y = y_0)$.  
It is crucial, however, that $\P(Y = y_0) \neq 0$; this is not a serious restriction since events of probability zero generally aren't very interesting in the discrete case, but as we shall see in the next section they cause quite a bit of trouble more generally.

## The Borel paradox

Conditional distributions and their expectations are quite a bit tricker for continuous random variables.
A historically impotant example which illustrates the main issues involved is the Borel paradox, which concerns the distribution of a uniformly randomly selected point on the unit sphere $S^2 \subseteq \R^3$ subject to the condition that the point lie on a prescribed great circle $C$.

Let us work in spherical coordinates $(\theta, \phi) \in [-\pi,\pi] \times [0,\pi]$.
Let $\Theta$ and $\Phi$ be the random variables which represent the $\theta$- and $\phi$-coordinates, respectively, of the random point.
The join density function of $\Theta$ and $\Phi$ which ensures that the point is chosen uniformly randomly is $f(\theta, \phi) = \frac{1}{4\pi}\sin \phi$; this is simply the Jacobian of the spherical coordinate transformation.
Following Borel, we will compute conditional density functions along two different great circles: the circle $\theta = 0$ and the circle $\phi = \frac{\pi}{2}$.

First, let us compute the marginal density functions $f_\Theta$ and $f_\Phi$ - each is obtained by integrating the joint density function against the other variable.
We get:

$$f_\Theta(\theta) = \frac{1}{2\pi}, \quad f_\Phi(\phi) = \frac{1}{2} \sin \phi$$

The as in the discrete case the conditional density function is simply the ratio of the joint density to the marginal density, so we get:

$$f(\theta \vert \phi = \pi/2) = \frac{f(\theta, \pi/2)}{f_\Phi(\pi/2)} = \frac{1}{2\pi}$$

and:

$$f(\phi \vert \theta = 0) = \frac{f(0, \phi)}{f_\Theta(0)} = \frac{1}{2} \sin \phi$$

This is, at first glance, a bit disturbing: all great circles should have the same conditional distribution since they differ only by rotations and the uniform probability measure is rotationally invariant, but we have exhibited great circles with both uniform and non-uniform conditional distributions.

Of course the paradox disappears upon closer inspection.
Edwin Jaynes argued that the computations above conceal a certain limiting procedure which is carried out differently for the two great circles.
A priori the conditional density function for a great circle $C$ ought to be recovered from probabilities of the form $\P(A \vert C)$ where $A$ is an arbitrary measurable subset of the sphere.
It is here that the division-by-zero issues that we safely ignored in the discrete case come back to bite us: the conditional probabilities ought to be defined by the formula:

$$\P(A \vert C) = \frac{\P(A \cap C)}{\P(C)}$$

but $C$ has measure $0$ in the sphere.
Behind the scenes the calculations with conditional density functions above work by thickening $C$ to a tiny neighborhood $C_\eps$ of the great circle.
This neighborhood will have positive measure for each $\eps > 0$, so we can try to define:

$$\P(A \vert C) = \lim_{\eps \to 0} \frac{\P(A \cap C_\eps)}{\P(C_\eps)}$$

The problem is that the limit depends on the neighborhoods $C_\eps$ used to compute it.
In the case where we condition on the circle $\phi = \frac{\pi}{2}$ the neighborhood has the form:

$$C_{\phi, \eps} = \br{(\theta, \phi) \colon \abs{\phi - \pi/2} < \eps}$$

When we condition on the cirlce $\theta = 0$ the neighborhood is given by:

$$C_{\theta, \eps} = \br{(\theta, \phi) \colon \abs{\theta} < \eps}$$

The neighborhood $C_{\phi, \eps}$ is the intersection of the sphere with the region in $\R^3$ between two planes which are parallel to the great cirlce; in particular it has constant thickness as $\theta$ varies, which explains why the limiting conditional distribution is uniform.
On the other hand the neighborhood $C_{\theta, \eps}$ is the intersection of the sphere with the region in $\R^3$ swept out as the plane containing the great circle is rotated; in the case of the great circle $\theta = 0$ the neighborhood is thickest at the equator and actually shrinks to a point at the poles.
This explains why the limiting distribution is non-uniform in this case.

## The "right" definition

Jaynes' thickening argument does strictly speaking resolve the Borel paradox, but it leaves the theory in a state of disarray.
We don't have any assurances that the limits needed to define the desired conditional probabilities will exist, and even when they do we know that the answers may depend sensitively on the thickening procedure used.
Also, it is not at all obvious how to apply this definition in a general probabily space, where there need not be a metric or topological structure lying around.

Fortunately, there is a much better definition which works in an abstract measure space.
An amusing twist is that this approach bypasses the conditional density function and constructs the conditional expectation directly; the conditional density function can then be recovered from the conditional expectation if desired.

The setup is as follows: let $(\Omega, \Sigma, \P)$ be a probability space, let $X$ be a random variable on this space, and let $\Sigma_0$ be a sub-$\sigma$-algebra of $\Sigma$.
Our aim is to define the conditional expectation of $X$ with respect to $\Sigma_0$; this will be a $\Sigma_0$-measurable random variable, and by choosing $\Sigma_0$ carefully we will be able to replicate both the discrete and continuous computations above.
To begin, note that $\P$ restricts to a measure $\P_0$ (not necessarily a probability measure!) on $\Sigma_0$.
Define a new measure $\mu$ on $\Sigma_0$ by:

$$\mu(E) = \int_E X\, d\P$$

(Note that $X$ is not $\Sigma_0$-measurable in general so we cannot integrate with respect to $\P_0$, but we can integrate $X$ over sets in $\Sigma_0$ with respect to $\P$.)
For any $E \in \Sigma_0$, if $\P_0(E) = 0 $ then clearly $\P(E) = 0$ and thus $\mu(E) = 0$, so $\mu$ is absolutely continuous with respect to $\P_0$.

<div class="definition">
With the notation above, the *conditional expectation* of $X$ with respect to $\Sigma_0$ is defined to be the Radon-Nikodym derivative:

$$\E(X \vert \Sigma_0) = \frac{d\P_0}{d\mu}$$

</div>

This definition is a bit brutal, and it is not at all obvious how it is related to the more down-to-earth computations with coin flips and great circles that we started with.
We will go through those examples again in the next section, but a few preliminary remarks are in order.

First, note that the object $\E(X \vert \Sigma_0)$ is a $\Sigma_0$-measurable function $\Omega \to \R$.
This function is uniquely characterized by the equation:

$$\int_E X\, d\P = \mu(E) = \int_E \E(X \vert \sigma_0)\, d\P_0$$

It is not clear how such an object is related to the quantities $\E(X \vert Y = y_0)$, which are real numbers, that we considered in our examples.
Where does the $\sigma$-algebra fit in, and how do we use it to actually do calculations?

To answer these questions, let us adapt our construction to $Y$.
Pull back the Borel $\sigma$-algebra on $\R$ along $Y$ to obtain a $\sigma$-algebra $\Sigma_Y$ on $\Omega$; since $Y$ is $\Sigma$-measurable, this is a sub-$\sigma$-algebra of $\Sigma$.
Define a probability measure $\P_Y$ on $\R$ by $\P_Y(B) = \P(Y^{-1}(B))$.
As before, the map $\P_X(B) = \int_{Y^{-1}(B)} X\, d\P$ is a measure on $\R$ which is absolutely continuous with respect to $\P_Y$, so let us denote by $E(X \vert Y)$ the corresponding Radon-Nikodym derivative.  

$E(X \vert Y)$ is a function $\R \to \R$ which is characterized by the equation:

$$\int_{Y^{-1}(B)} X\, d\P = \int_B \E(X \vert Y)\, d\P_Y$$

This function allows us to condition $X$ against the values of $Y$: we simply define $\E(X \vert Y = y_0)$ to be the value of $\E(X \vert Y)$ at the point $y_0$.
(Note: it was not essential in this discussion that $Y$ took values in $\R$ - any measurable space would do.)

While it is good enough to work with $\E(X \vert Y)$ when doing calculations, this function is closely related to $\E(X \vert \Sigma_Y)$.
Using the notation $\P_{\Sigma_Y}$ for the restriction of $\P$ to $\Sigma_Y$, we can pull back the integral on the right-hand side to $\Omega$:

$$\int_{Y^{-1}(B)} X\, d\P = \int_{Y^{-1}(B)} \E(X \vert Y) \circ Y\, d\P_{\Sigma_Y}$$

But this is precisely the equation which characterizes $\E(X \vert \Sigma_Y)$, so we conclude:

$$\E(X \vert \Sigma_Y) = \E(X \vert Y) \circ Y$$

This equation holds almost everywhere with respect to $\P_{\Sigma_Y}$.

## Examples revisited 

We conclude by redoing our two example calculations - the conditional expectation of the coin flip and the conditional distribution of a great circle on the sphere - using the language of the previous section.
In both cases we'll only need to work with the function $\E(X \vert Y)$, but there are some nice computations which use conditional expectations relative to a $\sigma$-algebra in an essential way.
Perhaps I'll revisit some of those computations in a future post.

### Coin flips

Recall: the sample space is given by $\Omega = \br{H,T}^2$, and the $\sigma$-algebra $\Sigma$ is simply the power set of $\Omega$.
The measure $\P$ is the uniform measure on $\Omega$, i.e. it assigns the probability $\frac{1}{4}$ to each point in $\Omega$.
The random variable $X$ maps each point in $\Omega$ to the number of times $H$ appears; thus $X(TT) = 0$, $X(HT) = X(TH) = 1$, and $X(HH) = 2$.

We wish to condition against the event in which the first coin flip comes up heads.
As before let $Y$ be the random variable which takes the value $0$ if the first coin flip is tails and $1$ if it is heads.
We wish to compute the function $\E(X \vert Y)$, and to this end we must calculate the measures $\P_Y$ and $\P_X$.
These are both measures on $\R$ which factor through $Y$, so in fact they are supported on the set $\br{0, 1}$ (the range of $Y$).

To calculate $P_Y$, we compute the values of $\P$ on the level sets of $Y$:

$$\P_Y(\br{1}) = \P(Y^{-1}(1)) = \P(\br{HT, HH}) = \frac{1}{2}$$

A similar calculation shows that $\P_Y(\br{0}) = \frac{1}{2}$ as well.
To compute $\P_X$ we integrate $X$ over the level sets of $Y$:

$$
\begin{align*}
   \P_X(\br{1}) &= \int_{Y^{-1}(1)} X\, d\P \\
      &= \int_{\br{HT, HH}} X\, d\P \\
      &= X(HT)\P(HT) + X(HH)\P(HH) \\
      &= 1 \cdot \frac{1}{4} + 2 \cdot \frac{1}{4} \\
      &= \frac{3}{4}
\end{align*}
$$

A similar calculation gives $\P_X(\br{0}) = \frac{1}{4}$.

The conditional expectation of $X$ given $Y$ is the Radon-Nikodym derivative of $P_Y$ with respect to $P_X$.
Following the warm-up calculation in my previous post on the Radon-Nikodym derivative, we get:

$$
\begin{align*}
   \E(X \vert Y = 1) &= \E(X \vert Y)(1) \\
      &= \frac{d\P_X}{d\P_Y}(1) \\
      &= \frac{\P_X(\br{1})}{\P_Y(\br{1})} \\
      &= \frac{3/4}{1/2} \\
      &= \frac{3}{2}
\end{align*}
$$

(A similar calculation gives $E(X \vert Y = 0) = \frac{1}{2}$.)

### Borel Paradox

The Borel paradox concerns the distribution of a random point on the sphere given that the point lies in a prescribed great circle.
To begin, let us discuss how the language of conditional expectations can be used to compute conditional probabilities for a random variable.

Let $E$ be a measurable set in a probability space $(\Omega, \Sigma, \P)$ and let $1_E$ denote its indicator function.
$1_E$ can be viewed as a random variable on $\Omega$, and by definition $\E(1_E) = \P(E)$.    
This suggests how to define the conditional probability of an event $E$ given a real-valued random variable $Y$:

$$\P(E \vert Y) = \E(1_E \vert Y)$$

Thus $\P(E \vert Y)$ is a measurable function on $\R$ which is characterized by the equation:

$$\int_B \P(E \vert Y)\, d\P_Y = \int_{Y^{-1}(B)} 1_E\, d\P = \P(E \cap Y^{-1}(B))$$

where $B$ is an arbitrary Borel measurable set in $\R$.

So let us return to great circles on the sphere.
Working in spherical coordinates, we aim to compute the conditional distribution of the random variable $\Phi$ with respect to the great circle $\theta = 0$ and the conditional distribution of the random variable $\Theta$ with respect to the great circle $\phi = \frac{\pi}{2}$.

Recall that $E(\Phi \vert \Theta)$ is by definition the Radon-Nikodym derivative $\frac{d\P_\Phi}{d\P_\Theta}$.

$\P_\Theta$ is the restriction of the standard probability measure $\P$ on the sphere to the pullback along $\Theta$ of the Borel measure on $\R$.
In other words, $\P_\Theta$ is defined on the $\sigma$-algebra on $S^2$ generated by sets of the form $\theta_0 < \theta < \theta_1$, and it is calculated by integrating the spherical density function $f(\theta, \phi) = \frac{1}{4\pi} \sin \phi$ over these sets.
Explicitly:

$$
\begin{align*}
   \P_\Theta(
\end{align*}
$$

$\P_\Phi$ is the measure given by:

$$\P_\Phi(B) = \int_{\Theta^{-1}(B)} \Phi\, d\P$$

where $B$ is a Borel measurable subset of $\R$.  We can calculate this explicitly on a set of the form $B = (\theta_0, \theta_1)$:

$$
\begin{align*}
   \P_\Phi(B) &= \int_{\theta_0}^{\theta_1} \int_0^\pi \frac{1}{4\pi} \phi \sin \phi\, d\phi\, d\theta \\
      &= \frac{1}{4}\left( \theta_1 - \theta_0 \right)
\end{align*}
$$


