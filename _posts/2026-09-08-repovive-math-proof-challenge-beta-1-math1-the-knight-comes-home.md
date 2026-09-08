---
title: "Repovive Math Proof Challenge (Beta) 1 — MATH1: The Knight Comes Home"
date: 2026-09-08 00:10:00 +0900
categories: [Competitive Programming, Repovive]
tags: [repovive, mathematics, proofs]
math: true
---

This post gives a complete proof of MATH1, **The Knight Comes Home**, from Repovive Math Proof Challenge (Beta) 1. The central invariant is the parity of the coordinate sum, and every step from the geometric move rule to the bipartite-graph interpretation is made explicit.

## Complete Proof

Consider a knight on a standard $8\times 8$ chessboard. We want to show that whenever a sequence of legal moves returns the knight to its starting square, the number of moves is even.

The geometry of an individual move suggests the right quantity to examine. A knight changes one coordinate by an even number and the other by an odd number, so the parity of the sum of its coordinates changes at every step. This is exactly what the usual black-and-white coloring of a chessboard records.

Label a square by $(x,y)\in\{1,\ldots,8\}^2$, and define its color by

$$
c(x,y)=(x+y)\bmod 2\in\{0,1\}.
$$

If a legal move takes $(x,y)$ to $(x',y')$, then

$$
(x'-x,y'-y)\in
\{(\pm2,\pm1),(\pm1,\pm2)\}.
$$

In either case, the sum of the coordinate changes is odd. Negative signs do not affect this conclusion, because $-1\equiv1\pmod{2}$ and $-2\equiv0\pmod{2}$. Thus

$$
\begin{aligned}
c(x',y')-c(x,y)
&\equiv (x'-x)+(y'-y)\\
&\equiv 1\pmod{2}.
\end{aligned}
$$

Now let $v_0,v_1,\ldots,v_r$ be the successive squares visited by the knight. Applying the preceding congruence to each of the $r$ moves and adding gives

$$
\begin{aligned}
c(v_r)-c(v_0)
&=\sum_{i=0}^{r-1}\bigl(c(v_{i+1})-c(v_i)\bigr)\\
&\equiv \sum_{i=0}^{r-1}1\\
&\equiv r\pmod{2}.
\end{aligned}
$$

If the knight returns to its starting square, then $v_r=v_0$, and the left-hand side is zero. Therefore

$$
r\equiv0\pmod{2},
$$

which proves the assertion. Revisiting other squares along the way causes no difficulty: the argument applies to every move separately and uses only the equality of the initial and final squares.

In graph-theoretic language, the graph whose vertices are chessboard squares and whose edges are legal knight moves is bipartite under this coloring. A walk alternates between its two vertex classes, so a closed walk necessarily has even length. $\square$

