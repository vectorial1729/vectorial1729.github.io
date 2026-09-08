---
title: "Repovive Math Proof Challenge (Beta) 1 — MATH3: Count the Good Cyclic Shifts"
date: 2026-09-08 00:12:00 +0900
categories: [Competitive Programming, Repovive]
tags: [repovive, mathematics, proofs]
math: true
---

This post gives a complete proof of MATH3, **Count the Good Cyclic Shifts**, from Repovive Math Proof Challenge (Beta) 1. We characterize every valid starting index using partial sums and the last occurrences of $k$ consecutive integer levels.

## Complete Proof

Let $a_1,\ldots,a_n$ be integers satisfying $a_i\le1$ and

$$
a_1+\cdots+a_n=k>0.
$$

A starting index $t\in\{0,\ldots,n-1\}$ is good if every nonempty prefix of

$$
a_{t+1},a_{t+2},\ldots,a_n,a_1,\ldots,a_t
$$

has strictly positive sum. We will identify all good indices explicitly and show that there are exactly $k$.

Define the partial sums

$$
s_0=0,
\qquad
s_j=\sum_{i=1}^{j}a_i
\quad(1\le j\le n).
$$

Then $s_n=k$ and $s_j-s_{j-1}=a_j\le1$. The walk traced by the partial sums can fall by an arbitrary integer amount, but it can rise by at most one unit at a time. In particular, whenever it rises from below an integer level to above that level, it must visit the level itself. This consequence of the hypotheses will determine the good indices.

For a fixed $t$, let $P_t(r)$ denote the sum of the first $r$ terms of the cyclic shift, where $1\le r\le n$. Directly from the definition,

$$
P_t(r)=
\begin{cases}
s_{t+r}-s_t,
&1\le r\le n-t,\\
k+s_{r-(n-t)}-s_t,
&n-t<r\le n.
\end{cases}
$$

The first expression accounts for prefixes contained in the original suffix. The second includes the entire suffix, of sum $k-s_t$, followed by a prefix of the original sequence. Consequently, $t$ is good if and only if

$$
\begin{aligned}
s_j&>s_t &&(t<j\le n),\\
k+s_j&>s_t &&(0\le j\le t).
\end{aligned}
$$

Including $j=0$ in the second line merely repeats the condition for the full original suffix.

Now put

$$
\mu=\min_{0\le j<n}s_j.
$$

Since $s_0=0$, we have $\mu\le0$. The relevant levels are

$$
\mu,\mu+1,\ldots,\mu+k-1.
$$

For every such integer $h$, define its last occurrence before index $n$ by

$$
t_h=\max\{j\in\{0,\ldots,n-1\}:s_j=h\}.
$$

We first justify that these indices exist. Choose $q$ such that $s_q=\mu$. Because

$$
\mu\le h\le\mu+k-1\le k-1<k=s_n,
$$

the walk from $s_q$ to $s_n$ starts at or below $h$ and finishes above it. When $h>\mu$, let $j$ be its first index after $q$ with $s_j\ge h$. Integrality gives $s_{j-1}\le h-1$, and the bound on upward increments gives

$$
h\le s_j=s_{j-1}+a_j\le(h-1)+1=h.
$$

Thus $s_j=h$. Moreover, $j<n$ because $s_n=k>h$. When $h=\mu$, the index $q$ itself already supplies an occurrence. Hence $t_h$ is well defined for all $k$ levels.

Fix one such $h$, and let $t=t_h$. We claim that every later partial sum is strictly larger than $h$. A later equality would contradict the definition of the last occurrence. If a later partial sum were smaller than $h$, the walk would subsequently have to reach $s_n=k>h$, forcing another visit to $h$ because upward increments are at most one. That would also contradict the definition of $t_h$. Therefore

$$
s_j>h=s_t
\qquad(t<j\le n).
$$

For the prefixes that wrap around, the global minimum supplies the required estimate:

$$
k+s_j-s_t
=k+s_j-h
\ge k+\mu-h
\ge1
\qquad(0\le j\le t).
$$

Both families of inequalities hold, so $t_h$ is good.

It remains to prove that no other starting index is good. Suppose that $t$ is good, and write $h=s_t$. Choose $q\in\{0,\ldots,n-1\}$ with $s_q=\mu$. If $q>t$, the first family of inequalities would give

$$
\mu=s_q>s_t\ge\mu,
$$

which is impossible. Thus $q\le t$, and the second family gives

$$
k+\mu-h>0.
$$

Since $h$ and $\mu$ are integers, we conclude that

$$
\mu\le h\le\mu+k-1.
$$

The first family also implies that no index after $t$ has partial sum $h$. Therefore $t$ is exactly the last occurrence of this level: $t=t_h$.

We have established the exact correspondence

$$
\{t\in\{0,\ldots,n-1\}:t\text{ is good}\}
=
\{t_h:h=\mu,\mu+1,\ldots,\mu+k-1\}.
$$

Distinct levels give distinct indices, since a single partial sum cannot equal two different integers. The set on the right consequently has exactly $k$ elements.

For instance, take $(a_1,a_2,a_3,a_4)=(1,-1,1,1)$. Its partial sums are $(0,1,0,1,2)$, so $\mu=0$ and $k=2$. The last occurrences of levels $0$ and $1$ before index $4$ are $t_0=2$ and $t_1=3$. The corresponding shifts are $(1,1,1,-1)$ and $(1,1,-1,1)$, whose prefix sums are respectively $(1,2,3,2)$ and $(1,2,1,2)$. Both are strictly positive throughout.

The correspondence counts starting indices themselves. It therefore proves the stated result even when periodicity causes different starting indices to produce identical cyclic sequences. $\square$

