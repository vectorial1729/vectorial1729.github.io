---
title: Repovive Math Proof Challenge (Beta) 1 Solution
date: 2026-09-08 00:10:00 +0900
categories: [Competitive Programming, Repovive]
tags: [repovive, mathematics, proofs]
math: true
---

This post presents complete proofs for MATH1 through MATH5 of Repovive Math Proof Challenge (Beta) 1. The five arguments use parity, linear algebra over $\mathbb F_2$, cyclic partial sums, connectivity of constrained subset families, and extremal graph theory.

## MATH1. The Knight Comes Home

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

## MATH2. Even Subgraphs

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

## MATH3. Count the Good Cyclic Shifts

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

## MATH4. Reverse a Balance

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

## MATH5. Dense Triangle-Free Graph

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
