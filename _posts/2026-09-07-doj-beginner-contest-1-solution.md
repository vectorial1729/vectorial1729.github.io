---
title: DOJ Beginner Contest 1 Solution
date: 2026-09-07 22:00:00 +0900
categories: [Competitive Programming, DOJ]
tags: [doj, bcd, competitive-programming]
math: true
---

This post summarizes the solutions to problems A through I of [DOJ Beginner Contest 1](https://doj.kr/en/contests/doj-beginner-contest-1). The earlier problems require only one or two key observations, but the later problems depend increasingly on identifying the exact structure: maintaining dynamic states, $2$-adic valuations, the number-theoretic structure of generated data, a max-plus automaton, and the prefix/suffix/cross decomposition of a histogram.

## A. Easy String Problem

We want to place as many overlapping copies of a string $S$ as possible inside $T$. The important question is how little one copy of $S$ can be shifted relative to another while keeping their overlap valid.

Let the length of $S$ be $N$, and place the second copy $d$ positions to the right of the first. The overlapping part must satisfy

$$
S_{d+1}S_{d+2}\cdots S_N
=
S_1S_2\cdots S_{N-d}.
$$

Equivalently,

$$
S[d..N-1]=S[0..N-d-1].
$$

This means that a prefix of length $N-d$ is also a suffix.

If $L$ is the length of the longest proper border of $S$, the smallest possible positive shift is

$$
\boxed{p=N-L}.
$$

Using the KMP prefix function $\pi$, we have

$$
L=\pi_{N-1},
$$

and therefore

$$
\boxed{p=N-\pi_{N-1}}.
$$

The value $p$, often called the minimum period, does not have to divide $N$. The only condition we need is

$$
S_i=S_{i-p}
\qquad(p<i\le N),
$$

and this condition means that $S$ is a prefix of the infinite repetition of its own prefix of length $p$.

Indeed, define

$$
P=S_1S_2\cdots S_p,
$$

and imagine the infinite string

$$
U=PPPP\cdots.
$$

By the periodicity condition of $S$,

$$
U_1U_2\cdots U_N=S.
$$

Thus, if $T$ is the first $M$ characters of $U$, then $S$ repeatedly appears at starting positions

$$
1,\ 1+p,\ 1+2p,\ \ldots.
$$

When $M\ge N$, the number of occurrences is

$$
1+\left\lfloor\frac{M-N}{p}\right\rfloor.
$$

This is also the maximum possible number. Suppose $S$ appears in $T$ at two distinct starting positions $x<y$. Let the distance between them be

$$
d=y-x.
$$

If $d<N$, the two occurrences overlap, so $d$ must be a valid shift of $S$. Since the minimum valid shift is $p$,

$$
d\ge p.
$$

If they do not overlap, then from the beginning

$$
d\ge N\ge p,
$$

so the same inequality holds.

Therefore, the starting positions of any two consecutive occurrences must be at least $p$ apart, and a string of length $M$ cannot contain more than

$$
\boxed{
1+\left\lfloor\frac{M-N}{p}\right\rfloor
}
$$

occurrences. The periodic construction above attains this upper bound exactly.

If $M<N$, $S$ cannot occur even once in any possible $T$, so we may output any string of length $M$. We can still output the prefix of the repeated string $P$.

Computing the prefix function takes $O(N)$ time, and printing $T$ takes $O(M)$ time. Hence, the total time complexity is

$$
\boxed{O(N+M)}.
$$

## B. Make It Increasing

Suppose we apply the operation $t_i\ge0$ times to each element.

First, consider $K>0$. The final value is

$$
B_i=A_i+t_iK,
$$

and the goal is to satisfy

$$
B_1<B_2<\cdots<B_N
$$

while minimizing

$$
\sum_{i=1}^{N}t_i.
$$

There is no reason to increase the first element. Making $B_1$ larger only raises the lower bounds that all later elements must satisfy, so the optimum has

$$
t_1=0,\qquad B_1=A_1.
$$

Suppose the optimal values through $B_{i-1}$ have already been fixed. Since $B_i$ must satisfy

$$
A_i+t_iK>B_{i-1},
$$

we need

$$
t_iK>B_{i-1}-A_i.
$$

Because $t_i$ is a nonnegative integer, its minimum possible value is

$$
\boxed{
t_i=
\max\left(
0,\,
\left\lfloor\frac{B_{i-1}-A_i}{K}\right\rfloor+1
\right)
}.
$$

In particular, if $A_i>B_{i-1}$, then

$$
t_i=0.
$$

Otherwise, we apply the operation exactly as many times as necessary to make the value exceed $B_{i-1}$.

To see why this greedy choice is globally optimal, applying the operation more times than necessary at position $i$ only increases the current cost and makes the next constraint

$$
B_{i+1}>B_i
$$

harder to satisfy. It offers no future benefit. Therefore, choosing the smallest feasible $B_i$ at every position is globally optimal.

Now consider $K<0$, and set

$$
H=-K>0.
$$

The operation becomes

$$
A_i\mapsto A_i-H,
$$

and the final value is

$$
B_i=A_i-t_iH.
$$

Viewed from the left, decreasing an earlier element helps its current constraint but also interacts with the later elements. The completely symmetric and natural approach is to scan from right to left.

There is no reason to decrease the last element, so

$$
t_N=0,\qquad B_N=A_N.
$$

For the preceding position $i$, we need

$$
B_i<B_{i+1},
$$

or equivalently,

$$
A_i-t_iH<B_{i+1}.
$$

Thus,

$$
t_iH>A_i-B_{i+1},
$$

and therefore

$$
\boxed{
t_i=
\max\left(
0,\,
\left\lfloor\frac{A_i-B_{i+1}}{H}\right\rfloor+1
\right)
}.
$$

In conclusion, when $K>0$, scan greedily from left to right; when $K<0$, scan greedily from right to left. Only one pass is required. The time complexity is

$$
\boxed{O(N)},
$$

and the additional memory usage is $O(1)$.

## C. 01 10

Let the number of positions whose two adjacent characters differ be

$$
D=
\sum_{i=1}^{N-1}[S_i\ne S_{i+1}].
$$

Since the string contains only zeros and ones, we may interpret each term as

$$
[S_i\ne S_{i+1}]
=
S_i\oplus S_{i+1}.
$$

Chaining these terms from the XOR perspective gives

$$
(S_1\oplus S_2)
\oplus
(S_2\oplus S_3)
\oplus\cdots\oplus
(S_{N-1}\oplus S_N)
=
S_1\oplus S_N.
$$

Every middle character $S_2,\ldots,S_{N-1}$ appears twice and cancels out.

Therefore, the parity of $D$ is

$$
\boxed{
D\bmod2=S_1\oplus S_N
},
$$

and

$$
D\text{ is even}
\iff
\boxed{S_1=S_N}.
$$

The problem is now equivalent to making the first and last characters equal using the minimum number of adjacent swaps.

Suppose we place some character $c\in\{0,1\}$ at both ends. If the positions containing $c$ are

$$
p_1<p_2<\cdots<p_r,
$$

we need two distinct occurrences of $c$, so

$$
r\ge2.
$$

The minimum cost of making the left endpoint equal to $c$ is moving the leftmost occurrence, at $p_1$, to the first position. This takes

$$
p_1-1
$$

swaps. Similarly, the minimum cost of making the right endpoint equal to $c$ is moving the rightmost occurrence, at $p_r$, to position $N$. This takes

$$
N-p_r
$$

swaps.

These are distinct occurrences, so the two movements can be performed without conflict, and their total cost is

$$
\boxed{
(p_1-1)+(N-p_r)
}.
$$

Compute this value for both $0$ and $1$ and take the minimum. If neither character appears at least twice, it is impossible to place the same character at both ends. For a binary string with $N\ge2$, this situation is simply a length-two string containing each character once.

Hence, the answer is

$$
\boxed{
\min_{c\in\{0,1\},\ \operatorname{cnt}(c)\ge2}
\left(
\operatorname{first}(c)-1
+
N-\operatorname{last}(c)
\right)
},
$$

or $-1$ if there is no valid $c$.

One scan of the string is enough to find the count and the first and last positions of each character, so the time complexity is

$$
\boxed{O(N)}.
$$

## D. Square Tiling

The answer is

$$
\boxed{
A+B-\gcd(A,B)
}.
$$

First, we show that this value is achievable. Assume $A\ge B$. Place one $B\times B$ square on the left, leaving an

$$
(A-B)\times B
$$

rectangle to be tiled in the same manner. If $F(A,B)$ denotes the minimum cost, then

$$
F(A,B)\le B+F(A-B,B).
$$

Repeating this process as in the Euclidean algorithm, when

$$
A=qB+r
\qquad(0\le r<B),
$$

we can place $q$ squares of size $B\times B$ and reduce the remaining rectangle to $r\times B$. Therefore,

$$
F(A,B)\le qB+F(r,B).
$$

If $r=0$, the tiling ends with $q$ squares of size $B\times B$, and its cost is

$$
qB=A.
$$

Substituting into the formula gives the same value:

$$
A+B-\gcd(A,B)
=
A+B-B=A.
$$

If $r>0$, induction gives

$$
F(r,B)\le r+B-\gcd(r,B),
$$

and

$$
\gcd(r,B)=\gcd(A,B).
$$

Thus,

$$
\begin{aligned}
F(A,B)
&\le qB+r+B-\gcd(r,B)\\
&=A+B-\gcd(A,B).
\end{aligned}
$$

Therefore,

$$
\boxed{
F(A,B)\le A+B-\gcd(A,B)
}.
$$

For the opposite direction, we use the standard lower-bound lemma for integer-grid square tilings.

> If an integer $a\times b$ rectangle is tiled perfectly with squares of integer side lengths, the sum of all square side lengths is at least the following value.

$$
a+b-\gcd(a,b).
$$

This lemma follows by induction from the fact that its lower bound is preserved under the Euclidean reduction that removes one layer of squares whose total width is the shorter side. When $a\ge b$, every tiling must pay a cost of at least $b$ to advance through a width of $b$, and the remaining part costs at least the lower bound for the $(a-b)\times b$ problem. Hence,

$$
F(a,b)\ge b+F(a-b,b).
$$

Applying this at every step of the Euclidean algorithm eventually reaches the final $g\times g$ square, where $g=\gcd(a,b)$, and

$$
F(g,g)=g.
$$

The accumulated value is exactly

$$
a+b-g.
$$

The upper and lower bounds therefore match, proving

$$
\boxed{
F(A,B)=A+B-\gcd(A,B)
}.
$$

For example, for a $6\times4$ rectangle,

$$
\gcd(6,4)=2,
$$

so

$$
6+4-2=8.
$$

Using one $4\times4$ square and two $2\times2$ squares indeed gives a cost of

$$
4+2+2=8.
$$

Only one gcd computation is required, so the time complexity is

$$
\boxed{O(\log\min(A,B))}.
$$

## E. Subarray Sums and Queries

Every subarray sum of length $R$ must be equal to $K$. That is,

$$
A_i+A_{i+1}+\cdots+A_{i+R-1}=K
$$

must hold for every

$$
1\le i\le N-R+1.
$$

Subtract two adjacent equations:

$$
(A_i+\cdots+A_{i+R-1})
-
(A_{i+1}+\cdots+A_{i+R})
=0.
$$

All middle terms cancel, giving

$$
\boxed{A_i=A_{i+R}}.
$$

Therefore, all elements whose indices have the same remainder modulo $R$ must have the same value. Using zero-based indices,

$$
i\equiv j\pmod R
\quad\Longrightarrow\quad
A_i=A_j.
$$

Let the common value of residue class $r\in\{0,\ldots,R-1\}$ be

$$
x_r.
$$

Every subarray of length $R$ contains each residue exactly once, so its sum is always

$$
x_0+x_1+\cdots+x_{R-1}.
$$

The two requirements can therefore be compressed into exactly the following conditions:

$$
\boxed{\text{All fixed values in the same residue class must be equal}}
$$

and

$$
\boxed{x_0+x_1+\cdots+x_{R-1}=K}.
$$

If any residue class already contains two different fixed values, the assignment is immediately impossible. For example, if both $3$ and $7$ are fixed in the same class, we would need

$$
x_r=3,\qquad x_r=7
$$

simultaneously, which is a contradiction.

Conversely, suppose every nonempty class has one consistent value. If at least one class contains no fixed value, a valid assignment always exists. The unspecified elements may contain **any integer**, so there is no range restriction. For example, if class $r$ is completely free, choose the other class values first and set

$$
\boxed{
x_r
=
K-\sum_{j\ne r}x_j
}.
$$

Thus, we only need to check the sum condition directly when every class already contains at least one fixed value. In that case, every class value is forced, so we test whether

$$
\boxed{
\sum_{r=0}^{R-1}x_r=K
}.
$$

We now maintain these conditions efficiently after every query. For each residue $r$, store its current fixed values in a multiset. The class is consistent precisely when

$$
\min_r=\max_r.
$$

Globally, it is enough to maintain the following three values:

$$
B=\#\{\text{bad classes containing at least two distinct fixed values}\},
$$

$$
C=\#\{\text{nonempty classes containing at least one fixed value}\},
$$

$$
S=\sum_{\substack{r:\ \text{nonempty}\\\text{class }r\text{ consistent}}}x_r.
$$

After a query, the answer condition is simple.

First, if

$$
B>0,
$$

some values already conflict inside the same residue class, so the answer is necessarily `NO`.

Otherwise, if

$$
C<R,
$$

at least one class is completely free, and we can use it to adjust the total sum to $K$. The answer is `YES`.

Finally, if

$$
C=R,
$$

every class value is forced, so the answer is `YES` only when

$$
S=K.
$$

In other words,

$$
\boxed{
\text{YES}
\iff
B=0
\ \land\
(C<R\ \lor\ S=K)
}.
$$

Each query only inserts or removes one value in the multiset of one residue class, which takes $O(\log N)$ time. The total time complexity is

$$
\boxed{O((N+Q)\log N)}.
$$

## F. +2 x2

The original variable $X$ starts at $2$, and the operations are

$$
A:\ X\leftarrow X+2,
\qquad
M:\ X\leftarrow2X.
$$

The value that must be printed after every operation is

$$
\max\{k:2^k\mid X\}
=
v_2(X).
$$

Since $X$ is always even, write

$$
X=2Y.
$$

Initially,

$$
Y=1.
$$

The operations become

$$
A:\ Y\leftarrow Y+1,
\qquad
M:\ Y\leftarrow2Y,
$$

and

$$
\boxed{
v_2(X)=1+v_2(Y)
}.
$$

Operation $M$ is straightforward:

$$
v_2(2Y)=v_2(Y)+1.
$$

The difficult part is finding $v_2(Y+1)$ under operation $A$,

$$
Y\leftarrow Y+1.
$$

Consider the least significant end of the binary representation. For example, suppose $Y$ ends as

$$
Y=(\cdots111000)_2.
$$

The form of $Y+1$ is determined completely by the trailing run of equal bits.

If $Y$ ends in $z\ge1$ zeros as

$$
(\cdots 1\,0^z)_2,
$$

then

$$
Y+1=(\cdots1\,0^{z-1}1)_2.
$$

In particular, when $z>1$, the trailing run becomes a single $1$; when $z=1$, that bit merges into the run of ones immediately above it.

Conversely, if $Y$ ends in $o\ge1$ ones as

$$
Y=(\cdots0\,1^o)_2,
$$

the binary carry passes through all $o$ ones, giving

$$
Y+1=(\cdots1\,0^o)_2.
$$

Therefore,

$$
\boxed{v_2(Y+1)=o}.
$$

There is no need to store the entire value of $Y$ as a big integer. It is enough to maintain, from the least significant end, how many consecutive equal bits appear in each run. Represent the state as

$$
(b_1,\ell_1),(b_2,\ell_2),\ldots,
$$

where $b_1$ is the bit of the current least significant run and $\ell_1$ is its length.

Operation $M$ appends one zero to the binary representation:

$$
Y\mapsto Y0_2.
$$

If the first run is already a zero-run, update

$$
\ell_1\leftarrow\ell_1+1.
$$

If the first run is a one-run, insert a new

$$
(0,1)
$$

at the front.

Operation $A$ processes a carry.

If the first run consists of $\ell$ ones, then

$$
1^\ell\mapsto0^\ell,
$$

and the next zero changes into a one. Thus, we turn the first one-run into a zero-run and merge lengths with the next run as necessary.

If the first run is a zero-run, only its first zero changes into a one. When the zero-run has length greater than one,

$$
0^\ell\mapsto1\,0^{\ell-1}.
$$

When its length is exactly one, that run disappears and the new one merges into the following one-run.

Each operation modifies only a few runs at the front of the deque, so its amortized complexity is $O(1)$. Over the full sequence, every run is created, merged, and deleted only a linear number of times in total, which is sufficient even for $N\le10^6$.

The output depends only on the first run. If the first run is a zero-run of length $\ell$, then

$$
v_2(Y)=\ell,
$$

so

$$
\boxed{v_2(X)=1+\ell}.
$$

If the first run is a one-run, then

$$
v_2(Y)=0,
$$

and therefore

$$
\boxed{v_2(X)=1}.
$$

The total time complexity is

$$
\boxed{O(N)},
$$

and the run deque uses $O(N)$ memory in the worst case.

## G. 3SUM

The input is not actually an arbitrary array. By its generation rule,

$$
A_i\equiv2^{i-1}\pmod M.
$$

Thus, we need to find

$$
2^{i-1}+2^{j-1}+2^{k-1}\equiv0\pmod M
$$

with

$$
1\le i<j<k\le N.
$$

Separate $M$ into its power-of-two part and its odd part:

$$
\boxed{
M=2^q m,
\qquad m\text{ odd}
},
$$

where

$$
q=v_2(M).
$$

Write the three exponents as

$$
e_1=i-1<e_2=j-1<e_3=k-1.
$$

Their sum factors as

$$
2^{e_1}
\left(
1+2^{e_2-e_1}+2^{e_3-e_1}
\right).
$$

The value inside the parentheses has the form

$$
1+\text{even}+\text{even},
$$

so it is always odd. For the sum to be divisible by $2^q$, we must have

$$
\boxed{e_1\ge q}.
$$

Now suppose some solution exists. Write $e_1=q+s$. Then

$$
2^{q+s}
\left(
1+2^u+2^v
\right)
\equiv0\pmod{2^qm},
$$

where

$$
u=e_2-e_1,\qquad v=e_3-e_1,\qquad 1\le u<v.
$$

Canceling $2^q$ gives

$$
2^s(1+2^u+2^v)\equiv0\pmod m.
$$

Since $m$ is odd,

$$
\gcd(2^s,m)=1,
$$

and therefore

$$
\boxed{
1+2^u+2^v\equiv0\pmod m
}.
$$

We can now shift every exponent to the left by $s$ and use

$$
q,\quad q+u,\quad q+v
$$

to obtain another solution. In other words, **if any solution exists, there must be a solution whose smallest exponent is exactly $q$.**

Therefore, the first index may be fixed as

$$
\boxed{i=q+1}.
$$

The remaining condition is

$$
1+2^u+2^v\equiv0\pmod m,
$$

or equivalently,

$$
\boxed{
2^u+2^v\equiv-1\pmod m
}.
$$

The available exponent differences satisfy

$$
1\le u<v\le N-1-q.
$$

Define

$$
D=N-1-q,
$$

and compute

$$
r_t=2^t\bmod m
\qquad(1\le t\le D).
$$

Every residue satisfies

$$
0\le r_t<m,
$$

so the sum of two residues lies in the range

$$
0\le r_u+r_v\le2m-2.
$$

Meanwhile, we need

$$
r_u+r_v\equiv-1\equiv m-1\pmod m.
$$

Within the possible range, the only valid value is

$$
\boxed{r_u+r_v=m-1}.
$$

The next congruent value, $2m-1$, is greater than the maximum $2m-2$ and cannot occur.

The problem has now become an ordinary two-pointer search for two distinct elements whose sum is $m-1$ in a sorted residue array.

If

$$
D<2,
$$

there is no room to place two distinct exponents after the first exponent $q$, so the answer is immediately `NO`.

Otherwise, sort the pairs

$$
(r_t,t),
$$

and compare

$$
r_l+r_r
$$

using pointers $l$ and $r$ at the two ends.

If

$$
r_l+r_r<m-1,
$$

increment $l$. If

$$
r_l+r_r>m-1,
$$

decrement $r$. If

$$
r_l+r_r=m-1,
$$

we have found a solution.

For the resulting $u,v$, the actual indices are

$$
\boxed{
i=q+1,\qquad
j=q+u+1,\qquad
k=q+v+1
}.
$$

The residues can be generated in $O(N)$ time using

$$
r_{t+1}=2r_t\bmod m.
$$

Sorting takes $O(N\log N)$, and the two-pointer scan takes $O(N)$. Therefore, the total time complexity is

$$
\boxed{O(N\log N)}.
$$

## H. 67 Eradicator

We want to delete some of the numbers $1,2,\ldots,N$ so that, after concatenating the decimal representations of the remaining numbers in order, `67` never appears. Minimizing the number of deleted values is equivalent to maximizing the number of values we keep.

First, consider one number independently. If the decimal representation of an integer $x$ already contains `67`, then $x$ can never be kept. No matter which other numbers are deleted, those two digits remain adjacent inside $x$.

Now consider only numbers that do not contain `67` internally. For two distinct kept numbers $a<b$, a new `67` appears across their boundary precisely when

$$
\boxed{
a\text{ ends in }6
\quad\land\quad
b\text{ begins with }7
}.
$$

Therefore, the only state we need is whether the previously kept number ends in the digit $6$.

$$
dp_0=\text{maximum count when the last kept number does not end in }6,
$$

$$
dp_1=\text{maximum count when the last kept number ends in }6.
$$

If $x$ contains an internal `67`, it must be skipped, so the state remains unchanged.

For every other number, define

$$
f(x)=[x\text{ begins with }7],
\qquad
\ell(x)=[x\text{ ends in }6].
$$

If we skip the number, the transitions are

$$
0\to0,\qquad1\to1,
$$

with a score increase of zero.

If we keep the number, the new state is always

$$
\ell(x),
$$

and the score increases by one. However, if the previous state is $1$ and $f(x)=1$, keeping it would create `67` at the boundary and is forbidden.

Represent these transitions by a max-plus matrix. Let the rows denote new states and the columns old states, and define

$$
T_x[a][b]
=
\text{maximum score increase for the transition from state }b\text{ to }a.
$$

Impossible transitions have value $-\infty$.

Skipping always gives

$$
T_x[0][0]\ge0,\qquad
T_x[1][1]\ge0.
$$

When keeping is valid,

$$
T_x[\ell(x)][0]\ge1,
$$

and only when $f(x)=0$ do we also have

$$
T_x[\ell(x)][1]\ge1.
$$

If the matrices $A$ and $B$ for two consecutive ranges are applied in that order, their combined result is the max-plus product obtained by replacing multiplication and addition in ordinary matrix multiplication with addition and maximum, respectively:

$$
\boxed{
(B\otimes A)_{ij}
=
\max_k(B_{ik}+A_{kj})
}.
$$

Therefore, if we can multiply all transition matrices for $1,\ldots,N$, the answer follows immediately. Initially, no number has been selected, so we start in state $0$ with

$$
v_{\mathrm{init}}
=
\begin{pmatrix}
0\\
-\infty
\end{pmatrix}.
$$

If the total product is $M$, the maximum number of values we can keep is

$$
\boxed{
\max(M_{0,0},M_{1,0})
}.
$$

Hence, the answer is

$$
\boxed{
N-\max(M_{0,0},M_{1,0})
}.
$$

The difficulty is that $N\le10^{18}$, so processing every number individually is impossible. We use digit DP in the form of a decimal trie.

Listing all fixed-length numbers in numerical order is the same as listing their decimal strings in lexicographical order. Suppose a prefix has already been fixed. Only two pieces of information are needed while generating the remaining suffix.

The first is whether the first digit is $7$:

$$
f\in\{0,1\}.
$$

The second is whether the current last digit of the prefix is $6$:

$$
p\in\{0,1\}.
$$

When $r$ digits remain, let the matrix product obtained by processing every valid completion in numerical order be

$$
F[r][f][p].
$$

If no digits remain, one number has been completed, so

$$
\boxed{
F[0][f][p]=T_{f,p}
}.
$$

Here, $T_{f,p}$ is the transition matrix of one valid number whose first digit is $7$ exactly when $f=1$ and whose last digit is $6$ exactly when $p=1$.

For $r>0$, append the next digit $d=0,1,\ldots,9$ in order. However, if the current last digit is $6$ and

$$
d=7,
$$

the entire branch is discarded because it creates an internal `67`.

Therefore,

$$
\boxed{
F[r][f][p]
=
\bigotimes_{\substack{d=0\\\neg(p=1\land d=7)}}^{9}
F[r-1][f][d=6]
}.
$$

The product is taken in increasing order $d=0,1,\ldots,9$.

To process every positive integer of length $L$, the first digit ranges from $1$ through $9$, so

$$
\boxed{
G_L
=
\bigotimes_{d=1}^{9}
F[L-1][d=7][d=6]
}.
$$

Now write the decimal representation of $N$ as

$$
S=s_1s_2\cdots s_L.
$$

All numbers of lengths $1,\ldots,L-1$ are handled by multiplying $G_1,G_2,\ldots,G_{L-1}$ in order.

For length-$L$ numbers at most $N$, follow the tight prefix as in an ordinary digit DP. At position $i$, where the limiting digit is $s_i$, consider every smaller possible digit

$$
d<s_i.
$$

For each such digit, process its entire remaining suffix at once with

$$
F[L-i][f][d=6].
$$

Then append the actual digit $s_i$ on the tight branch and proceed to the next position.

If the tight prefix creates `67` at some point, every number under that prefix also contains an internal `67`, so no number on that branch can be kept. Conversely, if the prefix remains valid through the end, multiply the transition matrix of $N$ itself last.

There are at most $19$ digits, and every state matrix is only $2\times2$, so the amount of computation is essentially constant. More precisely, it requires about

$$
O(19\cdot10\cdot2^3)
$$

max-plus operations.

Thus, even for $N\le10^{18}$, the entire problem can be solved in

$$
\boxed{O(\log_{10}N)}.
$$

## I. Insert One Histogram Bar

Let the insertion position $k$ be one of

$$
0,1,\ldots,N.
$$

It is convenient to regard $k$ as the cut

$$
A_k\mid A_{k+1}
$$

in the original array. Here, $k=0$ means the very beginning and $k=N$ means the very end.

In the histogram obtained after inserting $x$, the maximum rectangle belongs to one of three types.

First, it may avoid the inserted bar entirely and lie only on the left. This is the maximum rectangle in the original histogram prefix

$$
A_1,\ldots,A_k.
$$

Denote this value by

$$
P_k.
$$

Second, it may avoid the inserted bar and lie only on the right. This is the maximum rectangle in the suffix

$$
A_{k+1},\ldots,A_N.
$$

Denote this value by

$$
S_k.
$$

Third, the rectangle may be required to contain the newly inserted bar of height $x$. Denote this value by

$$
C_k.
$$

The answer is therefore exactly

$$
\boxed{
\operatorname{Ans}_k
=
\max(P_k,S_k,C_k)
}.
$$

First, compute $P_k$ for every $k$. Add histogram bars from left to right while maintaining a monotone increasing stack.

Suppose the current right endpoint of the prefix is $r$, and a stack entry represents a height $h$ together with the leftmost position $l$ from which $h$ remains the minimum. The area of the rectangle extending through the current right endpoint is

$$
\boxed{
h(r-l+1)
}.
$$

As a function of $r$,

$$
h(r-l+1)
=
hr+h(1-l),
$$

which is the linear function

$$
\boxed{
y=hr+h(1-l)
}.
$$

Thus, every active height in the current stack corresponds to one line that must be evaluated at the current right endpoint $r$.

When a new height $A_r$ arrives, every stack entry whose height is at least $A_r$ can no longer remain active and is popped. The new height inherits the leftmost position $l$ of those entries. This is exactly the standard monotone-stack procedure for a histogram.

Therefore, if the lines are inserted into and removed from a rollback Li Chao Tree together with the stack, we can compute

$$
E_r
=
\max_{\text{active }(h,l)}
h(r-l+1)
$$

in $O(\log N)$ time. This is the maximum rectangle that uses the current right endpoint $r$.

The maximum value for the entire prefix is

$$
\boxed{
P_{r+1}=\max(P_r,E_r)
}.
$$

Applying the same computation to the reversed array gives every suffix maximum

$$
S_k.
$$

Now consider the most important part, $C_k$.

Suppose a rectangle containing the new bar of height $x$ also contains the contiguous interval

$$
[l,r]
$$

of the original array. For the insertion cut $k$ to lie inside this rectangle, we need

$$
\boxed{
l-1\le k\le r
}.
$$

Using zero-based cuts, the same condition takes the form

$$
l\le k\le r+1.
$$

The rectangle's height is

$$
\min(x,A_l,A_{l+1},\ldots,A_r),
$$

and its width is the number $r-l+1$ of original bars plus the new bar:

$$
r-l+2.
$$

Now consider the new histogram capped at $x$ by defining

$$
\boxed{
H_i=\min(A_i,x)
}.
$$

The rectangle's height is simply

$$
\min(H_l,\ldots,H_r).
$$

Thus, computing $C_k$ is equivalent to the following problem.

> Choose a subarray $[l,r]$ of $H$, and let its minimum height be $h$. Add the following value as a candidate for every cut $k\in[l,r+1]$.

$$
h(r-l+2)
$$

For each $i$, find the maximal interval on which $H_i$ is a minimum. Let

$$
L_i,\qquad R_i
$$

be determined by the first positions to the left and right whose values are smaller than $H_i$. Then

$$
H_j\ge H_i
\qquad(L_i\le j\le R_i),
$$

and expanding the interval one more position would make it impossible to maintain height $H_i$.

Therefore, the widest rectangle using $H_i$ as its minimum has width

$$
R_i-L_i+2
$$

after including the new bar, and its area is

$$
\boxed{
V_i
=
H_i(R_i-L_i+2)
}.
$$

This candidate is valid for every insertion position inside its maximal span, so it applies throughout

$$
\boxed{
k\in[L_i,R_i+1]
}.
$$

It is also important to see why considering only maximal intervals is sufficient. Suppose $H_i$ is the minimum on some smaller subarray $[l,r]$. Since the maximal interval satisfies

$$
L_i\le l\le r\le R_i,
$$

expanding the interval keeps its minimum at least $H_i$, and every original cut $k\in[l,r+1]$ is certainly also contained in

$$
k\in[L_i,R_i+1].
$$

Hence, the smaller interval candidate

$$
H_i(r-l+2)
$$

is always dominated by the maximal interval candidate

$$
H_i(R_i-L_i+2).
$$

Consequently, for every $i$, we only need to apply a range maximum update on

$$
[L_i,R_i+1]
$$

with the value

$$
V_i=H_i(R_i-L_i+2).
$$

There is also the case in which the rectangle contains no original bar and uses only the inserted bar of height $x$. Therefore, initialize every position with

$$
C_k\ge x.
$$

The boundaries $L_i,R_i$ can be found in $O(N)$ time with a monotone stack. Applying range maximum updates to all intervals and recovering every point value can be handled by a lazy segment tree or a commutative dual segment tree in

$$
O(N\log N)
$$

time.

The prefix and suffix parts each take

$$
O(N\log N)
$$

with a rollback Li Chao Tree, and the cross part also takes

$$
O(N\log N).
$$

Thus, the total time complexity is

$$
\boxed{O(N\log N)}.
$$

Finally, for every

$$
k=0,1,\ldots,N,
$$

output

$$
\boxed{
\operatorname{Ans}_k
=
\max(P_k,S_k,C_k)
}.
$$
