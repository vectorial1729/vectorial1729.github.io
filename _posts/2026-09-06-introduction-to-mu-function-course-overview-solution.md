---
title: Introduction to μ Function Course Overview solution
date: 2026-09-06 21:21:20 +0900
categories: [Competitive Programming, yukicoder]
tags: [number-theory, mobius-function, sieve, mathematics]
math: true
description: Solutions and key ideas for problems A through G of the yukicoder μ Function Course.
---

This post covers the main ideas behind problems A through G of the yukicoder **μ Function Course**. The central technique is to replace conditions involving divisibility or greatest common divisors with divisor sums using the Möbius function.

## A. No.3689 LCM Sum (Easy Version)

We need to compute the sum of the least common multiples over every pair satisfying $1 \le a \le N$ and $1 \le b \le M$.

$$
\operatorname{lcm}(a,b)=\frac{ab}{\gcd(a,b)}
$$

The difficult part is the reciprocal of the gcd. Let $f$ satisfy

$$
\frac{1}{n}=\sum_{d\mid n}f(d).
$$

By Möbius inversion,

$$
f(n)
=\sum_{d\mid n}\frac{\mu(d)}{n/d}
=\frac{1}{n}\sum_{d\mid n}\mu(d)d.
$$

Define

$$
F(n)=\sum_{d\mid n}\mu(d)d.
$$

Then

$$
\frac{1}{n}=\sum_{d\mid n}\frac{F(d)}{d},
$$

and therefore

$$
\frac{1}{\gcd(a,b)}
=\sum_{d\mid\gcd(a,b)}\frac{F(d)}{d}.
$$

Because $d\mid\gcd(a,b)$ is equivalent to $d\mid a$ and $d\mid b$, we may reverse the order of summation. Let

$$
S(x)=\frac{x(x+1)}{2}.
$$

The sum of all multiples of $d$ not exceeding $N$ is

$$
\sum_{\substack{1\le a\le N\\d\mid a}}a
=dS\!\left(\left\lfloor\frac{N}{d}\right\rfloor\right).
$$

Thus the answer is

$$
\boxed{
\sum_{d=1}^{\min(N,M)}
dF(d)
S\!\left(\left\lfloor\frac{N}{d}\right\rfloor\right)
S\!\left(\left\lfloor\frac{M}{d}\right\rfloor\right)
}.
$$

For the easy version, $N,M\le 1000$. We can compute

$$
F(d)=\sum_{k\mid d}\mu(k)k
$$

by adding $\mu(k)k$ to every multiple of $k$. This takes $O(K\log K)$ time, where $K=\min(N,M)$.

## B. No.3692 Calculate Mu

Let

$$
N=p_1^{e_1}p_2^{e_2}\cdots p_k^{e_k}.
$$

The Möbius function is

$$
\mu(N)=
\begin{cases}
1, & N=1,\\
0, & \exists i:\ e_i\ge 2,\\
(-1)^k, & e_1=e_2=\cdots=e_k=1.
\end{cases}
$$

For example,

$$
30=2\cdot3\cdot5
\quad\Longrightarrow\quad
\mu(30)=-1,
$$

while

$$
54=2\cdot3^3
\quad\Longrightarrow\quad
\mu(54)=0.
$$

Since $N\le 10^{12}$, only one integer needs to be factorized. If any prime exponent is at least two, the answer is zero. Otherwise, the answer is determined by the parity of the number of distinct prime factors. The empty factorization of $N=1$ naturally gives $\mu(1)=1$.

## C. No.3691 Calculate Mu Sum

Define the Mertens function

$$
M(N)=\sum_{k=1}^{N}\mu(k).
$$

Consider

$$
\sum_{i=1}^{n}M\!\left(\left\lfloor\frac{n}{i}\right\rfloor\right).
$$

Expanding the definition and reversing the order of summation gives

$$
\begin{aligned}
\sum_{i=1}^{n}M\!\left(\left\lfloor\frac{n}{i}\right\rfloor\right)
&=\sum_{i=1}^{n}\sum_{d\le n/i}\mu(d)\\
&=\sum_{d=1}^{n}\mu(d)\left\lfloor\frac{n}{d}\right\rfloor\\
&=\sum_{m=1}^{n}\sum_{d\mid m}\mu(d)\\
&=1.
\end{aligned}
$$

Therefore,

$$
M(n)=1-\sum_{i=2}^{n}M\!\left(\left\lfloor\frac{n}{i}\right\rfloor\right).
$$

The quotient $\lfloor n/i\rfloor$ is constant over intervals. If

$$
q=\left\lfloor\frac{n}{l}\right\rfloor,
$$

then the largest index with the same quotient is

$$
r=\left\lfloor\frac{n}{q}\right\rfloor.
$$

Hence the interval $[l,r]$ contributes

$$
(r-l+1)M(q).
$$

Grouping equal floor quotients reduces the number of transitions to about $O(\sqrt n)$ per distinct state. For $N\le 10^7$, a direct Möbius sieve and prefix sum also work, but this recurrence remains useful for much larger values.

## D. No.3690 Only One

The required value is

$$
\sum_{\substack{d\mid A\\d\mid B}}\mu(d).
$$

Let $g=\gcd(A,B)$. The common divisors of $A$ and $B$ are exactly the divisors of $g$, so

$$
\sum_{\substack{d\mid A\\d\mid B}}\mu(d)
=\sum_{d\mid g}\mu(d)
=
\begin{cases}
1, & g=1,\\
0, & g>1.
\end{cases}
$$

The problem is equivalent to checking whether $A$ and $B$ are coprime. Euclid's algorithm runs in

$$
O\!\left(\log\min(A,B)\right),
$$

so the limit $A,B\le 10^{18}$ is not an issue.

## E. No.3687 Coprime Count

We need to count the ordered pairs $(a,b)$ satisfying

$$
1\le a\le N,
\qquad
1\le b\le M,
\qquad
\gcd(a,b)=1.
$$

Use the identity

$$
[\gcd(a,b)=1]
=\sum_{\substack{d\mid a\\d\mid b}}\mu(d).
$$

Then

$$
\begin{aligned}
\sum_{a=1}^{N}\sum_{b=1}^{M}[\gcd(a,b)=1]
&=\sum_{a=1}^{N}\sum_{b=1}^{M}
\sum_{\substack{d\mid a\\d\mid b}}\mu(d)\\
&=\sum_{d=1}^{\min(N,M)}
\mu(d)
\left\lfloor\frac{N}{d}\right\rfloor
\left\lfloor\frac{M}{d}\right\rfloor.
\end{aligned}
$$

Thus the answer is

$$
\boxed{
\sum_{d=1}^{\min(N,M)}
\mu(d)
\left\lfloor\frac{N}{d}\right\rfloor
\left\lfloor\frac{M}{d}\right\rfloor
}.
$$

We only need the values of $\mu(d)$ up to $\min(N,M)$. A sieve followed by one linear pass is sufficient for $N,M\le 10^7$. The answer can be as large as $NM$, so a 64-bit integer is required.

## F. No.3686 Coprime Sum

This problem uses the same coprimality condition as E, but each valid pair contributes $ab$ instead of $1$:

$$
\sum_{\substack{1\le a\le N\\1\le b\le M\\\gcd(a,b)=1}}ab.
$$

Applying Möbius inversion,

$$
\begin{aligned}
\sum_{\gcd(a,b)=1}ab
&=\sum_{a=1}^{N}\sum_{b=1}^{M}
ab\sum_{\substack{d\mid a\\d\mid b}}\mu(d)\\
&=\sum_{d=1}^{\min(N,M)}\mu(d)
\left(\sum_{\substack{1\le a\le N\\d\mid a}}a\right)
\left(\sum_{\substack{1\le b\le M\\d\mid b}}b\right).
\end{aligned}
$$

Since

$$
\sum_{\substack{1\le a\le N\\d\mid a}}a
=dS\!\left(\left\lfloor\frac{N}{d}\right\rfloor\right),
$$

the answer becomes

$$
\boxed{
\sum_{d=1}^{\min(N,M)}
\mu(d)d^2
S\!\left(\left\lfloor\frac{N}{d}\right\rfloor\right)
S\!\left(\left\lfloor\frac{M}{d}\right\rfloor\right)
}.
$$

After sieving the Möbius values, the summation takes $O(\min(N,M))$ time. All arithmetic should be performed modulo $998244353$.

## G. No.3688 LCM Sum

The mathematical formula is identical to A:

$$
\boxed{
\sum_{d=1}^{K}
dF(d)
S\!\left(\left\lfloor\frac{N}{d}\right\rfloor\right)
S\!\left(\left\lfloor\frac{M}{d}\right\rfloor\right)
},
\qquad
K=\min(N,M),
$$

where

$$
F(n)=\sum_{d\mid n}\mu(d)d.
$$

However, $N,M\le 3\times 10^7$, so the $O(K\log K)$ divisor-update method from A is too slow. Define

$$
g(n)=\mu(n)n.
$$

The function $g$ is multiplicative, and

$$
F(n)=\sum_{d\mid n}g(d),
$$

so $F$ is also multiplicative. For every prime power,

$$
F(p^e)=1-p.
$$

Therefore, if

$$
n=p_1^{e_1}p_2^{e_2}\cdots p_k^{e_k},
$$

then

$$
F(n)=\prod_{i=1}^{k}(1-p_i).
$$

Only the distinct prime factors matter. Let

$$
p=\operatorname{lpf}(n),
\qquad
q=\frac{n}{p}.
$$

Then

$$
F(n)=
\begin{cases}
F(q), & p\mid q,\\
F(q)(1-p), & p\nmid q.
\end{cases}
$$

Because $q<n$, all required values are already available when processing the integers in increasing order. A linear sieve computes the least prime factor and $F(n)$ in $O(1)$ amortized work per integer, giving total complexity

$$
O(K)
$$

with $O(K)$ memory.

Finally,

$$
|F(n)|
=\prod_{p\mid n}(p-1)
<\prod_{p\mid n}p
\le n,
$$

so $F(n)$ safely fits in a 32-bit signed integer throughout the required range.
