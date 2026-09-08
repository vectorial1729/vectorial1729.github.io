---
title: "Repovive Math Proof Challenge (Beta) 1 — MATH5: Dense Triangle-Free Graph"
date: 2026-09-08 00:14:00 +0900
categories: [Competitive Programming, Repovive]
tags: [repovive, mathematics, proofs]
math: true
---

This post gives a complete proof of MATH5, **Dense Triangle-Free Graph**, from Repovive Math Proof Challenge (Beta) 1. The proof derives bipartiteness from the shortest odd cycle and then constructs the sharp balanced blow-up of a five-cycle.

## Complete Proof

Let $G$ be a finite simple undirected graph on $n$ vertices. Assume that it is triangle-free and that every vertex has degree greater than $2n/5$. We will show that $G$ must be bipartite and then construct examples demonstrating why the inequality must be strict.

A graph is bipartite if and only if it contains no odd cycle. One direction follows because a cycle in a bipartite graph alternates between the two vertex classes. For the other, root a spanning tree in each connected component and color every vertex by the parity of its depth. If an edge joined vertices of equal depth parity, the tree path between its endpoints would have even length; that path together with the edge would form an odd cycle. Thus a graph with no odd cycle admits the required coloring.

Suppose, toward a contradiction, that $G$ is not bipartite. Choose an odd cycle $C$ of minimum length, and set

$$
\ell=\left\lvert V(C)\right\rvert.
$$

Since triangles are excluded,

$$
\ell\ge5.
$$

The minimality of $C$ substantially restricts how other edges can meet it. First, $C$ has no chord. If a chord joined two nonconsecutive vertices, it would split the original cycle into paths of lengths $p$ and $\ell-p$, where

$$
2\le p\le\ell-2.
$$

Adding the chord to the two paths would produce cycles of lengths $p+1$ and $\ell-p+1$. Their sum is $\ell+2$, which is odd, so exactly one of these two lengths is odd. Both are smaller than $\ell$, contradicting the choice of $C$.

We next show that a vertex outside $C$ has at most two neighbors on $C$. Suppose that $x\notin V(C)$ has $r\ge3$ such neighbors. List them in cyclic order and let $g_1,\ldots,g_r$ be the lengths, measured in edges, of the arcs between successive listed neighbors. These arcs partition the cycle, so

$$
g_1+\cdots+g_r=\ell.
$$

Each $g_i$ is at least two. An arc of length one would mean that two adjacent vertices of $C$ are both adjacent to $x$, forming a triangle. Therefore

$$
g_i\ge2
\qquad(1\le i\le r).
$$

Because their sum is odd, at least one gap, say $g_i$, has odd length. Since there are at least two other gaps,

$$
\sum_{j\ne i}g_j\ge2(r-1)\ge4,
$$

and hence

$$
g_i
=\ell-\sum_{j\ne i}g_j
\le\ell-4.
$$

Take the corresponding arc and add the two edges from its endpoints to $x$. Since $x$ is outside $C$, this produces a simple cycle. Its length is $g_i+2$, which is odd, and

$$
g_i+2\le\ell-2<\ell.
$$

This is another contradiction to the minimality of $C$. Thus every vertex outside $C$ has at most two neighbors on $C$.

Vertices on $C$ have exactly two neighbors on $C$, because the cycle has no chord. Combining the two observations, we obtain the uniform bound

$$
\left\lvert N(x)\cap V(C)\right\rvert\le2
\qquad\text{for every }x\in V(G).
$$

Now count the ordered adjacent pairs $(v,x)$ with $v\in V(C)$ and $x\in V(G)$ in two ways. Counting first by $v$ gives the sum of the degrees of the cycle vertices. Counting first by $x$ gives the number of its neighbors on the cycle. Thus

$$
\sum_{v\in V(C)}\deg(v)
=
\sum_{x\in V(G)}\left\lvert N(x)\cap V(C)\right\rvert
\le2n.
$$

Let $\delta(G)$ denote the minimum degree. The cycle has $\ell$ vertices, each of degree at least $\delta(G)$, so

$$
\ell\,\delta(G)
\le
\sum_{v\in V(C)}\deg(v)
\le2n.
$$

It follows that

$$
\delta(G)
\le\frac{2n}{\ell}
\le\frac{2n}{5},
$$

contrary to the hypothesis $\delta(G)>2n/5$. Therefore $G$ is bipartite.

The same argument indicates where equality can occur: the shortest odd cycle should have length five, and every vertex should have exactly two neighbors on that cycle. This leads to a construction obtained by replacing the five vertices of a cycle with equally sized independent sets.

Fix a positive integer $t$, and take five disjoint sets

$$
V_0,V_1,V_2,V_3,V_4,
\qquad \left\lvert V_i\right\rvert=t.
$$

With subscripts interpreted modulo $5$, join every vertex in $V_i$ to every vertex in $V_{i+1}$, and include no other edges. The resulting graph has

$$
\left\lvert V(G)\right\rvert=\sum_{i=0}^{4}\left\lvert V_i\right\rvert=5t.
$$

For a vertex $v\in V_i$, its neighbors are exactly the vertices in $V_{i-1}$ and $V_{i+1}$. These two sets are disjoint, so

$$
\deg(v)=\left\lvert V_{i-1}\right\rvert+\left\lvert V_{i+1}\right\rvert=2t.
$$

In particular, the graph is regular and $\delta(G)=2t$.

It is triangle-free. A triangle cannot contain two vertices from the same class, since each class is independent. A triangle using three different classes would require those classes to be pairwise adjacent in the five-cycle describing the construction, which is impossible.

On the other hand, choose one vertex $v_i\in V_i$ for each $i$. Then

$$
v_0v_1v_2v_3v_4v_0
$$

is a cycle of length five. The graph is therefore not bipartite.

For every positive integer $t$, this constructs a triangle-free, non-bipartite graph satisfying

$$
\boxed{\left\lvert V(G)\right\rvert=5t,\qquad \delta(G)=2t=\frac{2\left\lvert V(G)\right\rvert}{5}}.
$$

Thus replacing the hypothesis $\delta(G)>2n/5$ by $\delta(G)\ge2n/5$ would make the conclusion false. The bound is optimal. $\square$

