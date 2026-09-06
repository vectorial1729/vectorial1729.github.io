---
title: AtCoder Beginner Contest 474 Solution
date: 2026-09-06 22:10:00 +0900
categories: [Competitive Programming, AtCoder]
tags: [atcoder, abc474, competitive-programming]
math: true
---

This post summarizes the solutions to problems A through G of AtCoder Beginner Contest 474. The code has been uploaded separately to GitHub, so instead of reproducing the code itself here, I will focus on the formulas established by the submitted code and how those formulas are translated into an implementation.

## [A - Not X](https://atcoder.jp/contests/abc474/tasks/abc474_a)

The given value is $X\in\{1,2,3\}$, and we may output any value in the same set that is different from $X$. In other words, the only conditions on the value $Y$ that we need to find are $1\le Y\le3$ and $Y\ne X$. There are always at least two possible values, so there is no exceptional case to handle. The submitted code checks $1,2,3$ in order and outputs the first value $x$ satisfying $x\ne X$. Since the search range is always fixed at three values, the time complexity is $O(1)$.

## [B - Exit Order](https://atcoder.jp/contests/abc474/tasks/abc474_b)

When the seat numbers range from $1$ to $N$, the groups are formed as $\{1,\ldots,10\},\{11,\ldots,20\},\ldots$. Therefore, using zero-based indexing, the group number of the person in seat $x$ is

$$
g(x)=\left\lfloor\frac{x-1}{10}\right\rfloor.
$$

Likewise, in the exit order, the first ten people must belong to the first group, the next ten people must belong to the second group, and so on. Therefore, the group to which the person leaving at zero-based position $i$ must belong is

$$
h(i)=\left\lfloor\frac{i}{10}\right\rfloor.
$$

The order in which people from the same group leave is unrestricted, so we do not need to check the exact seat order. We only need to verify that the group at each position is correct. Thus, if

$$
\left\lfloor\frac{P_i-1}{10}\right\rfloor
=
\left\lfloor\frac{i}{10}\right\rfloor
$$

holds for every $0\le i<N$, the answer is `Yes`; if it fails even once, the answer is `No`. This formula remains valid even when the last group contains fewer than ten people. Since we traverse the array once, the time complexity is $O(N)$.

## [C - Remove and Append](https://atcoder.jp/contests/abc474/tasks/abc474_c)

For each query, the value $a_q$ is removed from the current permutation and appended to the end. If we actually delete it from an array every time, we may have to shift every element after the deleted position. This can take $O(N)$ time for one query and as much as $O(NQ)$ time overall. However, we only need the final permutation after all queries have been processed, rather than every intermediate state, so it is enough to consider the last time each value was moved.

Suppose a value $x$ never appears in the queries. Although this does not necessarily mean that it stays at the same position as in the initial permutation, its relative order with every other value that is also never moved can never change. Therefore, all such values appear at the front of the final permutation in exactly the same order in which they appeared in the original permutation $P$.

Conversely, for a value $x$ that appears in the queries, define its last occurrence time as

$$
L_x=\max\{q\mid a_q=x\}.
$$

Suppose both $x$ and $y$ are moved at least once and $L_x<L_y$. After $x$ is moved to the end for the last time, $y$ is moved to the end once more. Therefore, $x$ must appear before $y$ in the final state. In other words, the final order among the moved values is exactly the order determined by

$$
L_x<L_y.
$$

Consequently, the final permutation is completely determined as

$$
\bigl(\text{values that never appear, in their original order in }P\bigr)
+
\bigl(\text{values that appear, in increasing order of }L_x\bigr).
$$

The submitted code stores $L_x$ in `last[x]`. It first scans the original permutation $P$ and appends only the values satisfying `last[x] = -1` to the answer. It then scans the query sequence from the beginning and appends $a_q$ only when the current position $q$ equals `last[a_q]`. Even if a value appears multiple times, it is appended only at its last occurrence. Because the queries are read in chronological order, this automatically places the values in increasing order of $L_x$. The total time complexity is $O(N+Q)$, and the memory complexity is $O(N+Q)$.

## [D - Outweigh](https://atcoder.jp/contests/abc474/tasks/abc474_d)

Written as a formula, the required condition is

$$
\sum_{i=1}^{N}A_iW_i>\sum_{i=1}^{N}B_iW_i.
$$

Moving everything to one side, we need to construct positive integers $W_i\le10^{18}$ satisfying

$$
\sum_{i=1}^{N}(A_i-B_i)W_i>0.
$$

If we define $C_i=A_i-B_i$, the problem becomes constructing weights such that $\sum C_iW_i>0$.

If $A_i\le B_i$ for every $i$, or equivalently $C_i\le0$ for every $i$, then $W_i>0$ implies that every term satisfies $C_iW_i\le0$. Therefore,

$$
\sum_{i=1}^{N}C_iW_i\le0.
$$

In this case, no choice of weights can satisfy the condition.

Conversely, suppose there is at least one position $p$ such that $A_p>B_p$, or equivalently $C_p\ge1$. Assign the maximum weight

$$
W_p=10^{18}
$$

to that position and set every other weight to $W_i=1$. Then

$$
\sum_{i=1}^{N}C_iW_i
=
C_p\cdot10^{18}
+
\sum_{i\ne p}C_i.
$$

The first term is at least $10^{18}$ because $C_p\ge1$. On the other hand, since $1\le A_i,B_i\le10^9$, we have $C_i\ge-(10^9-1)$. Since $N\le10^5$, the absolute value of the total negative contribution from the remaining positions is at most roughly

$$
\left\lvert\sum_{i\ne p}\min(C_i,0)\right\rvert
<
10^5\cdot10^9
=
10^{14}.
$$

Therefore,

$$
C_p\cdot10^{18}+\sum_{i\ne p}C_i
>
10^{18}-10^{14}>0
$$

is guaranteed. Consequently, a solution exists if and only if there is at least one position satisfying $A_i>B_i$. When such a position exists, assigning the weight $10^{18}$ to that one position is sufficient. The time complexity is $O(N)$.

## [E - One Time Coupon](https://atcoder.jp/contests/abc474/tasks/abc474_e)

Buying item $i$ normally costs $A_i$ yen and gives us one coupon, while buying it with a coupon consumes one coupon and costs only $B_i$ yen. Since $B_i<A_i$ always holds, it is beneficial to buy a required item for $B_i$ whenever possible, but doing so requires the same number of coupons. The important point is that the purchase order is not essential. We can make all normal purchases first to obtain the coupons and then make all coupon purchases afterward, so only the total number of coupons needed in the final plan matters.

We must buy every item at least once. Consider choosing a set $S$ of items whose required purchase is made at the normal price $A_i$, while buying every other item at the coupon price $B_i$. Let $x=\lvert S\rvert$. The required normal purchases yield $x$ coupons, while the number of items bought with coupons is $N-x$, so we need $N-x$ coupons. The coupon deficit is

$$
(N-x)-x=N-2x.
$$

When this value is negative, no additional purchase is necessary. Therefore, the actual number of additional normal purchases is

$$
e=\max(0,N-2x).
$$

Additional purchases are made solely to create coupons, so it is optimal to repeat the cheapest normal purchase. Let

$$
c=\min_{i\in S}A_i
$$

be the smallest $A_i$ among the items in $S$. The additional cost is then $ec$.

If we first imagine buying every item for $B_i$, the base cost is

$$
B_{\mathrm{sum}}=\sum_{i=1}^{N}B_i.
$$

Changing item $i$ from a coupon purchase to a normal purchase increases the cost by

$$
D_i=A_i-B_i.
$$

Therefore, after choosing a particular set $S$, the total cost is exactly

$$
\boxed{
B_{\mathrm{sum}}
+
\sum_{i\in S}D_i
+
\max(0,N-2\lvert S\rvert)\min_{i\in S}A_i
}.
$$

Now, following the perspective used by the submitted code, fix one item $g$ whose $A_i$ is the smallest in $S$, and let

$$
c=A_g.
$$

Then $g\in S$, and every other item included in $S$ must satisfy $A_i\ge c$. If an item with $A_i<c$ were included, then $g$ would no longer have the minimum $A$ in $S$. That case is considered separately when the item with the smaller value is chosen as the new $g$. This is why the code processes the items in decreasing order of $A_i$ and activates only items whose $A_i$ is at least the current value $c$.

First, consider the state in which we choose only $S=\{g\}$. Its cost is

$$
B_{\mathrm{sum}}+D_g+\max(0,N-2)c.
$$

If we add another item $i$ to $S$, buying that item for $A_i$ instead of $B_i$ increases the cost by $D_i$. At the same time, the number of normally purchased items increases by one, which gives us one more coupon, while the number of items that must be bought with coupons decreases by one. Thus, the coupon balance improves by two in total.

As long as at least two additional purchases are still required, adding item $i$ lets us reduce the number of repeated purchases of $g$ by two, decreasing the cost by $2c$. Therefore, in this range, the net change caused by adding one item $i$ to $S$ is

$$
D_i-2c.
$$

Thus, it is beneficial to include every item satisfying $D_i<2c$. If there is a limit on how many items can be selected, it is optimal to choose them in increasing order of $D_i$.

Suppose $N=2h$ is even. When $\lvert S\rvert=h$,

$$
N-2\lvert S\rvert=2h-2h=0,
$$

so the need for additional purchases disappears completely. Since $g$ has already been selected, it is only worthwhile to select at most $h-1$ other items using the change $D_i-2c$. Therefore, among the activated items other than $g$ that satisfy $D_i<2c$, it is optimal to add the smallest

$$
\min\bigl(h-1,\#\{i:D_i<2c\}\bigr)
$$

values.

Now suppose $N=2h+1$ is odd. When $\lvert S\rvert=h$,

$$
N-2\lvert S\rvert=1,
$$

so exactly one additional purchase remains. The first $h-1$ selections made while growing $S=\{g\}$ to $\lvert S\rvert=h$ still save $2c$ each, so we choose the smallest values satisfying $D_i<2c$ first.

After that, when one more item is added and $\lvert S\rvert$ changes from $h$ to $h+1$, the number of additional purchases decreases from one to zero. This time, the saving is only $c$. Therefore, adding the final item is beneficial only when

$$
D_i-c<0,
\qquad\text{or equivalently}\qquad
D_i<c.
$$

The remaining problem is to perform this calculation quickly for every possible choice of $g$. The submitted code coordinate-compresses the sorted values $D_i$ and activates the items in decreasing order of $A_i$. When the current value is $c=A_g$, the data structure contains exactly the items satisfying $A_i\ge c$.

The `fenwick_tree_set` manages the number of active elements in each range of the compressed $D_i$ order, while `FenwickTree<long long>` manages the sum of the $D_i$ values in the same range. This lets us obtain

$$
\#\{i:D_i<2c\},
\qquad
\sum_{D_i<2c}D_i,
\qquad
\text{the sum of the smallest }k\text{ values }D_i
$$

in $O(\log N)$ time.

The item $g$ itself always satisfies $D_g=A_g-B_g<c<2c$, so it is included in this set. The code checks whether the rank of $g$ lies among the first $k$ elements and subtracts $D_g$ from either the prefix sum of the first $k$ elements or that of the first $k+1$ elements. This computes exactly the smallest values after excluding $g$. For odd $N$, the final candidate satisfying $D_i<c$ is handled by finding the next element in the same ordered set after excluding $g$. Combining the sorting and Fenwick Tree operations gives a total time complexity of $O(N\log N)$ over all test cases.

## [F - Increment All Divisors](https://atcoder.jp/contests/abc474/tasks/abc474_f)

When we choose an integer $i$ once, $A_j$ increases by one for every positive divisor $j$ of $i$. Suppose that we choose each $i$ exactly $x_i$ times in total and make every element equal to the same value $T$. The number of times position $j$ is incremented is the sum of $x_i$ over all $i$ satisfying $j\mid i$, which means all multiples of $j$. Therefore,

$$
T-A_j
=
\sum_{\substack{1\le i\le N\\j\mid i}}x_i.
$$

The right-hand side is the **multiple-direction zeta transform** of $x$. If we define the vector

$$
y_j=T-A_j,
$$

then

$$
y_j=\sum_{j\mid i}x_i.
$$

Therefore, applying multiple-direction Möbius inversion uniquely recovers $x$.

Let $\mathcal M$ denote the multiple-direction Möbius inverse transform. Then

$$
x=\mathcal M(y)
=
\mathcal M(T\mathbf 1-A).
$$

Because this transformation is linear,

$$
x
=
T\mathcal M(\mathbf 1)+\mathcal M(-A).
$$

If we define

$$
c=\mathcal M(\mathbf 1),
\qquad
d=\mathcal M(-A),
$$

then, for every $i$, we obtain the linear expression

$$
\boxed{x_i=c_iT+d_i}.
$$

This is exactly why the submitted code applies `multiple_transform::mobius` to both the array `c`, whose entries are all initially one, and the array `d`, whose entries are $-A_i$.

Written more directly, the multiple-direction Möbius inverse transform is

$$
x_i
=
\sum_{k=1}^{\lfloor N/i\rfloor}
\mu(k)y_{ik}.
$$

Therefore, we may also view the coefficients as

$$
c_i
=
\sum_{k=1}^{\lfloor N/i\rfloor}\mu(k),
$$

and

$$
d_i
=
-\sum_{k=1}^{\lfloor N/i\rfloor}\mu(k)A_{ik}.
$$

In the actual implementation, however, the library transform means that we do not need to construct these sums directly.

Since $x_i$ is a number of operations, it must satisfy

$$
x_i=c_iT+d_i\ge0.
$$

If $c_i>0$, then

$$
T\ge-\frac{d_i}{c_i},
$$

so for an integer $T$ we obtain the lower bound

$$
T\ge
\left\lceil-\frac{d_i}{c_i}\right\rceil.
$$

Conversely, if $c_i<0$, then

$$
T\le\frac{d_i}{-c_i},
$$

which gives the upper bound

$$
T\le
\left\lfloor\frac{d_i}{-c_i}\right\rfloor.
$$

If $c_i=0$, the condition simply becomes $d_i\ge0$. If $c_i<0$ and $d_i<0$, the upper bound on the right-hand side is itself negative. Since $T$ must be a positive value at least as large as every initial value, we can immediately determine that this case is impossible.

Moreover, every operation only increases values, so the final value must satisfy

$$
T\ge\max_i A_i.
$$

Combining $\max_i A_i$ with the maximum of all lower bounds, define

$$
L=
\max\left(
\max_i A_i,\;
\max_{c_i>0}
\left\lceil-\frac{d_i}{c_i}\right\rceil
\right).
$$

Also define the minimum of all upper bounds as

$$
R=
\min_{c_i<0}
\left\lfloor\frac{d_i}{-c_i}\right\rfloor.
$$

A feasible value of $T$ exists if and only if

$$
L\le R.
$$

When it exists, we want the minimum number of operations, so we choose the smallest feasible value

$$
T=L.
$$

Finally, there is no need to recompute the total number of operations as $\sum_i x_i$. No matter which $i$ is selected, $1\mid i$, so every operation increases $A_1$ by exactly one. Therefore, if $X$ denotes the total number of operations, then

$$
T=A_1+X,
$$

and hence

$$
\boxed{X=T-A_1}.
$$

This is why the final output is `lo - A[1]`. The library's multiple-direction Möbius transform updates multiples of primes, so its total running time is approximately $O(N\log\log N)$, while checking the inequalities afterward takes $O(N)$ time.

## [G - LRUD Moving 2](https://atcoder.jp/contests/abc474/tasks/abc474_g)

We need to construct a Hamiltonian path from $(1,1)$ to $(N,N)$ that visits all $N^2$ cells exactly once, while making the number of `R` moves exactly $K$. First, let us determine the possible range of $K$. Let $R,L,U,D$ denote the respective numbers of `R`, `L`, `U`, and `D` moves. Since the starting column is $1$ and the final column is $N$, the net horizontal displacement is

$$
R-L=N-1.
$$

Likewise, for the rows,

$$
D-U=N-1.
$$

Because every cell is visited exactly once, the total number of moves is

$$
R+L+U+D=N^2-1.
$$

Substituting $L=R-(N-1)$ and $D=U+(N-1)$ from the first two equations gives

$$
2R+2U=N^2-1.
$$

The left-hand side is even, so $N^2-1$ must also be even. Therefore, it is necessary that

$$
\boxed{N\text{ is odd}}.
$$

Also, $L\ge0$ implies

$$
R\ge N-1.
$$

Since $U\ge0$ and

$$
R+U=\frac{N^2-1}{2},
$$

we also have

$$
R\le\frac{N^2-1}{2}.
$$

Thus, at a minimum,

$$
N-1\le K\le\frac{N^2-1}{2}
$$

must hold. The Hamiltonian paths in this problem have an additional parity invariant: when an odd-sized square grid is visited completely with $(1,1)$ and $(N,N)$ as the two endpoints, the number of `R` moves is always even. This is why the submitted code also requires

$$
K\equiv0\pmod 2.
$$

Consequently, the range constructed by the code is

$$
\boxed{
N\text{ odd},\qquad
N-1\le K\le\frac{N^2-1}{2},\qquad
K\text{ even}
}.
$$

Now let us examine the actual construction. Since $N$ is odd, we can write

$$
N=2m+1.
$$

The minimum possible number of rightward moves is

$$
N-1=2m.
$$

The difference between the desired value $K$ and this minimum is always even, so define

$$
q=\frac{K-(N-1)}{2}.
$$

The range condition gives

$$
0\le q
\le
\frac{1}{2}
\left(
\frac{N^2-1}{2}-(N-1)
\right).
$$

Substituting $N=2m+1$ gives

$$
\frac{N^2-1}{2}=2m(m+1),
$$

and therefore

$$
0\le q\le m^2.
$$

Thus, the problem has been transformed into distributing an integer $q$ satisfying $0\le q\le m^2$ across the path structure.

The submitted code defines

$$
a=\min\left(\left\lfloor\frac{q}{m}\right\rfloor,m-1\right),
\qquad
b=q-am.
$$

When $q<m^2$, this is the ordinary division $q=am+b$ with $0\le b<m$. Only at the maximum value $q=m^2$ do we obtain $a=m-1$ and $b=m$, which allows the same construction to handle the endpoint as well. Therefore,

$$
q=am+b
$$

always holds.

First, group the top $2a$ rows into pairs. Within one block, move in the pattern

$$
R^{N-1}DL^{N-1}D.
$$

This visits the entire first row from left to right, moves down by one cell, visits the entire second row from right to left, and then moves down by one more cell to reach the starting point of the next block. A single block uses exactly

$$
N-1=2m
$$

`R` moves, so the $a$ blocks create

$$
a(N-1)=2am
$$

`R` moves.

For the next two rows, use only the first $2b+1$ columns rather than the entire width. Starting from the left edge, move in the pattern

$$
R^{2b}DL^{2b}.
$$

This visits columns $1$ through $2b+1$ of the upper row and then the same range of the lower row without overlap, before returning to the left edge. This part adds

$$
2b
$$

`R` moves.

Next, fill the still-unvisited lower region with a vertical snake. Define the width of the first region as

$$
w_1=2b+1.
$$

First move downward to the bottom in the current column. Each time we move one column to the right afterward, alternate between moving upward and downward. To consume every column from $1$ through $w_1$, the number of moves between adjacent columns is

$$
w_1-1=2b,
$$

so this part produces another $2b$ `R` moves.

The width of the remaining region on the right is

$$
w_2=N-w_1=N-(2b+1).
$$

If $w_2>0$, entering the right region and changing columns within it produce exactly $w_2$ `R` moves in total. If $b=m$, then

$$
w_1=2m+1=N,
\qquad
w_2=0,
$$

so the full width has already been used and the second region does not exist.

Therefore, the total number of `R` moves is

$$
\begin{aligned}
R
&=a(N-1)+2b+2b+\bigl(N-(2b+1)\bigr)\\
&=a(N-1)+2b+N-1\\
&=2am+2b+N-1\\
&=N-1+2(am+b)\\
&=N-1+2q\\
&=K.
\end{aligned}
$$

Thus, the constructed string moves to the right exactly the desired number of times. Each part is designed to fill a rectangular region in a snake pattern without overlapping any region that has already been visited, so every cell is visited exactly once. The final vertical snake ends at $(N,N)$.

The string that must be printed always has length

$$
N^2-1,
$$

so any algorithm requires at least $\Omega(N^2)$ time just to produce the output. The submitted code generates the path directly in one pass, taking $O(N^2)$ time for each test case and $O(\sum N^2)$ time under the overall constraint $\sum N^2\le10^6$.
