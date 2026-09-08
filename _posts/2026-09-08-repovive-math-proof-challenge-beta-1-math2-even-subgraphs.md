---
title: "Repovive Math Proof Challenge (Beta) 1 — MATH2: Even Subgraphs"
date: 2026-09-08 00:11:00 +0900
categories: [Competitive Programming, Repovive]
tags: [repovive, mathematics, proofs]
math: true
---

This post gives a complete proof of MATH2, **Even Subgraphs**, from Repovive Math Proof Challenge (Beta) 1. The argument is developed both algebraically through the incidence matrix over $\mathbb F_2$ and combinatorially through a fundamental-cycle basis.

## Complete Proof

Let $G$ be a finite connected undirected graph with $n$ vertices and $m$ edges. We seek the number of edge subsets $S\subseteq E(G)$ for which every vertex has even degree in the spanning subgraph $(V(G),S)$.

There are $2^m$ edge subsets before the parity conditions are imposed. Each vertex appears to contribute one binary equation, but these $n$ equations are not independent: adding all degrees counts each chosen edge twice. The key is to show that connectedness leaves exactly this one dependence. The resulting space of solutions will have $m-(n-1)$ independent binary choices.

Work over the field $\mathbb F_2=\{0,1\}$, in which addition and multiplication are taken modulo $2$. An edge subset $S$ is represented by its indicator vector $x_S\in\mathbb F_2^m$, where

$$
(x_S)_e=
\begin{cases}
1,&e\in S,\\
0,&e\notin S.
\end{cases}
$$

Let $B$ be the $n\times m$ incidence matrix over $\mathbb F_2$. For an edge $e=uv$, its column is $\mathbf e_u+\mathbf e_v$, where $\mathbf e_u$ is the coordinate vector corresponding to vertex $u$. If loops are allowed, a loop contributes the zero column, consistently with its contribution of two to the degree of its vertex.

For each vertex $v$, the $v$-th coordinate of $Bx_S$ is its degree in $(V(G),S)$ reduced modulo $2$. Consequently,

$$
S\text{ is admissible}
\quad\Longleftrightarrow\quad
Bx_S=0.
$$

The problem has therefore become a count of the vectors in $\ker B$.

To determine the rank of $B$, consider its transpose. For $y=(y_v)_{v\in V(G)}\in\mathbb F_2^n$, the coordinate of $B^{\mathsf T}y$ corresponding to an edge $uv$ is

$$
(B^{\mathsf T}y)_{uv}=y_u+y_v.
$$

Thus

$$
B^{\mathsf T}y=0
\quad\Longleftrightarrow\quad
y_u=y_v\text{ for every edge }uv.
$$

Equality propagates along paths. Since $G$ is connected, any two vertices are joined by a path, so all coordinates of $y$ must be equal. Conversely, every constant vector satisfies these equations. Hence

$$
\ker B^{\mathsf T}
=\{0,\mathbf1\}
=\operatorname{span}_{\mathbb F_2}\{\mathbf1\},
$$

and therefore $\dim\ker B^{\mathsf T}=1$. Rank–nullity, together with equality of the ranks of a matrix and its transpose, now gives

$$
\operatorname{rank}B
=\operatorname{rank}B^{\mathsf T}
=n-\dim\ker B^{\mathsf T}
=n-1.
$$

Applying rank–nullity to $B:\mathbb F_2^m\to\mathbb F_2^n$ yields

$$
\dim\ker B
=m-\operatorname{rank}B
=m-n+1.
$$

If a vector space over $\mathbb F_2$ has a basis of size $d$, every vector has a unique expression as a linear combination of those basis vectors, and each coefficient has two possible values. Such a space therefore contains $2^d$ vectors. Since indicator vectors and edge subsets correspond bijectively, the required number is

$$
\boxed{2^{m-n+1}}.
$$

The exponent also has a concrete graph-theoretic interpretation. Choose a spanning tree $T$. Its $n-1$ edges leave exactly $m-n+1$ edges outside the tree. Each edge $e\notin E(T)$, together with the unique tree path between its endpoints, determines a cycle $C_e$. The indicator vectors of these cycles lie in $\ker B$.

They are linearly independent: the non-tree edge $e$ belongs to $C_e$ and to no other fundamental cycle $C_f$. Looking at the coordinate indexed by $e$ therefore forces the coefficient of $C_e$ to be zero in any vanishing linear combination. There are $m-n+1$ such independent vectors, so they form a basis of $\ker B$. Adding their indicator vectors over $\mathbb F_2$ corresponds to taking symmetric differences of their edge sets. Thus every admissible edge subset is obtained uniquely by choosing which fundamental cycles to combine. $\square$

