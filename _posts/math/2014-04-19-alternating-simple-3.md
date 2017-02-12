---
layout: post
title: The Alternating Group is Simple III
date: 2014-04-19
---

I will now conclude my series of posts about the alternating group by proving that $A_n$ is simple for $n \geq 5$.  Just as with $A_5$ I stole this argument from Dummit & Foote; while I feel I might have been able to come up with the argument for $A_5$ on my own, the argument for $A_n$ is a bit too clever for me.  If anyone knows who discovered it, please let me know.

We are going to use again and again the formula for conjugating permutations from my last post, so I will repeat it here for reference:

<div class="lemma">
Let $\sigma = (a_1 a_2 \ldots a_k)$ be a cycle and let $\tau$ be any permutation. Then $\tau \sigma \tau^{-1} = (\tau(a_1) \tau(a_2) \ldots \tau(a_k))$
</div>

Let us jump right into the proof of the main result:

<div class="theorem">
$A_n$ is simple for every $n \geq 5$.
</div>
<div class="proof">
We use induction on $n$.  The base case, $n = 5$, was handled in the last post.  So assume that $A_{n-1}$ is simple, and let $H$ be a proper normal subgroup of $A_n$, $n \geq 6$.  Our aim is to show that $H$ is the trivial group.

Our first step is to prove that no non-identity element of $H$ can fix any symbol.  Let $G_i$ denote the subgroup of $A_n$ consisting of all elements that fix the symbol $i$; by Lemma 1 we have $\tau G_i \tau^{-1} = G_{\tau(i)}$ for any permutation $\tau$.  Note that $G_i \cong A_{n-1}$ for each $i$, so if $H$ intersects some $G_i$ nontrivially then $G_i \subseteq H$ by the induction hypothesis.  Moreover, since any $G_j$ can be obtained from $G_i$ by conjugation and $H$ is normal, we have that $G_j \subseteq H$ for all $H$.

Now, any element of $A_n$ can be written as the product of pairs of transpositions.  A pair of transpositions can only permute up to four symbols, so since $n \geq 5$ every pair of transpositions fixes at least one symbol and hence is in some $G_i$.  Thus every element of $A_n$ can be written as a product of permutations each of which is in some $G_i$; since $G_i \subseteq H$, it follows that $A_n \subseteq H$, contradicting our assumption that $H$ is a proper subgroup.

So no non-identity element of $H$ can fix any symbol.  Consequently, if two elements of $H$ agree on even one symbol then they must be the same, for if $\tau_1(i) = \tau_2(i)$ then $\tau_1 \tau_2^{-1}$ fixes $i$ and hence is the identity.  To complete the proof we will use this observation to show that the identity is the only element of $H$.

* No element of $H$ can contain a $k$-cycle for $k \geq 3$:
Suppose $\sigma \in H$ contains a $k$-cycle $(a_1 a_2 a_3 \ldots)$.  Since $n \geq 5$ it is possible to choose $\tau$ which fixes $a_1$ and $a_2$ but not $a_3$.  By Lemma 1 we have:

$$\tau (a_1 a_2 a_3 \ldots) \tau^{-1} = (a_1 a_2 \tau(a_3) \ldots)$$

Thus $\sigma$ and $\tau \sigma \tau^{-1}$ are two permutations in $H$ which agree on $a_1$ but not on $a_2$; this is a contradiction.

* No element of $H$ can be the product of disjoint $2$-cycles:
Suppose such an element $\sigma$ were to exist.  Since $n \geq 6$ and $\sigma$ can't fix any symbols, it must be the product of at least three disjoint $2$-cycles:
$\sigma = (a_1 a_2)(a_3 a_4)(a_5 a_6)\ldots$
Let $\tau = (a_1 a_2)(a_3 a_5)$.  We have:
$\tau \sigma \tau^{-1} = (a_1 a_2)(a_5 a_4)(a_3 a_6)\ldots$
This time $\sigma$ and $\tau \sigma \tau^{-1}$ agree on $a_1$ and $a_2$ but not on $a_3$, a contradiction.

We conclude that no element of $H$ can have a cycle of length larger than $1$; this means that $H$ is the trivial group.
</div>
