---
layout: post
permalink: /ds/compute-quantiles
title: The subtleties of computing quantiles
abstract: Computing quantiles is a good way to summarize the distribution of a numerical dataset. But confusingly there are nearly a dozen different definitions of quantile that can all claim to be correct, and in all cases it is difficult to actually compute quantiles for very large datasets. I will explain why there are so many definitions, and compare a couple of different strategies for doing computations at scale.
date: 2020-06-21
categories: data-science
tags: live
comments: true
---

Quantiles are summary statistics, i.e. numbers that we calculate from a dataset to describe its distribution.
The median is perhaps the standard example: it is intended to be the "midpoint" of the dataset, so that half of the values are larger than the median and half are smaller.
Percentiles are also commonly used, and they are really just quantiles with different units; the 75th percentile just means the 0.75th quantile, and both refer to a value which is larger than 75% of the data and smaller than 25%, give or take.

This idea is simple enough, but actually nailing it down properly is surprisingly confusing - [Wikipedia][2] gives nine distinct definitions implemented by standard software packages!
And these implementations have scale limitations - they are not suitable for datasets with billions of values that have to be manipulated with distributed computing engines.
In this post I will thoroughly review the definition(s) of quantile so that we can make sense of all the different implementations, and then I will describe some alternative implementations that work well with distributed computing.

## The definition of quantile

A dataset for our present purposes is really just a finite sequence of numbers $S = x_1, \ldots, x_N$.
When we say "dataset" rather than "finite sequence", it usually means implicitly that the numbers are sampled from some real-world process which we wish to represent using a statistical model.
Let us assume that the statistical model is a continuous random variable $X$ and that the $x_i$'s are independent observations drawn from $X$

To describe the structure of a random variable $X$ we often look at its _cumulative distribution function_ (CDF) which is by definition:

$$\P_X(x) = \P(X \leq x)$$

The CDF of $X$ completely determines $X$ - we can express the probability of any event involving $X$ in terms of $\P_X$.
The concept of a quantile exists to answer the following question:

> Given a dataset $S$ sampled from a continuous random variable $X$, how can we use $S$ to approximate the cumulative distribution function of $X$?

In fact we really want to approximate the _inverse_ $\P_X^{-1}$ of the CDF; for instance $\P_X^{-1}(0.5)$ is the median of $X$.

### The empirical distribution function

To construct an approximation we define a new random variable $X_S$ on the set $\br{1,\ldots,N}$ (equipped with the uniform probability measure) by the formula $X_S(i) = x_i$, where $S = x_1, \ldots, x_N$ is written in nondecreasing order.
$X_S$ has its own cumulative distribution function, which by abuse of notation we will denote by $\P_S$:

$$\P_S(x) = \P(X_S \leq x)$$

Each number $1,\ldots,N$ has probability mass $\frac{1}{N}$, so we can compute this function by counting the number of $x_i$'s smaller than or equal to $x$ and dividing by $N$:

$$\P_S(x) = \frac{1}{N} \abs{\br{i \colon x_i \leq x, 1 \leq i \leq N}}$$

We call this the _empirical distribution function_ of the sample $S$, and this is the function that we use to approximate $\P_X$: according to the [Glivenko-Cantelli theorem][1], $\P_S$ almost surely converges to $\P_X$ as $\abs{S} \to \infty$.

$\P_S$ is not injective: it is constant on each half open interval $[x_i, x_{i+1})$, and of course the dataset $S$ can contain repeated values.
But if we restrict the domain of $\P_S$ to the set of distinct values in $S$ then it becomes injective and hence we can define an inverse $\P_S^{-1}$ called the _quantile function_ of $S$.
It is a priori defined on the range of $\P_S$, a subset of the set $\br{\frac{i}{N} \colon 1 \leq i \leq N}$ (depending on how many repeated values there are in $S$).

For real numbers $p$ in the range of $\P_S$, we can unambiguously define the _$p$th quantile of $S$_ to be the number $\P_S^{-1}(p)$.
This number is characterized by the fact that it is greater than or equal to a randomly chosen element of $S$ with probability $p$.

<div class="example">
Let us compute the empirical distribution function and quantile function for the dataset $S = 5,5,6,7$.
We have:

$$
    P_S(x) = \begin{cases}
        0 & x < 5 \\
        \frac{1}{2} & 5 \leq x < 6 \\
        \frac{3}{4} & 6 \leq x < 7 \\
        1 & x \geq 7
    \end{cases}
$$

The restriction of $P_S(x)$ to the set $\br{5,6,7}$ is injective, and so its inverse (which is the quantile function for $S$) is:

$$
    P_S^{-1}(p) = \begin{cases}
        5 & p = \frac{1}{2} \\
        6 & p = \frac{3}{4} \\
        7 & p = 1
    \end{cases}
$$

Thus the $0.5$th quantile is $5$, the $0.75$th quantile is $6$, and the $1.0$th quantile is $7$.
</div>

### Problems with the definition

Unfortunately the definition of $p$th quantile is a bit more complicated than this.

The first issue involves disagreement over the empirical distribution function; instead of considering the sets characterized by the inequality $x_i \leq x$, we could consider the strict inequality $x_i < x$.
This leads to an alternative function, which we might call the _strict empirical distribution function_ (though I'm not sure it has an official name):

$$\P_S^<(x) = \frac{1}{N-1} \abs{\br{i \colon x_i < x, 1 \leq i \leq N}}$$

Note that we divide by $N-1$ instead of $N$ to ensure that $\P_S^<(x_N) = 1$, writing $S = x_1, \ldots x_N$ in nondecreasing order.
The Glivenko-Cantelli theorem holds for the strict empirical distribution function as well, essentially because $\P(X \leq x) = \P(X < x)$ for continuous random variables.
But the inverse of $\P_S^<$ - we might as well call it the _strict quantile function_ - is not the same as the regular quantile function and thus gives a different definition of quantile.

The second issue is that it is desireable to extend the definition of quantile to arbitrary real numbers in $[0,1]$ rather than just the range of the empirical distribution function, a finite set.
For each $i$ from $1$ to $N$, define $p_i = \P_S(x_i)$; for any $p \in [0,1]$ not in the range of $\P_S$ there is a unique index $i$ such that $p_i < p < p_{i+1}$.
An _interpolation strategy_ is an algorithm which systematically chooses a number in $[x_i, x_{i+1}]$ to be the $p$th quantile, where $x_j = \P_S^{-1}(p_j)$ as above.
Common interpolation strategies include:

- Lower: $x_i$
- Upper: $x_{i+1}$
- Midpoint: $\frac{1}{2}(x_i + x_{i+1})$
- Linear: find $t$ so that $p = (1-t)p_i + t p_{i+1}$ and return $(1-t)x_i + t x_{i+1}$

Between the choice of empirical distribution function and the choice of interpolation strategy, we have eight inequivalent definitions of quantile, and there are still others.
Be careful!

## Computing quantiles at scale

The discussion above yields a straightforward algorithm for computing quantiles.
Here is what the algorithm looks like using the standard empirical distribution function and the lower interpolation strategy:

1. Input: a finite sequence $S$ and a probability $p$.
2. Sort $S$ in nondecreasing order: $S = x_1, \ldots, x_N$.
3. Compute the numbers $p_i = \frac{1}{N} \abs{\br{j \colon: x_j \leq x_i, 1 \leq j \leq n}}$.
4. If there is at least one index $i$ such that $p = p_i$, yield the largest $i$ with this property; otherwise find the unique $i$ such that $p_i < p < p_{i+1}$ and yield $i$.
5. Output $x_i$.

This algorithm works fine, but it is not very practical for very large datasets due to the sort in the second step.
Sorts are particularly limiting because it is difficult to distribute the computation across multiple CPUs.

### An approximate solution

One standard solution to this problem is to sacrifice a little bit of precision for scalability.
For instance, Apache Spark framework implements the [Greenwald-Khanna][3] algorithm, a distributed algorithm which allows the user compute arbitrary quantiles within any desired accuracy.
The algorithm is accessible via the `approxQuantile` method:

```python
data.approxQuantile(
    col='value',
    probabilities=[0.25, 0.5, 0.75, 0.9, 1.0],
    relativeError=0.1
)
```

To use this algorithm safely it is important to understand the sense in which it approximates the true quantiles.
I will use the ordinary empirical distribution function in this section, though I'm not actually sure what Spark uses.

It is typical to refer to the index of a value in $S$ (in nondecreasing sort order) as the _rank_ of the value.
Let us call the index generated in the fourth step of the algorithm above the $p$th _lower rank_, denoted $L(p)$.
$L(p)$ is the largest integer with the property that $\frac{L(p)}{N} \leq p$; in other words:

$$
L(p) = \lfloor p N \rfloor
$$

where $\lfloor \cdot \rfloor$ is the floor function.
The $p$th lower quantile is the value $x_{L(p)}$, and evidently the $p$th upper quantile is simply $x_{U(p)}$ where $U(p) = L(p) + 1$ is by definition the $p$th _upper rank_ of $p$.
We can express the upper rank as:

$$
U(p) = \lceil p N \rceil
$$

where $\lceil \cdot \rceil$ is the ceiling function.
Any sensible notion of quantile which uses the empirical distribution function as a starting point will lie between $x_{L(p)}$ and $x_{U(p)}$.
This motivates an appropriate sense in which one can hope to approximate the quantiles of the dataset: the goal isn't to approximate the quantile itself, but rather the indices $L(p)$ and $U(p)$ which determine where in the dataset the quantile can occur.
Here is the precise statement:

<div class="theorem">
Given an input dataset $S = x_1, \ldots, x_N$, an input quantile $p$ and an input error $\eps$, the Greenwald-Khanna algorithm outputs a value $x_i$ where $i$ satisfies the inequality:

$$\lfloor (p - \eps) N \rfloor \leq i \leq \lceil (p + \eps) N \rceil$$

</div>

It isn't all that important to know how the algorithm works - the [original paper][3] is pretty readable, for those who are curious - but it is important to understand the guarantee: we won't necessarily get numerically close to the correct quantile, but we will get close to the rank.

## An exact solution for skewed data

I tried to use the Greenwald-Khanna algorithm to compute quantiles for the probabilities $\br{0, \frac{1}{1000}, \frac{2}{1000}, \ldots, 1\}$ on the dataset consisting of retweet counts for all tweets posted in a two month period - this amounted to about 10 billion values, if I recall correctly.
I found that the running time of the algorithm on a dataset of this size to this level of precision was prohibitive, and tried to find an alternative.
I was able to find a much faster algorithm - the running time was a few seconds rather than closer to a day on a reasonably sized compute cluster - by exploiting the structure of the data.

Specifically, the dataset contains many repeated values: the vast majority of tweets get no retweets at all, a smaller but still very large number of tweets get only one retweet, and so on.
This happens a lot when operating on datasets involving observations drawn from a discrete distribution, and it can be adapted to non-integer datasets by passing to a discrete approximation (with similar theoretical guarantees as Greenwald-Khan).

I will explain the algorithm using a combination of the PySpark and Pandas APIs; at the outset let us assume that the the following dataset is loaded into a PySpark DataFrame object called `data`:

$$
S = 0,0,0,0,0,1,1,1,2,3,3,5,9
$$


The first step of the algorithm is to count the number of times each value occurs in the dataset:

```python
value_counts = data.groupby('value').count()
value_counts.show()
```
```
+-----+-----+
|value|count|
+-----+-----+
|    0|    5|
|    9|    1|
|    5|    1|
|    1|    3|
|    3|    2|
|    2|    1|
+-----+-----+
```

Modern distributed computing engines like Spark are highly optimized for this sort of computation, and this is the only part of the computation that will be distributed across the cluster.
It is here that you will be able to tell if my alternative algorithm is appropriate for your dataset: if it had enough repetition so that `value_counts` is of a manageable size for a single machine (up to, say, 50 million rows or so) then this approach will succeed, and otherwise it will not.

So now let us collect the dataset back to the driver node and sort it by value; from here on out I'll switch to the Pandas API.

```python
sorted_counts = value_counts.toPandas().sort_values(by='value')
```
```
 value  count
     0      5
     1      3
     2      1
     3      2
     5      1
     9      1
```

The advantage of this view on the data is that it makes it very easy to compute the values at various different ranks in the data: ranks $1$ through $5$ are occupied by $0$, ranks $6$ through $8$ by $1$, rank $9$ by $2$, and so on.
We can compute the ranks at which the values change by computing the cumulative sum of the count column, and we can compute the quantiles at these critical ranks by dividing through the total number of values in the original dataset (given by the sum of the count column).
These values are precisely the values of the empirical distribution function.

```python
N = sorted_counts['count'].sum()
sorted_counts['empirical_distribution'] = sorted_counts['count'].cumsum() / N
```
```
 value  count  empirical_distribution
     0      5           0.384615
     1      3           0.615385
     2      1           0.692308
     3      2           0.846154
     5      1           0.923077
     9      1           1.000000
```

Reviewing the definitions in the previous section, this allows us to easily read off quantiles from this table.
For instance the lower quantile at $p = 0.75$ is $2$; the Pandas syntax for this is:

```python
sorted_counts[sorted_counts['empirical_distribution'] <= .75]['value'].iloc[-1]
```
```
2
```

To efficiently compute quantiles at many different probabilities simultaneously, we use the somewhat obscure _as of_ join.
This is a tool borrowed from time series analysis.
Imagine you have a database consisting of observations of some variable based on readings taken at random times during the day, and you want to know the value of the latest observation at each hour.
You can compute this using an "as of" join between the table of timestamped observations and the table of hours `01:00, 02:00, 03:00,...`.
The join is so named because its values have the interpretation "as of `02:00` the observed value was X".

This is essentially the computation that we're trying to do, only instead of joining along prescribed timestamps we want to join along prescribed probabilities.
The Pandas syntax is:

```python
probabilities = pd.DataFrame([0.25, 0.5, 0.75, 0.9, 1.0], columns=['quantile'])
quantiles = (
    pd.merge_asof(
        left=probabilities,
        right=sorted_counts,
        left_on='quantile',
        right_on='empirical_distribution',
        direction='backward'
    )
    .bfill()
)
quantiles[['quantile', 'value']]
```
```
 quantile  value
     0.25    0.0
     0.50    0.0
     0.75    2.0
     0.95    5.0
     1.00    9.0
```

This computes the lower quantiles; upper quantiles can be computed using the parameter `direction='forward'`, and most other interpolation strategies can be expressed in terms of the lower and upper quantiles.

The diligent reader may notice that this algorithm gives different results from the native Pandas implementation of the quantile algorithm.
This is because the Pandas implementation (which is really Numpy's implementation) uses the strict empirical distribution function, a choice which I find a bit bizarre.
We can replicate this choice using our more scalable implementation by excluding the current row during the cumulative sum computation (and normalizing by $N-1$ instead of $N$):

```python
N = sorted_counts['count'].sum()
sorted_counts['strict_emprical_distribution'] = (
    (sorted_counts['count'].cumsum() - sorted_counts['count']) / (N - 1)
)
```

[1]: https://en.wikipedia.org/wiki/Glivenko%E2%80%93Cantelli_theorem
[2]: https://en.wikipedia.org/wiki/Quantile
[3]: http://infolab.stanford.edu/~datar/courses/cs361a/papers/quantiles.pdf
