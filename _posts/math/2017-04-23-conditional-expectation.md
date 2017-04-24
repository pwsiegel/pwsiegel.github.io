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
\P(X = n | Y = 1) = \frac{\P(X = n \cap Y = 1)}{\P(Y = 1)} = 
\begin{cases}
   \frac{1}{2} & n = 1 \\
   \frac{1}{2} & n = 2
\end{cases}
$$

The conditional expectation is therefore:

$$E(X | Y = 1) = 1 \cdot \frac{1}{2} + 2 \cdot \frac{1}{2} = \frac{3}{2}$$

This computation works more or less the same for any pair of discrete random variables $X$ and $Y$; it depends only on finding the conditional density function $\P(X = x | Y = y_0)$.  
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

$$f(\theta | \phi = \pi/2) = \frac{f(\theta, \pi/2)}{f_\Phi(\pi/2)} = \frac{1}{2\pi}$$

and:

$$f(\phi | \theta = 0) = \frac{f(0, \phi)}{f_\Theta(0)} = \frac{1}{2} \sin \phi$$

This is, at first glance, a bit disturbing: all great circles should have the same conditional distribution since they differ only by rotations and the uniform probability measure is rotationally invariant, but we have exhibited great circles with both uniform and non-uniform conditional distributions.

Of course the paradox disappears upon closer inspection.
Edwin Jaynes argued that the computations above conceal a certain limiting procedure which is carried out differently for the two great circles.
A priori the conditional density function for a great circle $C$ ought to be recovered from probabilities of the form $\P(A|C)$ where $A$ is an arbitrary measurable subset of the sphere.
It is here that the division-by-zero issues that we safely ignored in the discrete case come back to bite us: the conditional probabilities ought to be defined by the formula:

$$\P(A|C) = \frac{\P(A \cap C)}{\P(C)}$$

but $C$ has measure $0$ in the sphere.
Behind the scenes the calculations with conditional density functions above work by thickening $C$ to a tiny neighborhood $C_\eps$ of the great circle.
This neighborhood will have positive measure for each $\eps > 0$, so we can try to define:

$$\P(A|C) = \lim_{\eps \to 0} \frac{\P(A \cap C_\eps)}{\P(C_\eps)}$$

The problem is that the limit depends on the neighborhoods $C_\eps$ used to compute it.
In the case where we condition on the circle $\phi = \frac{\pi}{2}$ the neighborhood has the form:

$$C_{\phi, \eps} = \br{(\theta, \phi) \colon \abs{\phi - \pi/2} < \eps}$$

When we condition on the cirlce $\theta = 0$ the neighborhood is given by:

$$C_{\theta, \eps} = \br{(\theta, \phi) \colon \abs{\theta} < \eps}$$

The neighborhood $C_{\phi, \eps}$ is the intersection of the sphere with the region in $\R^3$ between two planes which are parallel to the great cirlce; in particular it has constant thickness as $\theta$ varies, which explains why the limiting conditional distribution is uniform.
On the other hand the neighborhood $C_{\theta, \eps}$ is the intersection of the sphere with the region in $\R^3$ swept out as the plane containing the great circle is rotated; in the case of the great circle $\theta = 0$ the neighborhood is thickest at the equator and actually shrinks to a point at the poles.
This explains why the limiting distribution is non-uniform in this case.

## The "right" definition

The Borel paradox has strictly speaking been resolved, but the resolution leaves the theory in a state of disarray.
The closest we have to a definition of conditional density function (let alone conditional expectation) involves thickening the event that we would like to condition against so that it has positive measure and then passing to the limit as the thickness parameter goes to zero.
But we don't have any assurances that this limit will exist, and even when it does we know that the limit may depend sensitively on the thickening procedure.
Also, it is not at all obvious how to apply this definition in a general probabily space, where there need not be a metric or topological structure lying around.

Fortunately, there is a much better definition which works in an abstract measure space.
An amusing twist is that this approach bypasses the conditional density function and constructs the conditional expectation directly; the conditional density function can then be recovered from the conditional expectation if desired.

The setup is as follows: let $(\Omega, \Sigma, \P)$ be a probability space, let $X$ be a random variable on this space, and let $\Sigma_0$ be a sub-$\sigma$-algebra of $\Sigma$.
Our aim is to define the conditional expectation of $X$ with respect to $\Sigma_0$; this will be a $\Sigma_0$-measurable random variable, and by choosing $\Sigma_0$ carefully we will be able to replicate both the discrete and continuous examples above.
To begin, note that $\P$ restricts to a measure $\P_0$ (not necessarily a probability measure!) on $\Sigma_0$.
Define a new measure $\P_X$ on $\Sigma_0$ by:

$$\P_X(E) = \int_E X\, d\P$$

(Note that $X$ is not $\Sigma_0$-measurable in general so we cannot integrate with respect to $\P_0$, but we can integrate $X$ over sets in $\Sigma_0$ with respect to $\P$.)
For any $E \in \Sigma_0$, if $\P_0(E) = 0 $ then clearly $\P(E) = 0$ and thus $\P_X(E) = 0$, so $\P_X$ is absolutely continuous with respect to $\P_0$.

<div class="definition">
With the notation above, the *conditional expectation* of $X$ with respect to $\Sigma_0$ is defined to be the Radon-Nikodym derivative:

$$\E(X|\Sigma_0) = \frac{d\P_0}{d\P_X}$$
</div>

The conditional expectation is a $\Sigma_0$-measurable function which is uniquely characterized by the equation:

$$\int_E X\, d\P = \int_E \E(X|\Sigma_0)\, d\P_0$$

## Examples revisited 

The abstract definition of conditional expectation  gains a lot in generality and theoretical soundness, but it is not at all clear what relevance it has to the examples we have considered so far.

### Coin flips

Recall: the sample space is given by $\Omega = \br{H,T}^2$, and the $\sigma$-algebra $\Sigma$ is simply the power set of $\Omega$.
The measure $\P$ is the uniform measure on $\Omega$, i.e. it assigns the probability $\frac{1}{4}$ to each point in $\Omega$.
The random variable $X$ maps each point in $\Omega$ to the number of times $H$ appears; thus $X(TT) = 0$, $X(HT) = X(TH) = 1$, and $X(HH) = 2$.

We wish to condition against the event in which the first coin flip comes up heads.
As before let $Y$ be the random variable which takes the value $0$ if the first coin flip is tails and $1$ if it is heads.
The pullback under $Y$ of the Borel $\sigma$-algebra on $\R$ is a $\sigma$-algebra $\Sigma_0$ on $\Omega$; explicitly, it is:

$$\Sigma_0 = \br{\emptyset, Y_0 = \br{TT, TH}, Y_1 = \br{HT, HH}, \Omega}$$

(These sets are just the inverse images of $\emptyset$, $\br{0}$, $\br{1}$, and $\R$ under $Y$.)
Let us compute the measures $\P_0$ and $\P_X$. We have:

$$\P_0(Y_0) = \P(Y_0) = \frac{1}{2}, \quad \P_0(Y_1) = \P(Y_1) = \frac{1}{2}$$

To compute $P_X$ we must integrate:

$$\P_X(Y_0) = \int_{Y_0} X\, d\P = X(TT)\P(TT) + X(TH) \P(TH) = 0 \cdot \frac{1}{4} + 1 \cdot \frac{1}{4} = \frac{1}{4}$$

A similar computation gives $\P_X(Y_1) = \frac{3}{4}$.

Now, the conditional expectation $\E(X|\Sigma_0)$ is a $\Sigma_0$-measurable function $\Omega \to \R$; $\Sigma_0$-measurability implies that it is constant on $Y_0$ and $Y_1$ (and therefore lifts to a well-defined function on the values of $Y$.)
Together with the equation $\P_X(E) = \int_E \E(X|\Sigma_0)\, d\P_0$, we are forced to set:

$$\E(X|\Sigma_0)(\omega) = \frac{\P_X(Y_0)}{\P_0(Y_0)} = \frac{1/4}{1/2} = \frac{1}{2}$$

for $\omega \in Y_0$ and:

$$\E(X|\Sigma_0)(\omega) = \frac{\P_X(Y_1)}{\P_0(Y_1)} = \frac{3/4}{1/2} = \frac{3}{2}$$

for $\omega \in Y_1$.
So we can recover $\E(X|Y=1)$ by plugging any element of $Y_1$ into the function $\E(X|\Sigma_0)$; this gives the value $\frac{3}{2}$, which agrees with our answer using conditional density functions.

### Borel Paradox
