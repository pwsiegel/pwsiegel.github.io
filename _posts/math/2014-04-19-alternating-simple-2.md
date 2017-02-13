---
layout: post
title: The Alternating Group is Simple II
abstract: A detailed proof that $A_5$ is simple.
date: 2014-04-19
categories: math
---

In my last post I described the alternating group and its place in the world of groups. I will now prove that $A_5$ is simple, and in the third and final post of this series I will prove that $A_n$ is simple for $n \geq 5$. The plan of attack is as follows: first I will carry out some preliminary analysis of conjugacy in $S_n$ and $A_n$, and then by identifying all conjugacy classes in $A_5$ I will prove that $A_5$ is simple. I will then prove that $A_n$ is simple for $n \geq 5$ by induction. I'm not sure who invented this argument; all I know is that I learned it in Dummit & Foote.

## Conjugacy Classes in the Alternating Group
To understand the normal subgroups of a group it is very useful to first think carefully about its conjugacy classes; this is because a normal subgroup is by definition the union of conjugacy classes. Fortunately conjugation in the symmetric group is easy to understand using "cycle notation". A *$k$-cycle* in $S_n$ is a permutation which fixes all but $k$ symbols $a_1, \ldots, a_k$ which acts on these symbols as:

$$a_1 \to a_2 \to \ldots \to a_k \to a_1$$

The notation for this cycle is $(a_1 a_2 \ldots a_k)$. It is not hard to show that every permutation decomposes as the product of disjoint cycles, and the decomposition is unique up to reordering the cycles. Indeed, cycle notation makes it particularly easy to understand conjugation.

<div class="lemma">
Let $\sigma = (a_1 a_2 \ldots a_k)$ be a cycle and let $\tau$ be any permutation. Then $\tau \sigma \tau^{-1} = (\tau(a_1) \tau(a_2) \ldots \tau(a_k))$
</div>
<div class="proof">
For $i < k$ we have $\tau \sigma \tau^{-1}(\tau(a_i)) = \tau \sigma(a_i) = \tau(a_{i+1})$ and similarly $\tau \sigma \tau^{-1}(\tau(a_k)) = \tau(a_1)$.

</div>

The lemma extends easily to the case where $\sigma$ is the product of cycles, so we see that conjugation by $\tau$ preserves the cycle structure of $\sigma$ while relabelling the symbols in the cycle. In particular, two elements of $S_n$ are conjugate if and only if the number and lengths of cycles are the same. For instance, $(12)(345)$ is conjugate to $(124)(35)$ in $S_5$ but not to $(12345)$.

Note that conjugacy in $A_n$ is a little more subtle. A $k$-cycle is even if and only if $k$ is odd, but not all $k$-cycles are conjugate in $A_n$. For instance the transposition $\tau = (45)$ conjugates $(12345)$ to $(12354)$ in $S_5$, but there is no even permutation which conjugates $(12345)$ to $(12354)$ and hence they are not conjugate in $A_5$.

To prove that $A_5$ is simple, we will need to determine the sizes of all of its conjugacy classes. We will do this using the following tool:

<div class="lemma">
Let $g$ be an element of a group $G$, let $Z_G(g)$ be the centralizer of $g$ (i.e. the set of all elements of $G$ which commute with $g$) and let $C_G(g)$ denote the conjugacy class of $g$. Then

$$|Z_G(g)| \cdot |C_G(g)| = |G|$$

</div>
<div class="proof">
Let $G$ act on itself by conjugation.  The orbit of $g$ under this action is $C_G(g)$ and the stabilizer is $Z_G(g)$, so the result follows from the orbit-stabilizer theorem.

</div>

We will apply this lemma as follows.  First we will use our understanding of conjugacy in $S_n$ to identify the centralizer of a cycle.  From that it is easy to identify the centralizer of a cycle in $A_n$, and that will allow us to count the conjugates of a cycle in $A_n$.

<div class="proposition">
Let $\sigma \in S_n$ be a $k$-cycle.  Then:
$Z_{S_n}(\sigma) = \{\sigma^i \tau:\: 0 \leq i < k,\, \tau \in S_{n-k}\}$
</div>
<div class="proof">
Proof: By Lemma 1, the conjugates of $\sigma$ in $S_n$ are precisely the $k$-cycles.  To specify a $k$-cycle one must specify the symbols in the $k$-cycle and the order in which they appear; there are $\frac{n!}{k!(n-k)!}$ ways to choose $k$ symbols and $k!$ different orders in which they can appear, though $k$ of the orders define the same cyclic permutation.  Thus there are $\frac{n!}{k!(n-k)!} \cdot (k-1)! = \frac{n!}{k \cdot (n-k)!}$ conjugates of $\sigma$; by Lemma 2, $\abs{Z_{S_n}(\sigma)} = k \cdot (n-k)!$.

The permutation $\sigma^i$ clearly commutes with $\sigma$.  Any permutation $\tau$ which fixes the $k$ symbols that $\sigma$ acts on also commutes with $\sigma$, and the subgroup of all such permutations is isomorphic to $S_{n-k}$.  Thus the permutations $\sigma^i \tau$, $\tau \in S_{n-k}$, all commute with $\sigma$; there are $k \cdot (n-k)!$ distinct permutations of this form, so they make up the entire centralizer of $\sigma$.
</div>

## Simplicity of $A_5$
We are now ready to prove the main result of this post:

<div class="theorem">
$A_5$ is simple.
</div>
<div class="proof">
The only possible cycle structures of non-identity elements in $A_5$ are $(123)$, $(12345)$, and $(12)(34)$.  Recall that in $S_5$ the cycle structure completely determines the conjugacy class; in $A_5$ some of these conjugacy classes may split.  Let us analyze each conjugacy class in turn using Proposition 1.

* $(123)$: The centralizer of $(123)$ in $S_5$ consists of the six permutations $(123)^i \tau$ where $i = 0, 1, 2$ and $\tau$ is either the identity or $(45)$, so $(123)$ has $120/6 = 20$ conjugates in $S_5$.  If $\tau = (45)$ then $(123)^i \tau$ is odd, so the centralizer in $A_5$ has only three elements and hence the number of conjugates is still $60/3 = 20$.  Thus all $3$-cycles are conjugate in $A_5$.

* $(12345)$: The centralizer of $(12345)$ in both $S_5$ and $A_5$ is just the cyclic subgroup $\{(12345)^i\}$, so there are $120/5 = 24$ conjugates in $S_5$ and $60/5 = 12$ conjugates in $A_5$.  The other $12$ elements in the $S_5$ conjugacy class are accounted for by the $A_5$ conjugacy class of $(12354)$ which is disjoint from that of $(12345)$.

* $(12)(34)$: It is straightforward to check that $(12)(34)$ commutes with the identity, itself, $(13)(24)$ and $(14)(23)$.  If $\tau$ does not fix the symbol $5$ then $\tau (12)(34) \tau \neq (12)(34)$ by Lemma 1, so $(12)(34)$ does not commute with $\tau$.  A similar argument shows that $(12)(34)$ does not commute with any $3$-cycle, so the centralizer has exactly $4$ elements and hence $(12)(34)$ has $60/4 = 15$ conjugates in $A_5$.

Including the identity, we have accounted for the conjugacy classes of all $60$ elements of $A_5$: $60 = 1 + 20 + 12 + 12 + 15$.  So let $H$ be a normal subgroup of $A_5$.  Since $H$ is normal it is the union of conjugacy classes (including the identity), so $|H|$ is the sum of $1$ and some subset of $\{20, 12, 12, 15\}$.  But $|H|$ must also divide $|A_5| = 60$; checking cases the only possible choices for $|H|$ are $1$ and $60$.
</div>
