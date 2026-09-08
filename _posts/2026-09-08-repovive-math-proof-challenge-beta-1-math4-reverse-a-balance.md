---
title: "Repovive Math Proof Challenge (Beta) 1 — MATH4: Reverse a Balance"
date: 2026-09-08 00:13:00 +0900
categories: [Competitive Programming, Repovive]
tags: [repovive, mathematics, proofs]
math: true
---

This post gives a complete proof of MATH4, **Reverse a Balance**, from Repovive Math Proof Challenge (Beta) 1. It proves the lower bound forced by the largest weight and the matching upper bound through connectivity of a constrained subset-sum graph.

## Complete Proof

The weights $1,2,\ldots,4n$ initially occupy two pans of equal total mass. We may transfer one weight at a time, possibly moving the same weight more than once. The objective is to reverse the starting side of every weight while controlling the difference between the masses on the two pans after every transfer.

The optimal threshold is

$$
\boxed{T=4n}.
$$

The largest weight gives the lower bound. The more substantial part is proving that this bound always suffices, regardless of the initial balanced arrangement. We will do so by showing that all configurations satisfying the threshold belong to a single connected graph of legal transfers.

First translate the physical arrangement into a subset. The total mass of all weights is

$$
\sum_{j=1}^{4n}j
=\frac{4n(4n+1)}2
=2n(4n+1).
$$

Let

$$
H=n(4n+1)
$$

be half of this total. Represent a configuration by the subset $A\subseteq\{1,\ldots,4n\}$ of weights on the left pan, and write

$$
\sigma(A)=\sum_{a\in A}a.
$$

The right pan then has mass $2H-\sigma(A)$, so the signed difference between the pan masses is

$$
D(A)=\sigma(A)-\bigl(2H-\sigma(A)\bigr)
=2\bigl(\sigma(A)-H\bigr).
$$

This factor of two matters: transferring a weight of mass $w$ decreases one pan's mass by $w$ and increases the other pan's mass by $w$. The signed difference therefore changes by $2w$ in absolute value.

Every successful reversal must transfer the weight of mass $4n$ at least once. If $D$ and $D'$ are the signed differences immediately before and after one such transfer, then

$$
\left\lvert D'-D\right\rvert=8n.
$$

Both configurations must satisfy the threshold. The triangle inequality consequently gives

$$
8n=\left\lvert D'-D\right\rvert
\le \left\lvert D'\right\rvert+\left\lvert D\right\rvert
\le2T.
$$

Therefore every possible threshold must satisfy $T\ge4n$. This argument holds even when weights are transferred repeatedly.

For the reverse inequality, observe that the proposed threshold is equivalent to a condition on the left-pan sum:

$$
\begin{aligned}
\left\lvert D(A)\right\rvert\le4n
&\iff 2\left\lvert\sigma(A)-H\right\rvert\le4n\\
&\iff \left\lvert\sigma(A)-H\right\rvert\le2n\\
&\iff H-2n\le\sigma(A)\le H+2n.
\end{aligned}
$$

The allowed interval of subset sums has width $4n$, exactly the value of the largest weight. This suggests the following general statement.

For an integer $r\ge0$ and integers $L,U$, define

$$
\mathcal F_r(L,U)=
\left\{
A\subseteq\{1,\ldots,r\}:
L\le\sum_{a\in A}a\le U
\right\}.
$$

Make this family into a graph by joining $A$ and $B$ whenever their symmetric difference contains one element:

$$
\left\lvert A\mathbin{\triangle}B\right\rvert=1.
$$

We claim that if $U-L\ge r$, then this graph is connected whenever it is nonempty. Each edge changes the membership of exactly one weight, so this is the connectivity property required by the balance problem.

Before proving the claim, we need the elementary fact that the subset sums of consecutive weights fill an entire integer interval. More precisely, for every $j\ge0$,

$$
\left\{
\sum_{a\in A}a:
A\subseteq\{1,\ldots,j\}
\right\}
=
\left\{0,1,\ldots,\frac{j(j+1)}2\right\}.
$$

For $j=0$, only the empty subset occurs and its sum is zero. Suppose the statement is known for $j-1$, and put $Q=j(j-1)/2$. Subsets omitting $j$ realize every integer in $[0,Q]$, while subsets containing $j$ realize every integer in $[j,Q+j]$. These intervals leave no integer gap, because

$$
Q+1-j
=\frac{j(j-1)}2+1-j
=\frac{(j-1)(j-2)}2
\ge0
\qquad(j\ge1).
$$

Their union therefore contains every integer from $0$ to $Q+j=j(j+1)/2$. No subset sum can fall outside this range, completing the induction.

Return now to the connectivity claim, and induct on $r$. For $r=0$, a nonempty admissible family consists only of the empty subset, so its graph is connected.

For $r\ge1$, split the admissible subsets into those omitting $r$ and those containing $r$. The first part is exactly $\mathcal F_{r-1}(L,U)$. Removing $r$ from every subset in the second part identifies that part with $\mathcal F_{r-1}(L-r,U-r)$. Both intervals still have width $U-L\ge r\ge r-1$, so each nonempty part is connected by the induction hypothesis.

If only one part is nonempty, this already proves the claim. Suppose both are nonempty. We need one edge joining the two parts.

Write

$$
R=\frac{r(r-1)}2
$$

for the sum of all weights smaller than $r$. A subset omitting $r$ has sum at most $R$, so nonemptiness of the first part implies $L\le R$. A subset containing $r$ has sum at least $r$, so nonemptiness of the second part implies $U\ge r$. The width assumption also gives $L\le U-r$. Taken together,

$$
L\le R,\qquad 0\le R,\qquad
L\le U-r,\qquad 0\le U-r.
$$

Consequently,

$$
\max\{L,0\}\le\min\{R,U-r\}.
$$

Choose an integer $s$ in this nonempty interval. Since $0\le s\le R$, the subset-sum fact supplies a subset $B\subseteq\{1,\ldots,r-1\}$ with sum $s$. The other inequalities give

$$
L\le s\le s+r\le U.
$$

Thus both $B$ and $B\cup\{r\}$ are admissible. They differ in the membership of the single weight $r$, so they are adjacent. This edge joins the two connected parts and proves that their union is connected. The induction is complete.

Apply the claim with

$$
r=4n,\qquad
L=H-2n,\qquad
U=H+2n.
$$

Its width condition holds with equality:

$$
U-L=(H+2n)-(H-2n)=4n=r.
$$

Let $A_0$ be the subset on the left pan initially. Since the starting arrangement is balanced,

$$
\sigma(A_0)=H.
$$

The desired final subset is its complement $A_0^c=\{1,\ldots,4n\}\setminus A_0$, which also has sum

$$
\sigma(A_0^c)=2H-\sigma(A_0)=H.
$$

Both endpoints therefore belong to $\mathcal F_{4n}(H-2n,H+2n)$. Connectivity gives a finite path between them. Each edge of that path transfers a single weight to the opposite pan, and every vertex on the path satisfies $\left\lvert D(A)\right\rvert\le4n$. At the final vertex, every weight has switched sides because membership in $A_0$ has been complemented.

For a small example, when $n=1$ the balanced arrangement with left pan $\{1,4\}$ can be reversed through

$$
\{1,4\}
\longrightarrow
\{4\}
\longrightarrow
\{3,4\}
\longrightarrow
\{3\}
\longrightarrow
\{2,3\}.
$$

The left-pan sums are $5,4,7,3,5$, while the total mass is $10$. The corresponding signed differences are $0,-2,4,-4,0$. Moving the weight $4$ changes the difference from $4$ to $-4$, attaining the lower bound at both ends of that move.

The general connectivity argument proves sufficiency for every initial balanced arrangement, while the largest-weight argument proves necessity. Hence the smallest positive integer threshold is $T=4n$. $\square$

