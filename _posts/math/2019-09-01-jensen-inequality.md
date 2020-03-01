---
layout: post
permalink: /ds/jensen-inquality/
title: Jensen's Inequality and Statistics
abstract: Jensen's inequality is a beautiful little workhorse lemma from convex geometry. I have stumbled into it an number of times recently as I have been reading and writing about statistics and information theory, so I decided to split it off into its own blog post.  After introducing the inequality (and proving that it holds), I will give a few snappy applications to basic statistics, including the classical inequality between arithmetic, geometric, and harmonic means as well as a basic inequality relating the mean, median, and standard deviation.
date: 2019-09-01
categories: [math, data-science]
tags: live
comments: true
---

This post is all about Jensen's inequality, an elementary but powerful result about convex functions.
It comes up all over the place in mathematics, probably because the concept of convexity is itself so fundamental.
I ran into it recently as I was thinking about relative entropy and why it is so well behaved; I think a lot of it comes down to the fact that the relative entropy from one probability distribution to another is always positive - a result known as _Gibbs' inequality_ - and this follows very easily from Jensen's inequality.

But that will be for another blog post; in this one I will carefully formulate and prove Jensen's inequality and give a few fun applications in basic statistics.

## Convex functions

Before even stating Jensen's inequality, we need to go over the basics of the theory of convex functions.
A function is said to be convex is the set of points in the plane which lie above its graph form a convex set.
More formally:

<div class="definition">
Let $f \colon I \to \R$ be a function defined on an interval $I \subseteq \R$.
$f$ is _convex_ if $f(t a + (1-t) b) \leq t f(a) + (1-t) f(b)$ for all $a, b \in I$ and all $t \in [0,1]$.
</div>

The left-hand side of the inequality represents the values of $f$ at points between $a$ and $b$, while the right-hand side represents values of the line joining the points $(a, f(a))$ and $(b, f(b))$; thus the inequality says that the graph lies entirely below any line joining two of its points.

Just staring at the definition, it is not clear how one would go about constructing any examples.
Those who remember their calculus may think of the second derivative: if $f^{(2)}(x) > 0$ for all $x$ then it means that $f$ "curves up" in some sense, and this feels like it ought to imply that $f$ is convex.
Not all convex functions are twice (or even once!) differentiable, but this intuition is on the right track.
We can formalize it by introducing a generalization of the concept of a tangent line, which proceeds by means of the following lemma:

<div class="lemma">
Let $f \colon I \to \R$ be a convex function defined on an interval $I \subseteq \R$.
For any $a, x_0, b \in I$ such that $a < x_0 < b$, we have:

$$
\frac{f(x_0) - f(a)}{x_0 - a} \leq \frac{f(b) - f(a)}{b - a} \leq \frac{f(b) - f(x_0)}{b - x_0}
$$

</div>
<div class="proof">
Set $t = \frac{b-x_0}{b-a}$, so that $1 - t = \frac{x_0-a}{b-a}$; the reader may check that for this choice of $t$ we have $x_0 = t a + (1-t) b$.
By convexity, we have:

$$
\begin{align*}
f(x_0) &\leq t f(a) + (1-t) f(b) \\
&= \frac{b-x_0}{b-a} f(a) + \frac{x_0-a}{b-a}f(b) \\
&= x_0 \frac{f(b) - f(a)}{b - a} + \frac{b f(a) - a f(b)}{b - a}
\end{align*}
$$

Subtract $f(a)$ from both sides to obtain:

$$
\begin{align*}
f(x_0) - f(a) &\leq x_0 \frac{f(b) - f(a)}{b - a} + \frac{b f(a) - a f(b)}{b - a} - f(a) \\
&= x_0 \frac{f(b) - f(a)}{b - a} + \frac{b f(a) - a f(b)}{b - a} - \frac{b f(a) - a f(a)}{b - a} \\
&= x_0 \frac{f(b) - f(a)}{b - a} - a \frac{f(b) - f(a)}{b - a} \\
&= (x_0 - a) \frac{f(b) - f(a)}{b - a}
\end{align*}
$$

Dividing both sides through by $(x_0 - a)$ gives the first half of the claimed inequality.
To get the second half subtract $f(b)$ instead of $f(a)$, multiply through by $-1$, and proceed as above.
</div>

Now, define

$$m^- = \sup \left\{ \frac{f(x_0) - f(a)}{x_0 - a} \colon a \in I,\, a < x_0 \right\}, \quad m^+ = \inf \left\{ \frac{f(b) - f(x_0)}{b - x_0} \colon b \in I,\, b > x_0 \right\}$$

Both $m^-$ and $m^+$ are finite by the previous lemma, the reader may check that $m^- \leq m^+$.

<div class="exercise">
Show that $m^- = m^+$ if and only if $f$ is differentiable at $x_0$, and in that case $m^- = m^+ = f'(x_0)$.
</div>

<div class="exercise">
Let $f(x) = \abs{x}$ and let $x_0 = 0$.
What are $m^-$ and $m^+$?
</div>

The main significance of $m^-$ and $m^+$ for our purposes is that they can be used to construct lines which behave a little bit like tangent lines for $f$, in the sense of the following lemma:

<div class="lemma">
Let $f \colon I \to \R$, $x_0$, $m^-$, and $m^+$ be defined as above.
For any $m \in [m^-, m^+]$, define $\ell(x) = f(x_0) + m(x - x_0)$.
Then $\ell(x) \leq f(x)$ for all $x \in I$.
</div>
<div class="proof">
To begin, suppose $x < x_0$.
By definition, we have:

$$m \geq m^- \geq \frac{f(x_0) - f(x)}{x_0 - x} = \frac{f(x) - f(x_0)}{x - x_0}$$

So since $(x - x_0)$ is negative we get:

$$\ell(x) = f(x_0) + m(x - x_0) \leq f(x_0) + \frac{f(x) - f(x_0)}{x - x_0} (x - x_0) = f(x)$$

The case $x > x_0$ follows similarly, using the inequality $m \leq m^+$ instead.
</div>

Because of this lemma, numbers in the interval $[m^-, m^+]$ are often called _subderivatives_ of $f$ at $x_0$.
We're more interested in the lines themselves, and we'll give them a special name:

<div class="definition">
Let $f \colon I \to \R$ be a function defined on an interval $I$.
A _supporting line_ for $f$ at $x_0 \in I$ is a linear function $\ell(x)$ with the property that $\ell(x_0) = f(x_0)$ and $\ell(x) \leq f(x)$ for all $x \in I$.
</div>

<div class="exercise">
Show that $f(x) = x^3$ has no supporting line at $x_0 = 0$.
</div>

The existence of supporting lines at every point characterizes convexity, as per the next proposition:

<div class="proposition">
Let $f \colon I \to \R$ be a function defined on an interval $I$.
Then $f$ is convex if and only if there is a supporting line for $f$ at every point $x_0 \in I$.
</div>
<div class="proof">
If $f$ is convex then the previous lemma implies that $\ell(x) = f(x_0) + m(x - x_0)$ is a supporting line to $f$ at $x_0$ for any subderivative $m$.

So assume that $f$ has a supporting line at every point; we aim to show that $f$ is convex.
Following the definition of convexity, fix $a, b \in I$ and let $x_t = t a + (1-t)b$ for arbitrary $t \in [0,1]$.
Let $\ell(x)$ be a supporting line for $f$ at $x_t$; we have:

$$
\begin{align*}
f(t a + (1 - t)b) &= f(x_t) \\
&= \ell(x_t) \\
&= t \ell(a) + (1-t) \ell(b) \quad \text{by linearity} \\
&= \leq t f(a) + (1-t) f(b)
\end{align*}
$$

Since $a$, $b$, and $t$ were arbitrary, $f$ is convex.
</div>

The condition on supporting lines is often easier to use in examples than the definition of convexity because it can be checked pointwise.
To conclude our discussion of convex functions, let us use calculus to show that a lot of familiar functions like $f(x) = e^x$ or $f(x) = x^2$ are convex.

<div class="proposition">
Let $f \colon I \to \R$ be a twice continuously differentiable function on an interval $I$.
If $f^{(2)}(x) \geq 0$ for all $x \in I$ then every tangent line to $f$ is a supporting line and in particular $f$ is convex.
</div>
<div class="proof">
Our strategy is to use the Lagrange form of the error term in Taylor's theorem.
This asserts that for any base point $x_0 \in I$ and any other point $x \in I$ there is some $c$ between $x_0$ and $x$ such that:

$$f(x) = f(x_0) + f'(x_0)(x - x_0) + \frac{f^{(2)}(c)}{2!}(x - x_0)^2$$

The tangent line $\ell(x) = f(x_0) + f'(x_0)(x - x_0)$ clearly satisfies $\ell(x_0) = f(x_0)$, and we have $\ell(x) \leq f(x)$ since $f^{(2)}(c) \geq 0$.
</div>

## Jensen's inequality

We are now ready to formulate and prove Jensen's inequality.
It is an assertion about how convex functions interact with expected values of random variables, and we will formulate it on an abstract measure space $(\Omega, \Sigma, \P)$ where $\Omega$ is a set, $\Sigma$ is a $\sigma$-algebra of subsets of $\Sigma$, and $\P$ is a probability measure on $(\Omega, \Sigma)$.
Readers uncomfortable with this formalism are welcome to restrict to the case where $\Omega = \br{\omega_1, \ldots, \omega}$ is a finite set for which we assign probabilities $\P(\omega_i) = p_i$ where the $p_i$'s are nonnegative and sum to $1$.
This is enough for the applications in this post!

Given a random variable $X \colon \Omega \to \R$, recall that the expectation of $X$ is by definition:

$$\E(X) = \int_\Omega X\, d\P$$

In the case where $\Omega$ is finite, this just reduces to $\E(X) = \sum_{i=1}^n p_i X(\omega_i)$.
We will only really use three properties of the expectation operator:

- $\E$ is monotone: if $X \leq Y$ almost everywhere with respect to $\P$ then $\E(X) \leq \E(Y)$
- $\E$ is linear: if $X$ and $Y$ are random variables and $a, b \in \R$ then $\E(a X + b Y) = a \E(X) + b \E(Y)$ 
- $\E$ is positive definite: if $X \geq 0$ and $\E(X) = 0$ then $X = 0$ almost everywhere with respect to $\P$.

Finally, given a random variable $X \colon \Omega \to \R$ and a function $f \colon \R \to \R$, recall that $f(X)$ is by definition the random variable $f \circ X \colon \Omega \to \R$.
This still makes sense even if $f$ is defined only on the range of $X$.

<div class="theorem">
Let $X$ be a random variable defined on a probability space $(\Omega, \Sigma, \P)$ and let $f \colon I \to \R$ be a convex function defined on an interval $I \subseteq \R$ containing the range of $X$.
Then:

$$f(\E(X)) \leq \E(f(X))$$

with equality if and only if either $X$ is constant or $f$ coincides with a line on an interval which contains the range of $X$ (up to sets of measure zero).
</div>
<div class="proof">
Since $f$ is convex it admits a supporting line $\ell$ at the point $x_0 = \E(X)$.
This means $\ell(\E(X)) = f(\E(X))$ and $\ell(x) \leq f(x)$ for all $x \in I$; in particular, $\ell(X) \leq f(X)$.
By monotonicity and linearity of expectation, we get:

$$\E(f(X)) \geq \E(\ell(X)) = \ell(\E(X)) = f(\E(X))$$

To handle the equality case, observe that:

$$\E(f(X)) - \E(\ell(X)) = \E((f-\ell)(X)) = \int_\Omega (f - \ell) \circ X\, d\P$$

But the integrand is nonnegative since $\ell(x) \leq f(x)$, so the equality case is equivalent to $(f - \ell) \circ X = 0$ almost everywhere, or in other words $f = \ell$ almost everywhere on the range of $X$.

Let $R$ denote the set of all points in the range of $X$ at which $f = \ell$.
If $R$ contains only one point then $X$ is constant almost everywhere; otherwise let $a < b$ be two distinct points in $R$.
Any point between $a$ and $b$ can be expressed as $x_t = t a + (1-t)b$ for some $t \in [0,1]$, and we have:

$$\ell(x_t) \leq f(x_t) \leq t f(a) + (1-t) f(b) = t \ell(a) + (1-t) \ell(b) = \ell(t a + (1-t) b) = \ell(x_t)$$

Plainly, this forces $f(x_t) = \ell(x_t)$.
It follows that $f = \ell$ on the interval $[a,b]$, and by taking the union over all such pairs we see that $f = \ell$ on the interval $(\inf R, \sup R)$ which contains the range of $X$ up to a set of measure zero.
</div>

The proof of this result was relatively straightforward, but it depended crucially on all of the setup work that we did involving convex functions and supporting lines.
We'll see this pay off in the next section.

## Applications

We will give two applications here: a classical inequality involving different sorts of averages and an inequality involving the mean, median, and standard deviation.
I don't know significantly a significantly easier way to deduce either inequality.

### Arithmetic, geometric, and harmonic means

Let $x_1, \ldots, x_n$ be a list of positive numbers.
There are a number of different notions of "average" for such a list - I wrote an elementary blog post about the subject [here][1].
These include:

- Arithmetic mean: 

$$A(x_1, \ldots, x_n) = \frac{x_1 + \ldots + x_n}{n}$$

- Geometric mean: 

$$G(x_1, \ldots, x_n) = (x_1 \cdot \ldots \cdot x_n)^{\frac{1}{n}}$$

- Harmonic mean: 

$$H(x_1, \ldots, x_n) = \frac{n}{\frac{1}{x_1} + \ldots + \frac{1}{x_n}}$$

Our main result is that the arithmetic mean is always larger than the geometric mean, which in turn is always larger than the harmonic mean.
This is difficult to prove!
The approach using Jensen's inequality is by far the simplest that I know.

The first step is also perhaps the cleverest: to introduce probabilistic language.
Let $\Omega = \br{\omega_1, \ldots, \omega_n}$ be a set with $n$ elements, and introduce the unform probability measure determined by $\P(\omega_i) = \frac{1}{n}$ for each $i$.
Define a random variable $X$ on $\Omega$ by $X(\omega_i) = x_i$, and for short write $A(X)$ instead of $A(x_1, \ldots, x_n)$ and similarly for $G$ and $H$.

The proof of the inequality will use several identities which follow immediately from the definitions (and are left to the reader):

1. $A(X) = \E(X)$
2. $G(X) = e^{\E(\log X)}$
3. $H(X) = \frac{1}{\E(1/X)}$
4. $G(X) = \frac{1}{\E(1/X)}$

<div class="proposition">
For any finite set $x_1, \ldots, x_n$ of positive numbers: 

$$H(x_1, \ldots, x_n) \leq G(x_1, \ldots, x_n) \leq A(x_1, \ldots, x_n)$$

with equality if and only if $x_1 = \ldots = x_n$.
</div>
<div class="proof">
We'll start with the inequality $G(X) \leq A(X)$.
Consider the function $f(x) = -\log x$ defined on the interval $(0, \infty)$ (which contains the range of $X$ since the $x_i$'s are assumed to be positive).
We have $f^{(2)}(x) = \frac{1}{x^2} > 0$ for all $x$, so by the final result in our discussion of convex functions we know that $f$ is convex.
Thus we may apply Jensen's inequality to get:

$$-\log \E(X) \leq \E(-\log X)$$

We can remove the minus signs at the cost of reversing the inequality:

$$\log \E(X) \geq \E(\log X)$$

Exponentiating both sides preserves the inequality since $e^x$ is an increasing function of $x$, so we get:

$$\E(X) \geq e^{\E(\log X)}$$

Using identities 1 and 2 above, we recognize the left-hand side as $A(X)$ and the right-hand side as $G(X)$, as desired.

To handle the equality case, recall that Jensen's inequality is an equality if and only if $X$ is constant or $f$ coincides with a linear function on an interval containing the range of $X$.
But the logarithm function does not restrict to a linear function on any interval, so we get equality between the arithmetic and geoemetric mean if and only if all of the $x_i$'s are equal.

The inequality involving the harmonic and geometric means follows from the arithmetic / geometric mean inequality applied to the random variable $\frac{1}{X}$ (together with identities 3 and 4):

$$H(X) = \frac{1}{\E(1/X)} = \frac{1}{A(1/X)} \leq \frac{1}{G(1/X)} = G(X)$$

The analysis of the equality case follows from the arithmetic / geometric mean equality analysis.
</div>

In addition to being simple and elegant, this argument generalizes with basically no extra effort to the case of weighted averages and to continuous probability distributions thanks to the generality in which we formulated Jensen's inequality.

### How far is the median from the mean?

Here is a very simple question coming from statistics: given a finite sequence $x_1 \leq \ldots \leq x_n$ of numbers, what is the furthest the median can be from the (arithmetic) mean?
Recall that the median is by definition the value $x_i$ occurring at index $i = \frac{n+1}{2}$ if $n$ is odd, and if $n$ is even it is the average of the values $x_i$ and $x_{i+1}$ where $i = \frac{n}{2}$.
(In fact, any value between $x_i$ and $x_{i+1}$ would do for the present purposes.)

This question has a very simple answer: the median is always within one standard deviation of the mean.
This is a very elementary statement, but I don't think it is easy to prove - one way or the other some mechanism for estimating expectations of random variables is required.

To begin, we will need a clever variational characterization of the median, which is interesting in its own right.
As in the previous section, equip the finite set $\Omega = \br{\omega_1, \ldots, \omega_n}$ with the uniform probability measure, and let $X$ be the random variable on this probability space defined by $X(\omega_i) = x_i$.

<div class="proposition">
The minimum value of the _absolute deviation function_ $f(t) = \E(\abs{X - t})$ occurs at the median of $X$.
</div>
<div class="proof">
Assume the $x_i$'s are arranged in increasing order, so that $x_1 \leq \ldots \leq x_n$.
For any $t < x_1$ we have $\abs{x_i - t} > \abs{x_i - x_1}$ for all $i$, so $f$ cannot be minimized at a value of $t$ smaller than $x_1$.
Similarly, $f$ cannot be minimized at a value of $t$ larger than $x_n$.
Thus we can restrict $f$ to the interval $[x_1, x_n]$; $f$ is clearly continuous, so we know that $f$ attains its minimum value somewhere by compactness.

For any $t \in [x_1, x_n]$ we have:

$$\abs{x_1 - t} + \abs{x_n - t} = t - x_1 + x_n - t = x_n - x_1$$

Thus at such $t$ we have:

$$f(t) = \E(\abs{X - t}) = \frac{1}{n} \sum_{i=1}^n \abs{x_i - t} = \frac{x_n - x_1}{n} + \frac{1}{n} \sum_{i=2}^{n-1} \abs{x_i - t}$$

It follows that $f$ attains its minimum value at the same point as the absolute deviation function for the points $x_2, \ldots, x_{n-1}$.
Applying this argument inductively, we reduce either to the one point set $\br{x_{(n+1)/2}}$ if $n$ is odd or the two point set $\br{x_{n/2}, x_{n/2+1}}$ if $n$ is even.

In the first case, the absolute deviation function is $t \mapsto \abs{x_{(n+1)/2} - t}$ which is minimized precisely at $t = x_{(n+1)/2}$.
In the second case, the absolute deviation function is $t \mapsto \abs{x_{n/2} - t} + \abs{x_{n/2+1} - t}$, and as above this is minimized when $t \in [x_{n/2}, x_{n/2+1}]$ where it takes the constant value $x_{n/2+1} - x_{n/2}$.
In either case, the minimum value occurs at the median.
</div>

We're now ready to prove the main result.

<div class="proposition">
Let $\mu$ denote the arithmetic mean of the numbers $x_1, \ldots, x_n$, let $m$ denote their median, and let $\sigma = \sqrt{E((X - \mu)^2)}$ denote their standard deviation.
Then:

$$\abs{\mu - m} \leq \sigma$$

with equality if and only if the $x_i$'s are all equal.
</div>
<div class="proof">
Let $X$ denote the random variable whose values are $x_1, \ldots, x_n$ as above.
Clearly $\mu - m = \E(X) - m = \E(X - m)$.
The function $x \mapsto \abs{X}$ is convex ($\ell(x) = x$ is a supporting line at all $x_0 \geq 0$, and $\ell(x) = -x$ is a supporting line at all $x_0 \leq 0$), so by Jensen's inequality we have:

$$\abs{\mu - m} = \abs{\E(X - m)} \leq \E(\abs{X - m})$$

According to the previous proposition $\E(\abs{X - m})$ is the minimum value of the function $t \mapsto \E(\abs{X - t})$, so plugging in $t = \mu$ we get:

$$\E(\abs{X - m}) \leq \E(\abs{X - \mu})$$

The function $x \mapsto x^2$ is convex (it's second drivative is $2$ at all points), so by Jensen's inequality again we have:

$$\E(\abs{X - \mu})^2 \leq \E((X - \mu)^2)$$

Taking square roots and putting it all together, we get:

$$\abs{\mu - m} \leq \E(\abs{X - \mu}) \leq \sqrt{\E((X - \mu)^2)} = \sigma$$

Note that the equality case for the second application of Jensen's inequality requires that $\abs{X - \mu}$ is constant since $x^2$ is not linear on any interval, and this random variable can only be constant if $X$ is constant.
</div>

Both applications of Jensen's inequality in this argument were a little cheap; the first one is really just the triangle inequality, and the second one is the Cauchy-Schwarz inequality.
But expressing the argument in this way buys a lot of generality for free; for instance if $x_1, \ldots, x_n$ are points in $\R^d$ instead of $\R$ then one can define the _spatial median_ to be any point which minimizes the absolute deviation function $t \mapsto \E(\abs{X - t})$, and the argument above gives an inequality between the mean, spatial median, and standard deviation almost verbatim.
The argument also has generalizations to weighted and continuous probability distributions, all of which come for free without having to fuss about what the standard inequalities should look like.

[1]: {{ site.baseurl }}{% post_url math/2017-03-11-averages %} "Averages"
