---
title: Repovive Standard Round 7 Solution
date: 2026-09-07 00:30:00 +0900
categories: [Competitive Programming, Repovive]
tags: [repovive, competitive-programming]
math: true
---

This post summarizes the solutions to problems A through F of [Repovive Standard Round 7](https://repovive.com/contests/22). The earlier problems require only short observations, but the later ones depend on identifying the precise structure: scheduling, suffix records, an exchange argument on a tree, reconstruction from two frontiers, and cut decomposition.

## A. Four Pieces, One Sheet

Suppose the horizontal side is divided into two positive lengths $x,w>0$, and the vertical side into two positive lengths $y,z>0$. With a suitable ordering, the areas of the four rectangles are

$$
xy,\qquad xz,\qquad wy,\qquad wz.
$$

Assign these four values to $A,B,C,D$ as

$$
A=xy,\qquad B=xz,\qquad C=wy,\qquad D=wz.
$$

Then

$$
AD=(xy)(wz)=xwyz,
$$

and

$$
BC=(xz)(wy)=xwyz.
$$

Therefore, the condition

$$
\boxed{AD=BC}
$$

must hold.

This condition is not only necessary but also sufficient. Suppose that, after placing the four numbers into $A,B,C,D$ in some order, we have $AD=BC$. For example, choose

$$
x=1,\qquad y=A,\qquad z=B,\qquad w=\frac{C}{A}.
$$

Then

$$
xy=A,\qquad xz=B,\qquad wy=C,
$$

and

$$
wz=\frac{C}{A}B=\frac{BC}{A}=D.
$$

Thus, an actual rectangle and two cuts can be constructed. The lengths do not need to be integers, so it is enough that they are positive.

Consequently, we only need to determine whether the four numbers can be divided into two diagonal pairs whose products are equal. There are exactly three possible pairings, so if at least one of

$$
ab=cd,
\qquad
ac=bd,
\qquad
ad=bc
$$

holds, the answer is `Yes`; otherwise, it is `No`. Each number is at most $10^9$, so each product is at most $10^{18}$ and fits in a 64-bit integer. Only three comparisons are required per test case, giving a time complexity of $O(1)$.

## B. Opposite Workflows

Let $P$ be the set of jobs that visit $A$ first, and let $Q$ be the set of jobs that visit $B$ first. Every job $i$ uses exactly $a_i$ units of time on both machines. Define the total workloads and maximum job lengths of the two sets as

$$
S_A=\sum_{i\in P}a_i,
\qquad
S_B=\sum_{i\in Q}a_i,
$$

and

$$
M_A=\max_{i\in P}a_i,
\qquad
M_B=\max_{i\in Q}a_i,
$$

where the maximum of an empty set is defined as zero.

Every machine must process every job exactly once, so the total load on each machine is

$$
S=S_A+S_B.
$$

Therefore, if the makespan is $C_{\max}$, then certainly

$$
\boxed{C_{\max}\ge S_A+S_B}.
$$

Now consider only the jobs in $P$. They form a two-machine flow shop in the order

$$
A\rightarrow B,
$$

and each job has the same processing time $a_i$ on both machines. A two-machine flow shop has an optimal permutation schedule, so we may assume that both machines process the jobs in the same order

$$
p_1,p_2,\ldots,p_m.
$$

Define the prefix sum

$$
T_j=\sum_{h=1}^{j}a_{p_h},
$$

and let $C_j$ be the time at which the $j$-th job finishes on machine $B$. Then

$$
C_j=\max(T_j,C_{j-1})+a_{p_j}.
$$

If we define

$$
M_j=\max_{1\le h\le j}a_{p_h},
$$

then induction gives

$$
\boxed{C_j=T_j+M_j}.
$$

Indeed, assuming the statement holds through $j-1$,

$$
\begin{aligned}
C_j
&=\max(T_{j-1}+a_{p_j},\,T_{j-1}+M_{j-1})+a_{p_j}\\
&=T_{j-1}+\max(a_{p_j},M_{j-1})+a_{p_j}\\
&=T_j+M_j.
\end{aligned}
$$

Thus, even processing only the jobs in $P$ requires at least

$$
S_A+M_A,
$$

so the original problem must satisfy

$$
\boxed{C_{\max}\ge S_A+M_A}.
$$

By complete symmetry, we also obtain

$$
\boxed{C_{\max}\ge S_B+M_B}.
$$

Therefore, the overall lower bound is

$$
\boxed{
C_{\max}\ge
\max(S_A+S_B,\ S_A+M_A,\ S_B+M_B)
}.
$$

It remains to show that this lower bound can actually be attained. On machine $A$, process the jobs in $P$ consecutively in any order starting at time zero. On machine $B$, process the jobs in $Q$ consecutively starting at time zero. Then let the time at which $B$ begins the second stages of the jobs in $P$ be

$$
T_A=\max(S_B,M_A).
$$

Suppose a total workload of $p$ from the same group appears before some job $i\in P$. The $A$ stage of this job ends at

$$
p+a_i,
$$

while its $B$ stage begins at

$$
T_A+p.
$$

Since

$$
T_A\ge M_A\ge a_i,
$$

we have

$$
T_A+p\ge p+a_i,
$$

so the precedence constraint is always respected. Furthermore, $T_A\ge S_B$, so this stage does not overlap with the jobs in $Q$ that machine $B$ processed earlier.

Symmetrically, if machine $A$ starts the second stages of the jobs in $Q$ at

$$
T_B=\max(S_A,M_B),
$$

then every $B\rightarrow A$ constraint is also satisfied.

In this schedule, the finishing time of machine $B$ is

$$
\begin{aligned}
T_A+S_A
&=\max(S_B,M_A)+S_A\\
&=\max(S_A+S_B,\ S_A+M_A),
\end{aligned}
$$

while the finishing time of machine $A$ is

$$
\begin{aligned}
T_B+S_B
&=\max(S_A,M_B)+S_B\\
&=\max(S_A+S_B,\ S_B+M_B).
\end{aligned}
$$

Therefore, the overall makespan is exactly

$$
\boxed{
\max(S_A+S_B,\ S_A+M_A,\ S_B+M_B)
},
$$

which matches the lower bound derived above. It is therefore enough to know the sum and maximum of each group, giving an overall time complexity of $O(n)$.

## C. Prefix Painting

Let $p_i$ be the execution time of instruction $i$. Since every instruction is executed exactly once, we may regard $p_1,\ldots,p_n$ as distinct ranks. The instructions that paint cell $j$ are exactly

$$
j,j+1,\ldots,n,
$$

so the final color of cell $j$ is the color of the instruction executed latest among them. That is,

$$
f_j=c_{k_j},
\qquad
k_j=\operatorname*{argmax}_{i\ge j}p_i.
$$

When scanning $p_i$ from right to left, only positions that establish a new suffix maximum affect the final coloring. List these record positions in increasing order as

$$
r_1<r_2<\cdots<r_k=n.
$$

By the definition of a record, their execution times satisfy

$$
p_{r_1}>p_{r_2}>\cdots>p_{r_k}.
$$

For cells $1,\ldots,r_1$, instruction $r_1$ is the latest instruction in the suffix. For cells $r_1+1,\ldots,r_2$, instruction $r_2$ is the latest, and so on. Therefore, the final coloring has the form

$$
\underbrace{c_{r_1},\ldots,c_{r_1}}_{r_1},\;
\underbrace{c_{r_2},\ldots,c_{r_2}}_{r_2-r_1},\;\ldots,\;
\underbrace{c_{r_k},\ldots,c_{r_k}}_{n-r_{k-1}}.
$$

Conversely, any increasing sequence of positions containing $n$,

$$
r_1<r_2<\cdots<r_k=n,
$$

can be realized as the set of suffix records. Assign large execution times to the record positions in the order

$$
p_{r_1}>p_{r_2}>\cdots>p_{r_k},
$$

and assign every non-record position between two records a time smaller than the record to its right. Thus, possible final colorings are fundamentally determined by the choice of these record endpoints.

Counting record sets directly, however, creates duplicates. If two consecutive records have the same color,

$$
c_{r_j}=c_{r_{j+1}},
$$

their two blocks merge into one longer block of the same color in the actual coloring. To represent each final coloring uniquely, remove redundant consecutive records of the same color and retain only the **endpoints of maximal constant runs**. In this canonical representation,

$$
c_{r_j}\ne c_{r_{j+1}}
$$

always holds.

Define $dp_i$ as the number of distinct colorings whose last canonical endpoint is $i$. When $i$ is the only endpoint, the coloring

$$
(c_i,c_i,\ldots,c_i)
$$

is always possible, giving one base case. If there is an earlier endpoint $j<i$, the preceding part can be any of the $dp_j$ possibilities. The last two runs must have different colors in the canonical representation, so we require

$$
c_j\ne c_i.
$$

Therefore,

$$
\boxed{
dp_i
=
1+
\sum_{\substack{1\le j<i\\c_j\ne c_i}}dp_j
}.
$$

Let the sum of all preceding states be

$$
S_i=\sum_{j<i}dp_j,
$$

and let the sum of preceding states ending with color $x$ be

$$
C_x(i)=\sum_{\substack{j<i\\c_j=x}}dp_j.
$$

Then the recurrence becomes

$$
\boxed{
dp_i=1+S_i-C_{c_i}(i)
}.
$$

It is therefore enough to maintain the total sum and the sum for each color. At every position, they can be updated in $O(1)$ time as

$$
S\leftarrow S+dp_i,
\qquad
C_{c_i}\leftarrow C_{c_i}+dp_i.
$$

The only instruction that can paint the final cell $n$ is instruction $n$, so position $n$ is always a suffix record, and the canonical endpoints must also end at $n$. Therefore, the final answer is

$$
\boxed{dp_n}.
$$

For example, if $c=(1,2,1)$, then

$$
dp_1=1,
$$

$$
dp_2=1+dp_1=2,
$$

and

$$
dp_3=1+(dp_1+dp_2)-dp_1=3.
$$

These values correspond exactly to the three actual colorings

$$
(1,1,1),\qquad(1,2,1),\qquad(2,2,1).
$$

Every position is processed once, so the time complexity is $O(n)$, and the memory used to store the sum for each color is $O(n)$.

## D. Moving the Sand

Root the tree at vertex $1$. Let the total amounts of sand and workers in the subtree of a vertex $v$, after the entire subtree has been merged into $v$, be

$$
A_v=\sum_{x\in\operatorname{subtree}(v)}a_x,
\qquad
B_v=\sum_{x\in\operatorname{subtree}(v)}b_x.
$$

Regardless of the internal order in which the descendants are removed, all the sand and workers in the subtree have gathered at $v$ by the time $v$ is passed to its parent. Therefore, these two values are independent of the order.

Consequently, if we assume that every child subtree of $v$ has already been processed optimally, the only remaining choice at $v$ is the order in which those child subtrees are merged into it.

Suppose that the children are merged in the order

$$
\pi_1,\pi_2,\ldots,\pi_m.
$$

Immediately before the $i$-th child is merged, the amount of sand at $v$ is

$$
a_v+\sum_{j<i}A_{\pi_j}.
$$

Child $\pi_i$ contains $A_{\pi_i}$ units of sand and $B_{\pi_i}$ workers, so the cost of this operation is

$$
B_{\pi_i}
\left(
a_v+\sum_{j<i}A_{\pi_j}+A_{\pi_i}
\right).
$$

Therefore, the cost of merging all children at $v$ is

$$
\sum_{i=1}^{m}
B_{\pi_i}
\left(
a_v+A_{\pi_i}+\sum_{j<i}A_{\pi_j}
\right).
$$

Expanding this expression gives

$$
a_v\sum_iB_{\pi_i}
+
\sum_iA_{\pi_i}B_{\pi_i}
+
\sum_{j<i}A_{\pi_j}B_{\pi_i}.
$$

The first two terms do not depend on the order, so the only quantity that actually needs to be minimized is

$$
\boxed{
\sum_{j<i}A_{\pi_j}B_{\pi_i}
}.
$$

We now apply an exchange argument to two adjacent children $x$ and $y$. Suppose the parent currently contains $C$ units of sand. If $x$ is merged first and $y$ second, the part of the cost involving these two children is

$$
B_x(C+A_x)+B_y(C+A_x+A_y).
$$

If $y$ is merged first instead, the corresponding cost is

$$
B_y(C+A_y)+B_x(C+A_y+A_x).
$$

After canceling the common terms, the first order is no worse precisely when

$$
A_xB_y\le A_yB_x.
$$

Since every $B$ is positive, this is equivalent to

$$
\boxed{
\frac{A_x}{B_x}\le\frac{A_y}{B_y}
}.
$$

Thus, at each vertex, the child subtrees should be merged in nondecreasing order of $A_c/B_c$. In the implementation, floating-point arithmetic is unnecessary; it is enough to compare

$$
A_xB_y<A_yB_x.
$$

Define $dp_v$ as the minimum cost required to remove every vertex in the subtree of $v$ except $v$ itself. Sort the children in the ratio order above as

$$
c_1,c_2,\ldots,c_m,
$$

and initialize the amount of sand currently at the parent as

$$
cur=a_v.
$$

Then

$$
dp_v
=
\sum_{i=1}^{m}
\left(
dp_{c_i}
+
B_{c_i}(cur_i+A_{c_i})
\right),
$$

where

$$
cur_1=a_v,
\qquad
cur_{i+1}=cur_i+A_{c_i}.
$$

The aggregate values are

$$
A_v=a_v+\sum_iA_{c_i},
\qquad
B_v=b_v+\sum_iB_{c_i}.
$$

The root $1$ is never removed, so the answer is exactly

$$
\boxed{dp_1}.
$$

If $d_v$ is the number of children of $v$, sorting at vertex $v$ costs $O(d_v\log d_v)$, and

$$
\sum_vd_v=n-1.
$$

Hence, the total sorting cost satisfies

$$
\sum_vO(d_v\log d_v)\le O(n\log n).
$$

The overall time complexity is therefore $O(n\log n)$, and the tree and aggregate DP require $O(n)$ memory.

## E. Age Reports

Group together the people who have the same age, and let the group sizes in increasing order of age be

$$
g_1,g_2,\ldots,g_k.
$$

Let the number of people younger than the $j$-th group be

$$
L_j=\sum_{h<j}g_h,
$$

and let the number of people older than it be

$$
R_j=\sum_{h>j}g_h.
$$

Every person in this group reports

$$
\boxed{\min(L_j,R_j)}.
$$

Consider a group whose reported value is $x$. If this group lies in the left half, then

$$
L_j=x,
\qquad
R_j\ge x.
$$

If it lies in the right half, then

$$
R_j=x,
\qquad
L_j\ge x.
$$

Therefore, there can be at most one left group and at most one right group with the same reported value $x$. When

$$
L_j=R_j=x,
$$

there may instead be a single central group.

Let the number of people who report $x$ be

$$
c_x=\#\{i\mid a_i=x\},
$$

and define

$$
P_x=\sum_{y<x}c_y
$$

as the number of people whose reported value is smaller than $x$. We reconstruct the groups from the outside toward the center, processing reported values in increasing order.

Immediately before processing $x$, suppose $L$ people have already been placed on the left and $R$ people on the right. The people processed so far are exactly those with reported values smaller than $x$, so

$$
L+R=P_x.
$$

To create the next group with reported value $x$, the number of people outside that group must be exactly $x$. Thus, at least one frontier must satisfy

$$
L=x
\quad\text{or}\quad
R=x.
$$

If the opposite frontier were smaller than $x$, the next group formed from that side would also have a reported value smaller than $x$, contradicting the fact that every smaller value has already been processed. Therefore, the opposite frontier is at least $x$, and

$$
\boxed{
\{L,R\}=\{x,P_x-x\}
}.
$$

In particular, we must have

$$
\boxed{P_x\ge2x}.
$$

As soon as this condition fails, the answer is zero.

First, consider the case

$$
P_x>2x.
$$

Exactly one of the two frontiers is then equal to $x$, while the other is

$$
P_x-x>x.
$$

All $c_x$ people who report $x$ must belong to a single group on the side whose frontier is $x$. If two consecutive groups were formed on that side, adding the first group would make the number of outside people exceed $x$, so the second group could no longer report $x$. The opposite frontier already exceeds $x$ as well.

After placing all $c_x$ people into that one group, the two new frontiers are

$$
x+c_x
$$

and

$$
P_x-x.
$$

If the next reported value that actually appears is $y$, it must be the smaller of these two frontiers. Therefore, the condition

$$
\boxed{
y=\min(x+c_x,\ P_x-x)
}
$$

must hold. Otherwise, a reported value that should occur between $x$ and $y$ is missing, making the reconstruction impossible. In this case there is no choice about dividing people between the left and right; the number of age groups simply increases by one.

Now consider the case

$$
P_x=2x.
$$

The two frontiers are exactly equal:

$$
L=R=x.
$$

Suppose that a larger reported value $y>x$ still remains. If $p$ of the $c_x$ people are placed in the left group and $c_x-p$ in the right group, the new frontiers become

$$
x+p,
\qquad
x+c_x-p.
$$

Since the next reported value must be $y$, we need

$$
\min(x+p,\ x+c_x-p)=y.
$$

Set $g=y-x$. Then

$$
\boxed{
\min(p,c_x-p)=g
},
$$

so the necessary condition is

$$
\boxed{c_x\ge2g}.
$$

If

$$
c_x=2g,
$$

then necessarily

$$
p=g=c_x-p.
$$

The number of ways to choose which $g$ of the $c_x$ labeled people go to the left is

$$
\boxed{\binom{c_x}{g}}.
$$

On the other hand, if

$$
c_x>2g,
$$

the two group sizes are

$$
g,
\qquad
c_x-g.
$$

The smaller group may be either the left group or the right group, so the number of possibilities is

$$
\boxed{
2\binom{c_x}{g}
}.
$$

This case creates two age groups, one on the left and one on the right.

Finally, suppose that $x$ is the largest reported value that actually appears. If $P_x>2x$, the situation is the same as before: all $c_x$ people form one group on one side, and the reconstruction ends. The more interesting case is

$$
P_x=2x.
$$

There are already exactly $x$ people on each outer side, so the remaining $c_x$ people fill the center.

The first possibility is to place all $c_x$ people in one central group. This group has exactly $x$ younger people and $x$ older people, so it reports

$$
\min(x,x)=x,
$$

and there is no additional choice of how to divide its members.

The second possibility is to split the center into one left group and one right group. If $p$ people go to the left and $c_x-p$ go to the right, every split satisfying

$$
1\le p\le c_x-1
$$

is valid. The number of ways to choose the labeled people on the left is

$$
\binom{c_x}{p},
$$

and the sum over all nontrivial splits is

$$
\sum_{p=1}^{c_x-1}\binom{c_x}{p}
=
2^{c_x}-2.
$$

Thus, in the final equal-frontier state, the two possible structures are

$$
\boxed{\text{one central group}: 1}
$$

or

$$
\boxed{\text{two central groups}: 2^{c_x}-2}.
$$

So far, we have only decided how to assign people to an ordered sequence of age groups. To choose the actual ages, select as many distinct values from $1,\ldots,m$ as there are groups and assign them in increasing order. If there are $k$ groups, the ages satisfy

$$
1\le z_1<z_2<\cdots<z_k\le m,
$$

and the number of ways to choose them is

$$
\boxed{\binom{m}{k}}.
$$

Therefore, for each structure, multiplying its combinatorial factor for dividing the labeled people between the left and right groups by $\binom{m}{k}$ gives the number of actual age assignments.

For example, consider

$$
n=4,\qquad c_0=2,\qquad c_1=2.
$$

At $x=0$,

$$
P_0=0=2\cdot0,
$$

and the next reported value is $1$, so

$$
g=1.
$$

Furthermore,

$$
c_0=2=2g,
$$

so the two people are divided into the two outer groups, one person on each side. The number of ways is

$$
\binom21=2.
$$

At the final value $x=1$,

$$
P_1=2=2\cdot1.
$$

If the two remaining people form one central group, there are three groups in total, giving

$$
2\binom m3
$$

possibilities. If they are split into two central groups, the number of splits is

$$
2^2-2=2,
$$

and there are four groups, giving

$$
2\cdot2\binom m4
$$

possibilities. Hence, the total is

$$
\boxed{
2\binom m3+4\binom m4
},
$$

which is exactly $2$ when $m=3$, as in the sample.

We traverse the frequencies of the reported values and the values $x$ that actually appear only once. After precomputing factorials and inverse factorials, every binomial coefficient can be evaluated in $O(1)$, so the total time complexity is $O(n)$.

## F. Shortest Wiring

The vertices lie on a line in the order

$$
x_1<x_2<\cdots<x_n.
$$

First, decompose every edge cost into the sum of the adjacent coordinate gaps. The cost of one edge $u<v$ is

$$
x_v-x_u
=
\sum_{i=u}^{v-1}(x_{i+1}-x_i).
$$

Define

$$
g_i=x_{i+1}-x_i,
$$

and consider the cut

$$
\{1,\ldots,i\}\mid\{i+1,\ldots,n\}.
$$

If $c_i$ is the number of edges crossing this cut, the total cost of all edges is

$$
\boxed{
\operatorname{Cost}
=
\sum_{i=1}^{n-1}g_i c_i
}.
$$

Since every $g_i>0$, the problem is now to make the number $c_i$ of edges crossing each cut as small as possible.

Let the prefix degree sum be

$$
S_i=\sum_{j=1}^{i}d_j.
$$

If $e_i$ is the number of edges entirely inside the prefix $\{1,\ldots,i\}$, the degree-sum identity gives

$$
S_i=2e_i+c_i.
$$

The graph induced by the $i$ prefix vertices is a forest, so

$$
e_i\le i-1.
$$

Therefore,

$$
c_i=S_i-2e_i
\ge
S_i-2(i-1).
$$

In other words,

$$
\boxed{
c_i\ge S_i-2(i-1)
}.
$$

Apply the same argument to the suffix. Since the graph is a tree, the total degree sum is

$$
\sum_{j=1}^{n}d_j=2(n-1).
$$

Hence, the suffix degree sum is

$$
2(n-1)-S_i.
$$

Let $f_i$ be the number of edges entirely inside the suffix. The suffix contains $n-i$ vertices, so

$$
f_i\le n-i-1,
$$

and

$$
2(n-1)-S_i=2f_i+c_i.
$$

It follows that

$$
\begin{aligned}
c_i
&\ge
2(n-1)-S_i-2(n-i-1)\\
&=2i-S_i.
\end{aligned}
$$

Thus, we also need

$$
\boxed{
c_i\ge2i-S_i
}.
$$

Consequently, every tree satisfies

$$
\boxed{
c_i\ge
C_i
:=
\max\bigl(S_i-2(i-1),\,2i-S_i\bigr)
}.
$$

Equivalently,

$$
S_i-2(i-1)=1+\bigl(S_i-(2i-1)\bigr),
$$

and

$$
2i-S_i=1-\bigl(S_i-(2i-1)\bigr),
$$

so we may write

$$
\boxed{
C_i
=
1+\left\lvert S_i-(2i-1)\right\rvert
}
\qquad(1\le i<n).
$$

This gives a common lower bound on the cost of every tree:

$$
\boxed{
\operatorname{Cost}
\ge
\sum_{i=1}^{n-1}g_iC_i
}.
$$

The key point is that we can actually construct a tree satisfying

$$
c_i=C_i
$$

simultaneously for every cut. Such a tree exactly matches the lower bound for every choice of positive $g_i$ and is therefore optimal. Surprisingly, the magnitudes of the coordinate gaps do not affect the construction at all; only the order of the vertices matters.

For convenience, set

$$
C_0=C_n=0.
$$

Let $L_i$ be the number of edges from vertex $i$ to vertices on its left, and let $R_i$ be the number of edges from $i$ to vertices on its right. Clearly,

$$
L_i+R_i=d_i.
$$

When the cut moves one step from immediately after $i-1$ to immediately after $i$, the $L_i$ edges from $i$ to the left stop crossing the cut, while the $R_i$ edges from $i$ to the right begin crossing it. Therefore,

$$
C_i-C_{i-1}=R_i-L_i.
$$

Solving these two equations gives

$$
\boxed{
L_i
=
\frac{d_i-(C_i-C_{i-1})}{2}
}
$$

and

$$
\boxed{
R_i
=
\frac{d_i+(C_i-C_{i-1})}{2}
}.
$$

The fact that these values are integers also follows from degree-sum parity. For every cut of any tree,

$$
c_i\equiv S_i\pmod2.
$$

The two candidates defining $C_i$,

$$
S_i-2(i-1),\qquad2i-S_i,
$$

both have the same parity as $S_i$. Hence,

$$
C_i\equiv S_i\pmod2,
$$

and

$$
C_i-C_{i-1}
\equiv
S_i-S_{i-1}
=
d_i
\pmod2.
$$

Thus, both $L_i$ and $R_i$ are integers.

We now construct the edges while scanning the vertices from left to right. Store in a stack the left endpoints of edges whose right endpoints have not yet been encountered. Upon reaching vertex $i$, pop $L_i$ open slots from the stack and connect each of them to $i$. Then push $i$ onto the stack $R_i$ times, creating slots that will be connected to future vertices.

The most important invariant of this construction is that the stack size immediately after processing vertex $i$ is exactly $C_i$. Indeed, if the stack size before processing it is $C_{i-1}$, then

$$
\begin{aligned}
\left\lvert\mathrm{stack}'\right\rvert
&=
C_{i-1}-L_i+R_i\\
&=
C_{i-1}+(R_i-L_i)\\
&=
C_{i-1}+(C_i-C_{i-1})\\
&=
C_i.
\end{aligned}
$$

Thus, the stack represents exactly the number of edges that must leave the current prefix and enter the future suffix.

Moreover, for $1\le i<n$,

$$
C_i=1+\left\lvert S_i-(2i-1)\right\rvert\ge1,
$$

so the tree never becomes disconnected in the middle. At the end,

$$
C_n=0,
$$

so every open slot is consumed exactly once.

To see more precisely why a simple LIFO stack creates a tree without parallel edges, define, for every internal cut,

$$
h_i=S_i-(2i-1).
$$

Then

$$
C_i=1+\left\lvert h_i\right\rvert.
$$

Within a segment where $h_{i-1},h_i\ge0$,

$$
h_i-h_{i-1}=d_i-2,
$$

so

$$
C_i-C_{i-1}=d_i-2,
$$

and

$$
L_i=1,\qquad R_i=d_i-1.
$$

In other words, in a segment where the prefix side has more degree, each new vertex pops exactly one endpoint from the existing stack and therefore cannot be connected to the same previous vertex more than once.

Conversely, within a segment where $h_{i-1},h_i\le0$,

$$
C_i-C_{i-1}=2-d_i,
$$

which gives

$$
L_i=d_i-1,\qquad R_i=1.
$$

Thus, in a segment where the suffix side has more degree, each vertex leaves exactly one slot directed toward the future, keeping the endpoints in the stack separated. At the moment the sign changes from negative to positive, the current vertex merges the existing open components at once. This structure is why LIFO pairing produces a simple tree with the required left and right degrees.

Finally, the degree of vertex $i$ is

$$
L_i+R_i=d_i,
$$

the number of edges is

$$
\frac12\sum_{i=1}^{n}d_i=n-1,
$$

and every cut has exactly $C_i$ crossing edges. Therefore, the cost of the construction is

$$
\operatorname{Cost}
=
\sum_{i=1}^{n-1}g_iC_i,
$$

which exactly matches the lower bound and is optimal.

All cut values $C_i$ can be computed from the prefix degree sums in $O(n)$ time, and the stack construction creates each edge exactly once, so it also runs in $O(n)$ time. The total time complexity is

$$
\boxed{O(n)},
$$

and the stack and output edges require $O(n)$ memory.
