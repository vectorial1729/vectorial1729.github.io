---
title: "【LGR-299-Div.3】洛谷基础赛 #39 & IXOI Round 3 Solution"
date: 2026-09-06 22:55:00 +0900
categories: [Competitive Programming, Luogu]
tags: [luogu, ixoi, competitive-programming]
math: true
---

This post summarizes the solutions to problems A through D of 【LGR-299-Div.3】洛谷基础赛 #39 & IXOI Round 3. The code has been uploaded separately to GitHub, so I will develop the observations used by the submitted code as directly as possible through formulas.

## [A. P17413 「IXOI R3」时间复杂度分析](https://www.luogu.com.cn/problem/P17413)

The time complexities allowed by the problem are only $O(1)$, $O(n)$, and $O(n^2)$. Constants are ignored completely, and a program with complexity $O(f(n))$ is defined to perform exactly $f(n)$ operations. The number of operations that can be performed in one second is

$$
T=5\times10^8,
$$

so determining whether a time complexity passes simply amounts to checking whether the required number of operations is at most $T$.

First, consider the highest complexity, $O(n^2)$. Its acceptance condition is

$$
n^2\le T=5\times10^8.
$$

Taking the square root of both sides gives

$$
n\le\sqrt{5\times10^8}.
$$

To obtain the exact boundary for an integer $n$, observe that

$$
22360^2
=
499\,969\,600
\le
500\,000\,000,
$$

whereas

$$
22361^2
=
500\,014\,321
>
500\,000\,000.
$$

Therefore,

$$
\boxed{O(n^2)\text{ passes if and only if }n\le22360}.
$$

If this condition is not satisfied, we next consider $O(n)$. The required number of operations is exactly $n$, so it passes when

$$
n\le5\times10^8,
$$

and fails when $n$ is larger. An $O(1)$ program requires one operation, so it always passes over the entire input range of the problem.

The problem asks for the highest possible time complexity, so the final cases are

$$
\operatorname{Ans}(n)=
\begin{cases}
O(n^2), & 1\le n\le22360,\\[2mm]
O(n), & 22360<n\le5\times10^8,\\[2mm]
O(1), & n>5\times10^8.
\end{cases}
$$

Even though the input limit is $10^{18}$, the implementation performs only two integer comparisons. Both the time complexity and the additional memory complexity are $O(1)$.

## [B. P17414 「IXOI R3」贴吧 82 号](https://www.luogu.com.cn/problem/P17414)

Choosing an operation $x$ flips all bits at positions

$$
x,2x,3x,\ldots,\left\lfloor\frac{n}{x}\right\rfloor x.
$$

If the same $x$ is used twice, all of those positions are flipped exactly twice and return to their original values. Therefore, for every $x$, only the **parity** of the number of operations matters. We may assume that an optimal solution uses each $x$ either zero times or once.

Define whether operation $x$ is used by

$$
t_x\in\{0,1\}.
$$

Here, $t_x=1$ means that $x$ is used once, while $t_x=0$ means that it is not used.

Let the $i$-th character of the initial string be $s_i\in\{0,1\}$. Since its final value must be one, define the parity with which position $i$ must be flipped as

$$
b_i=s_i\oplus1.
$$

In other words,

$$
b_i=
\begin{cases}
1, & s_i=0,\\
0, & s_i=1.
\end{cases}
$$

Which operations flip position $i$? Operation $x$ affects position $i$ exactly when

$$
x\mid i.
$$

Therefore, the parity of the number of times position $i$ is actually flipped is

$$
\bigoplus_{d\mid i}t_d,
$$

and this must equal $b_i$. Thus, for every $i$, we require

$$
\boxed{
b_i=\bigoplus_{d\mid i}t_d
}
\tag{1}
$$

to hold.

The structure is already visible if we solve these equations directly in increasing order of $i$. When $i=1$, its only divisor is one, so

$$
b_1=t_1,
\qquad
t_1=b_1.
$$

For $i=2$,

$$
b_2=t_1\oplus t_2,
$$

and hence

$$
t_2=b_2\oplus t_1.
$$

For a general $i$,

$$
b_i
=
t_i
\oplus
\bigoplus_{\substack{d\mid i\\d<i}}t_d,
$$

so

$$
\boxed{
t_i
=
b_i
\oplus
\bigoplus_{\substack{d\mid i\\d<i}}t_d
}
\tag{2}
$$

follows. Therefore, $t_1,t_2,\ldots,t_n$ are determined **uniquely** in order. There are not several possible selections of operations; there is exactly one parity solution, and the minimum number of operations is the number of indices for which $t_i=1$ in this unique solution.

The same argument becomes more concise from the perspective of divisor transforms. Over the field $\mathbb F_2$, where XOR plays the role of addition, equation (1) can be viewed as the Dirichlet convolution

$$
b=\mathbf 1*t.
$$

Here,

$$
\mathbf 1(n)=1,
$$

and convolution is understood as

$$
(f*g)(n)
=
\bigoplus_{d\mid n}
f(d)g(n/d).
$$

The ordinary Möbius function $\mu$ satisfies

$$
\sum_{d\mid n}\mu(d)
=
[n=1],
$$

so it is the convolution inverse of $\mathbf 1$. Over $\mathbb F_2$,

$$
-1\equiv1\pmod2,
$$

so the sign of $\mu(n)$ disappears and only

$$
\mu(n)\bmod2
=
\begin{cases}
1, & n\text{ is square-free},\\
0, & \text{otherwise}
\end{cases}
$$

remains. Applying the Möbius transform to both sides therefore gives

$$
t=\mu*b,
$$

and each term is

$$
\boxed{
t_n
=
\bigoplus_{d\mid n}
(\mu(n/d)\bmod2)\,b_d
}
\tag{3}
$$

or, more directly,

$$
\boxed{
t_n
=
\bigoplus_{\substack{d\mid n\\n/d\text{ square-free}}}
b_d
}.
\tag{4}
$$

The submitted code constructs

$$
a_i=[s_i=0]=b_i
$$

and then applies `divisor_transform::mobius` using XOR as the operation. The transformed array is exactly $t_i$, so the answer is

$$
\boxed{
\operatorname{Ans}
=
\sum_{i=1}^{n}t_i
}.
$$

The library's divisor Möbius transform can be implemented by updating multiples for each prime $p$. It touches approximately $n/p$ elements for each prime, so the total amount of work is

$$
\sum_{p\le n}\frac{n}{p}
=
n\sum_{p\le n}\frac{1}{p}
=
O(n\log\log n).
$$

This is easily fast enough for $n\le10^5$.

## [C. P17415 「IXOI R3」帮助她玩游戏](https://www.luogu.com.cn/problem/P17415)

Let the current number of game coins be $v$. When we encounter an ordinary exchange machine $(a,b)$, it cannot be used if $v<a$, so

$$
v'=v.
$$

If $v\ge a$, it must be used, giving

$$
v'
=
v-a+b
=
v+(b-a).
$$

Therefore, if we define

$$
\Delta=b-a,
$$

the transition of an ordinary exchange machine is

$$
\boxed{
T(v)=
\begin{cases}
v, & v<a,\\
v+\Delta, & v\ge a.
\end{cases}
}
\tag{1}
$$

For a special exchange machine $(a,b,c)$, we likewise cannot do anything when $v<a$. When $v\ge a$, however, we may either choose not to use it or use it. Because the probabilities of obtaining either $b$ or $c$ after using it are both positive, if we only consider reachable values and define

$$
\Delta_1=b-a,
\qquad
\Delta_2=c-a,
$$

the transition is

$$
\boxed{
T(v)=
\begin{cases}
\{v\}, & v<a,\\
\{v,\ v+\Delta_1,\ v+\Delta_2\}, & v\ge a.
\end{cases}
}
\tag{2}
$$

The problem is to maintain the set of all possible values $v$ after passing each machine. A conventional bitset DP over a state range of size $V$ would require $O(V/64)$ work for each machine, giving

$$
O\left(n\frac{V}{64}\right),
$$

which is too large. The decisive constraint in this problem is

$$
a_i\le20.
$$

Let the maximum requirement among all machines be

$$
L=\max_i a_i.
$$

Then

$$
L\le20.
$$

If the current number of game coins satisfies

$$
v\ge L,
$$

then, for every machine $i$,

$$
v\ge L\ge a_i.
$$

Thus, no later machine can ever become unusable because we do not have enough coins. This suggests dividing the state space into

$$
\underbrace{\{1,2,\ldots,L-1\}}_{\text{low}}
\qquad\text{and}\qquad
\underbrace{\{L,L+1,\ldots\}}_{\text{high}}.
$$

Transitions become much simpler in the high region.

First, establish an absolute upper bound on the reachable values. For an ordinary machine $i$, the maximum possible increase is

$$
g_i=\max(0,b_i-a_i),
$$

while for a special machine it is

$$
g_i=\max(0,b_i-a_i,c_i-a_i).
$$

Therefore, no matter which choices are made, the final value cannot exceed

$$
\boxed{
v
\le
V_{\max}
=
X+\sum_{i=1}^{N}g_i
}.
\tag{3}
$$

The actual code computes this value as well.

Now let the set of reachable high values be $H\subseteq[L,V_{\max}]$. Every high value $v$ can use an ordinary machine, so only the second case of (1) applies, giving

$$
\boxed{
H'=H+\Delta
=
\{v+\Delta\mid v\in H\}
}.
\tag{4}
$$

In other words, the entire set is translated by the same amount.

Instead of actually performing a bitset shift for every such translation, we represent it with a lazy offset. Let the bitset length be $M$, chosen as a multiple of 64 satisfying $M>V_{\max}$. Store a logical value $v$ at bitset position

$$
\boxed{
p(v)=(v-o)\bmod M
}.
\tag{5}
$$

Here, $o$ is a global offset.

When an ordinary machine must increase every high value by $\Delta$, it is enough to set

$$
o'\equiv o+\Delta\pmod M.
$$

No bit in the actual bitset moves, but the logical value represented by the same stored position $p$ becomes

$$
v'=p+o'
\equiv
(p+o)+\Delta
\equiv
v+\Delta
\pmod M.
$$

Therefore, the entire shift of the high set is processed in $O(1)$ time using only

$$
\boxed{
o\leftarrow o+\Delta\pmod M
}.
\tag{6}
$$

We must also verify that using modulo $M$ does not cause states to collide. Every value that can actually occur lies in the range

$$
1\le v\le V_{\max}<M.
$$

For two distinct actual values $u\ne v$ to be stored at the same bitset position, we would need

$$
u-o\equiv v-o\pmod M,
$$

or equivalently

$$
u\equiv v\pmod M.
$$

However,

$$
\lvert u-v\rvert<M,
$$

so this is impossible. Thus, the circular representation is injective over the actual reachable values.

One complication is that $\Delta$ for an ordinary machine may be negative. A state that was originally high, with $v\ge L$, may move down to

$$
v'=v+\Delta<L.
$$

It then no longer belongs to the region where every machine is guaranteed to be usable. Therefore, after updating the offset, for each $1\le u<L$ we check whether position

$$
p(u)=(u-o)\bmod M
$$

is set. If it is, we clear it from the high bitset and move it to the low mask. The number of values that must be checked is only

$$
L-1\le19,
$$

so this process takes $O(L)$ time.

The low region contains at most 19 possible values, so a single bitmask is sufficient. For an ordinary machine, a low state $v$ is updated directly as

$$
v<a\Rightarrow v,
$$

and

$$
v\ge a\Rightarrow v+\Delta.
$$

If the result is at least $L$, set the high-bitset position

$$
p(v+\Delta)=(v+\Delta-o)\bmod M.
$$

For a special machine, every high state uses the second case of (2), so

$$
\boxed{
H'
=
H
\cup(H+\Delta_1)
\cup(H+\Delta_2)
}.
\tag{7}
$$

This is the union of the original set and two shifted copies rather than a single translation, so it cannot be handled using only the offset. An actual bitset shift-or is required. However, the number of special machines is only

$$
k\le30.
$$

Therefore, the expensive operation that scans the full bitset of length $M$ occurs at most 30 times.

Divide the bitset into 64-bit words and consider a cyclic shift by

$$
d=64q+r,
\qquad
0\le r<64.
$$

If the original value of word $i$ is $x$, the part

$$
x\ll r
$$

goes into word $i+q$, while the part

$$
x\gg(64-r)
$$

goes into the next word. Treating the indices modulo the word count produces a circular shift. The code's `shift_or` performs exactly this operation.

For a low state at a special machine, the option not to use the machine preserves the existing state. When $v\ge a$, it additionally creates

$$
v+\Delta_1,
\qquad
v+\Delta_2.
$$

Values among these that are at least $L$ are sent to the high set. States produced by the high-set shifts that fall below $L$ are moved back to the low set by `pull`.

Consequently, if we define the state as

$$
R_i
=
\{\text{numbers of game coins reachable after processing machine }i\},
$$

it is maintained exactly. The invariant of the low/high split is

$$
R_i
=
R_i^{\text{low}}
\sqcup
R_i^{\text{high}},
$$

with

$$
R_i^{\text{low}}\subseteq[1,L),
\qquad
R_i^{\text{high}}\subseteq[L,V_{\max}].
$$

The high set is represented by the offset encoding in (5).

For a final query $y$, if

$$
y>V_{\max},
$$

it is immediately impossible by (3). Otherwise, if $y<L$, check bit $y$ of the low mask. If $y\ge L$, check the high-bitset position

$$
p(y)=(y-o)\bmod M.
$$

In terms of total time complexity, an ordinary machine takes $O(L)$ time because of low-state processing and pulling. A special machine additionally requires a bitset shift in $O(M/64)$ time. Therefore, the total complexity is

$$
\boxed{
O\left(NL+k\frac{M}{64}+Q\right)
}.
$$

Since

$$
L\le20,
\qquad
k\le30,
$$

this is fast enough.

## [D. P17416 「IXOI R3」我才不玩原神呢](https://www.luogu.com.cn/problem/P17416)

For a subsequence $B=(b_1,\ldots,b_K)$ of length $K$, let its maximum be

$$
m=\max B.
$$

Its weight is

$$
W(B)
=
\sum_{j=1}^{K}(b_j\oplus m).
$$

The term corresponding to $m$ itself is

$$
m\oplus m=0.
$$

Therefore, after fixing one maximum value $m$, we only need to maximize the contribution

$$
\sum(a_j\oplus m)
$$

from the remaining $K-1$ elements.

Sort the array in increasing order as

$$
A_0\le A_1\le\cdots\le A_{N-1}.
$$

Let $A_i=x$ be the designated occurrence of the maximum in the selected subsequence. Every other element is at most $x$, so considering only elements before $i$ in the sorted order represents every possible case.

Repeated values do not cause a problem. If the maximum in an optimal subsequence is $x$ and several occurrences of $x$ are selected, choose the selected occurrence with the largest sorted index as $i$. Every other selected element then lies exactly in the prefix

$$
[0,i).
$$

Therefore, for every $i\ge K-1$, define

$$
\boxed{
F_i
=
\max_{\substack{S\subseteq\{0,\ldots,i-1\}\\\lvert S\rvert=K-1}}
\sum_{j\in S}(A_j\oplus A_i)
}
\tag{1}
$$

and the overall answer is

$$
\boxed{
\operatorname{Ans}
=
\max_{K-1\le i<N}F_i
}.
\tag{2}
$$

If $K=1$, the only selected element is the maximum itself, so

$$
W=x\oplus x=0,
$$

and the answer is immediately zero.

For a fixed $x=A_i$, we now need to select the largest $K-1$ values among

$$
A_0\oplus x,\ A_1\oplus x,\ldots,A_{i-1}\oplus x.
$$

The comparison of two XOR values is determined by their highest differing bit. Consider two candidates $u$ and $v$, and let

$$
b=\max\{t\mid u_t\ne v_t\}
$$

be the highest bit at which they differ. All bits higher than $b$ are equal, so

$$
(u\oplus x)_t=(v\oplus x)_t
\qquad(t>b).
$$

Therefore, their order is determined solely by bit $b$. If

$$
u_b\ne x_b,
\qquad
v_b=x_b,
$$

then

$$
(u\oplus x)_b=1,
\qquad
(v\oplus x)_b=0,
$$

and hence

$$
\boxed{
u\oplus x>v\oplus x
}.
\tag{3}
$$

This is why a query for $x$ in a binary trie always prioritizes the branch opposite to the current bit of $x$.

Instead of a conventional full trie with 30 levels, the submitted code constructs a compressed binary trie whose nodes represent contiguous intervals of the sorted array. Suppose a node represents the interval

$$
[l,r).
$$

If

$$
A_l=A_{r-1},
$$

then every value in the interval is equal, so the node is a leaf.

Otherwise,

$$
A_l\oplus A_{r-1}\ne0,
$$

and we define the highest bit at which the two endpoints differ as

$$
\boxed{
b=\operatorname{msb}(A_l\oplus A_{r-1})
}.
\tag{4}
$$

Every value in the sorted interval has the same bits above $b$. Thus, the values whose bit $b$ is zero come first, followed by those whose bit $b$ is one.

Define the common higher-bit prefix as

$$
\operatorname{base}
=
A_l\mathbin{\&}\sim(2^{b+1}-1).
$$

The first possible value whose bit $b$ is one is at the boundary

$$
\operatorname{cut}
=
\operatorname{base}+2^b.
$$

Therefore, setting

$$
m
=
\operatorname{lower\_bound}(A[l,r),\operatorname{cut})
$$

splits the node into the two children

$$
[l,m),
\qquad
[m,r).
$$

At a node whose split bit is $b$, if $x_b=0$, the one-child is the preferred branch; if $x_b=1$, the zero-child is preferred. Call this branch $P$ and the other branch $Q$. By (3),

$$
\boxed{
\forall u\in P,\ \forall v\in Q,
\qquad
u\oplus x>v\oplus x
}.
\tag{5}
$$

Suppose we still need to select $r$ elements and the preferred branch contains $c$ elements that are actually available within the prefix $[0,i)$. The next greedy choice is forced.

If

$$
c\ge r,
$$

then all of the largest $r$ values lie inside $P$, so the correct action is

$$
\boxed{\text{descend into }P\text{ and continue searching for }r\text{ elements}}.
$$

Conversely, if

$$
c<r,
$$

then, by (5), all $c$ elements in $P$ have larger XOR values than every element in $Q$. Therefore, we are forced to

$$
\boxed{
\text{select all }c\text{ elements of }P
\text{ and find }r-c\text{ elements in }Q
}.
$$

Repeating this process down to the lower bits selects exactly the largest $K-1$ XOR values.

It is important not to enumerate the selected elements one by one. Whenever an entire preferred branch is selected, that branch is a contiguous interval of the sorted array,

$$
[l,r).
$$

The trie depth is at most 30, so at most about 30 branches are selected completely during one query. The final selected set can therefore be represented as the union of contiguous intervals

$$
[l_1,r_1),[l_2,r_2),\ldots,[l_t,r_t),
\qquad
t=O(30).
$$

For one selected interval $[l,r)$, we now need to compute

$$
X(l,r;x)
=
\sum_{j=l}^{r-1}(A_j\oplus x)
$$

quickly. The code uses the identity

$$
\boxed{
u\oplus x
=
u+x-2(u\mathbin{\&}x)
}.
\tag{6}
$$

Therefore,

$$
X(l,r;x)
=
\sum_{j=l}^{r-1}A_j
+
(r-l)x
-
2\sum_{j=l}^{r-1}(A_j\mathbin{\&}x).
\tag{7}
$$

Construct an ordinary prefix sum for the first term:

$$
S_i=\sum_{j=0}^{i-1}A_j.
$$

Then

$$
\sum_{j=l}^{r-1}A_j
=
S_r-S_l.
\tag{8}
$$

The AND sum can be separated by bit. Let $x_b$ be bit $b$ of $x$, and let the number of elements in the interval whose bit $b$ is one be

$$
C_b(l,r)
=
\#\{j\in[l,r)\mid(A_j)_b=1\}.
$$

Since

$$
A_j\mathbin{\&}x
=
\sum_{b=0}^{29}
2^b(A_j)_b x_b,
$$

we have

$$
\sum_{j=l}^{r-1}(A_j\mathbin{\&}x)
=
\sum_{b=0}^{29}
2^b x_b C_b(l,r).
\tag{9}
$$

Precompute a prefix count for every bit:

$$
P_{i,b}
=
\sum_{j=0}^{i-1}(A_j)_b.
$$

Then

$$
C_b(l,r)
=
P_{r,b}-P_{l,b}.
\tag{10}
$$

Substituting (8), (9), and (10) into (7) gives

$$
\boxed{
X(l,r;x)
=
(S_r-S_l)
+
(r-l)x
-
2\sum_{\substack{0\le b<30\\x_b=1}}
2^b(P_{r,b}-P_{l,b})
}.
\tag{11}
$$

The code's `range_xor_sum` implements exactly this formula.

Writing the same expression directly in terms of the contribution of each XOR bit gives

$$
X(l,r;x)
=
\sum_{b=0}^{29}2^b
\begin{cases}
C_b(l,r), & x_b=0,\\
(r-l)-C_b(l,r), & x_b=1
\end{cases}
\tag{12}
$$

which is exactly equivalent to (11).

For every $i$, a query descends through at most $B=30$ levels of the compressed trie. The selected result consists of at most $O(B)$ intervals, and computing the XOR sum of one interval checks at most $B$ bits, taking $O(B)$ time. Therefore, one query takes

$$
O(B^2),
$$

and the overall time complexity is

$$
\boxed{
O(N\log N+NB^2)
}
\qquad(B=30).
$$

Since $B$ is a fixed constant, this is fast enough for $N\le10^5$. The bit prefix counts store

$$
(N+1)\times30
$$

integers, so the memory complexity is

$$
O(NB).
$$
