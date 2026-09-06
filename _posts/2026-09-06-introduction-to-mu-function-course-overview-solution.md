---
title: Introduction to μ Function Course Overview solution
date: 2026-09-06 21:21:20 +0900
categories: [Competitive Programming, yukicoder]
tags: [number-theory, mobius-function, sieve, mathematics]
math: true
description: Solutions and key ideas for problems A through G of the yukicoder μ Function Course.
---

## A. No.3689 LCM Sum (Easy Version)

The value we need is the sum of $\operatorname{lcm}(a,b)$ over all $1\le a\le N$ and $1\le b\le M$. First, since $\operatorname{lcm}(a,b)=ab/\gcd(a,b)$, the problem becomes computing $\sum_{a=1}^{N}\sum_{b=1}^{M}ab/\gcd(a,b)$. The part that is difficult to handle directly is $1/\gcd(a,b)$, but if we rewrite it as a sum over divisors, we can reverse the order of summation. Suppose a function $f$ satisfies $1/n=\sum_{d\mid n}f(d)$. Applying Möbius inversion gives $f(n)=\sum_{d\mid n}\mu(d)/(n/d)=\frac1n\sum_{d\mid n}\mu(d)d$. Therefore, if we define $F(n)=\sum_{d\mid n}\mu(d)d$, then $1/n=\sum_{d\mid n}F(d)/d$. Applying this to $n=\gcd(a,b)$ gives $1/\gcd(a,b)=\sum_{d\mid\gcd(a,b)}F(d)/d$. The condition $d\mid\gcd(a,b)$ means that both $d\mid a$ and $d\mid b$, so the entire sum can be rewritten in the form $\sum_d \frac{F(d)}d\left(\sum_{d\mid a}a\right)\left(\sum_{d\mid b}b\right)$. The multiples of $d$ not exceeding $N$ are $d,2d,\ldots,\lfloor N/d\rfloor d$, so their sum is $d\cdot \frac{\lfloor N/d\rfloor(\lfloor N/d\rfloor+1)}2$, and the same holds on the $M$ side. Consequently, if we define $S(x)=x(x+1)/2$, the answer is

$$
\begin{aligned}
\sum_{d=1}^{\min(N,M)}
&dF(d)\,
S\!\left(\left\lfloor\frac{N}{d}\right\rfloor\right) \\
&\qquad\cdot
S\!\left(\left\lfloor\frac{M}{d}\right\rfloor\right).
\end{aligned}
$$

What remains is to compute $F(d)=\sum_{k\mid d}\mu(k)k$. In the Easy Version, $N,M\le1000$, so it is sufficient to use divisor-multiple updates that add $\mu(k)k$ to every multiple of $k$. In other words, after fixing a value of $k$, add $\mu(k)k$ to $k,2k,3k,\ldots$. Then, for each $d$, exactly the values corresponding to divisors $k$ of $d$ are accumulated, producing $F(d)$. By the harmonic-series bound, this process takes $O(K\log K)$ time, and since $K=\min(N,M)\le1000$, it is easily fast enough. The key is not to handle the LCM directly, but to use Möbius inversion to rewrite $1/\gcd$ as a divisor sum and then independently calculate the sum of the multiples of each $d$.

## B. No.3692 Calculate Mu

This problem can be solved directly from the definition of the Möbius function. Suppose the prime factorization of $N$ is $N=p_1^{e_1}p_2^{e_2}\cdots p_k^{e_k}$. If any exponent $e_i$ is at least $2$, then $N$ is divisible by the square of a prime, so $\mu(N)=0$. Conversely, if every exponent is exactly $1$, then $N$ is the product of $k$ distinct primes, so $\mu(N)=(-1)^k$. For example, $30=2\cdot3\cdot5$, so it has three distinct prime factors and $\mu(30)=-1$. On the other hand, $54=2\cdot3^3$ is divisible by $3^2$, so $\mu(54)=0$. By definition, $\mu(1)=1$. Since the constraint is $N\le10^{12}$, there is no need to compute Möbius values from $1$ through $N$; it is enough to factorize the single given value $N$. Check whether any exponent in the prime factorization is at least $2$. If not, count only the number of distinct prime factors and output $-1$ when the count is odd and $1$ when it is even. With the current library's fast factorization function, the implementation effectively consists only of iterating once over the factorization result. When $N=1$, the factorization result is empty, so leaving the initial value as $1$ handles this case without any separate exception.

## C. No.3691 Calculate Mu Sum

The required value is $M(N)=\sum_{k=1}^{N}\mu(k)$, which is called the Mertens function. Since $N\le10^7$, an ordinary Möbius sieve followed by a prefix sum is sufficient, but it is useful to understand the form used later for much larger ranges. The key is to consider $\sum_{i=1}^{n}M(\lfloor n/i\rfloor)$. Substituting the definition directly gives $\sum_{i=1}^{n}\sum_{d\le n/i}\mu(d)$. Reversing the order of summation, each $d$ appears once for every $i\le n/d$, so this becomes $\sum_{d=1}^{n}\mu(d)\lfloor n/d\rfloor$. If we interpret $\lfloor n/d\rfloor$ as the number of multiples of $d$, this is also equal to $\sum_{m=1}^{n}\sum_{d\mid m}\mu(d)$. By the fundamental identity $\sum_{d\mid m}\mu(d)=[m=1]$, the entire sum is exactly $1$. Therefore, $\sum_{i=1}^{n}M(\lfloor n/i\rfloor)=1$. Since the term for $i=1$ is $M(n)$, we obtain the recurrence $M(n)=1-\sum_{i=2}^{n}M(\lfloor n/i\rfloor)$. Iterating over every $i=2,3,\ldots,n$ directly would be slow, but the value of $\lfloor n/i\rfloor$ changes only across about $O(\sqrt n)$ intervals. At the current left endpoint $l$, let $q=\lfloor n/l\rfloor$. The last position for which the same value is maintained is $r=\lfloor n/q\rfloor$, so the entire interval $[l,r]$ can be grouped and processed as $(r-l+1)M(q)$. The library's `SumOfMobiusFunction` handles this floor-quotient grouping and the preprocessing of small values internally, so in the problem itself we only need to construct the object and obtain $M(N)$. The point of this problem is not merely to add up all Möbius values, but to recognize that grouping intervals with equal $\lfloor n/i\rfloor$ makes it possible to compute the Mertens function itself over much larger ranges.

## D. No.3690 Only One

The value requested by the problem is the sum of $\mu(d)$ over every positive common divisor $d$ of $A$ and $B$, namely $\sum_{d\mid A,\ d\mid B}\mu(d)$. The set of common divisors of $A$ and $B$ is exactly the set of divisors of $g=\gcd(A,B)$, so the expression becomes $\sum_{d\mid g}\mu(d)$. We can now directly apply the most fundamental identity of the Möbius function: $\sum_{d\mid n}\mu(d)=1$ if $n=1$, and $0$ otherwise. Therefore, the answer is $1$ when $g=1$ and $0$ when $g>1$. In other words, the entire problem reduces to determining whether $A$ and $B$ are coprime. Although the constraint is $A,B\le10^{18}$, the Euclidean algorithm computes the gcd in only $O(\log\min(A,B))$ time, so this causes no difficulty. There is no need to compute Möbius values or enumerate divisors. This problem can be viewed as the simplest form of the transformation $[\gcd(a,b)=1]=\sum_{d\mid a,\ d\mid b}\mu(d)$ used later in Coprime Count and Coprime Sum.

## E. No.3687 Coprime Count

We need to count the ordered pairs satisfying $1\le a\le N$, $1\le b\le M$, and $\gcd(a,b)=1$. Checking every pair directly takes $O(NM)$ time, and since $N,M\le10^7$, this would require examining as many as $10^{14}$ pairs, which is impossible. The key is to eliminate the coprimality condition using the Möbius function. Substituting $n=\gcd(a,b)$ into the fundamental Möbius identity gives $[\gcd(a,b)=1]=\sum_{d\mid\gcd(a,b)}\mu(d)$. Since $d\mid\gcd(a,b)$ means that both $d\mid a$ and $d\mid b$, we have $[\gcd(a,b)=1]=\sum_{d\mid a,\ d\mid b}\mu(d)$. Thus, the answer is $\sum_{a=1}^{N}\sum_{b=1}^{M}\sum_{d\mid a,\ d\mid b}\mu(d)$. If we reverse the order of summation and choose $d$ first, the expression becomes much simpler. For a fixed $d$, the value $a$ must be a multiple of $d$ not exceeding $N$, so there are $\lfloor N/d\rfloor$ choices for $a$; similarly, there are $\lfloor M/d\rfloor$ choices for $b$. Therefore, there are $\lfloor N/d\rfloor\lfloor M/d\rfloor$ ordered pairs for which $d$ is a common divisor, and each contributes $\mu(d)$. Consequently, the answer is

$$
\begin{aligned}
\sum_{d=1}^{\min(N,M)}
&\mu(d)
\left\lfloor\frac{N}{d}\right\rfloor \\
&\qquad\cdot
\left\lfloor\frac{M}{d}\right\rfloor.
\end{aligned}
$$

We now need only the Möbius values from $1$ through $\min(N,M)$, so we can preprocess them up to $10^7$ with a sieve and then make one pass over the range. The answer itself can be as large as roughly $NM$, reaching $10^{14}$, so a `long long` is required. The key idea is not to test the two-variable relation `gcd(a,b)=1` directly, but to transform the problem into one that counts by the common divisor $d$. This reduces the double loop to a single loop.

## F. No.3686 Coprime Sum

As in E, this problem considers ordered pairs satisfying $\gcd(a,b)=1$, but instead of adding $1$ for every pair, we add $ab$. Thus, the answer is $\sum_{\gcd(a,b)=1}ab$. Replacing the coprimality condition using the Möbius function gives $[\gcd(a,b)=1]=\sum_{d\mid a,\ d\mid b}\mu(d)$, so the entire sum becomes $\sum_{a=1}^{N}\sum_{b=1}^{M}ab\sum_{d\mid a,\ d\mid b}\mu(d)$. If we reverse the order of summation and choose $d$ first, this separates into the form $\sum_d\mu(d)\left(\sum_{d\mid a}a\right)\left(\sum_{d\mid b}b\right)$. In E, we needed only the number of multiples of $d$, but here we need the sum of the multiples themselves. The multiples of $d$ not exceeding $N$ are $d,2d,\ldots,\lfloor N/d\rfloor d$, so their sum is $d\cdot \frac{\lfloor N/d\rfloor(\lfloor N/d\rfloor+1)}2$. If we write $S(x)=x(x+1)/2$, this becomes $dS(\lfloor N/d\rfloor)$, and the same holds for $M$. Therefore, the final expression to compute is

$$
\begin{aligned}
\sum_{d=1}^{\min(N,M)}
&\mu(d)d^2
S\!\left(\left\lfloor\frac{N}{d}\right\rfloor\right) \\
&\qquad\cdot
S\!\left(\left\lfloor\frac{M}{d}\right\rfloor\right).
\end{aligned}
$$

Structurally, this is almost identical to E. The factor $\lfloor N/d\rfloor\lfloor M/d\rfloor$ from E is replaced by the sum of the multiples on each side, producing $d^2S(\lfloor N/d\rfloor)S(\lfloor M/d\rfloor)$. Since $N,M\le10^7$, it is sufficient to preprocess the Möbius values and then iterate once from $d=1$ through $\min(N,M)$. The answer is required modulo $998244353$, so every large product should be handled with a modular integer type.

## G. No.3688 LCM Sum

This problem is the full version of A. The mathematical expression itself is identical, but because $N,M\le3\times10^7$, we need a faster way to construct $F(d)$. As in A, begin with $\operatorname{lcm}(a,b)=ab/\gcd(a,b)$. Define $F(n)=\sum_{d\mid n}\mu(d)d$; then Möbius inversion gives $1/n=\sum_{d\mid n}F(d)/d$. Applying this to $n=\gcd(a,b)$ and reversing the order of summation reduces the answer to

$$
\begin{aligned}
K&=\min(N,M), \\
\text{answer}
&=\sum_{d=1}^{K} dF(d)\,
S\!\left(\left\lfloor\frac{N}{d}\right\rfloor\right) \\
&\qquad\cdot
S\!\left(\left\lfloor\frac{M}{d}\right\rfloor\right).
\end{aligned}
$$

In A, $K\le1000$, so it was sufficient to construct $F(d)=\sum_{k\mid d}\mu(k)k$ in $O(K\log K)$ time with divisor-multiple updates. Here, however, $K$ can be as large as $3\times10^7$, so it is better to use the properties of $F$ itself.

The function $g(n)=\mu(n)n$ is multiplicative, and since $F(n)=\sum_{d\mid n}g(d)$, the function $F$ is also multiplicative. For a prime power $p^e$, we have $\mu(p^k)=0$ for $k\ge2$, so $F(p^e)=1-p$.

Therefore, for a general integer $n=p_1^{e_1}\cdots p_k^{e_k}$, we have

$$
F(n)=\prod_{i=1}^{k}(1-p_i).
$$

The exponents of the primes do not matter at all; only the set of distinct prime factors matters. Using this property together with the least prime factor allows us to compute every $F(n)$ linearly.

Let $p=\operatorname{lpf}(n)$ and $q=n/p$. If $p\mid q$, then $q$ already contains the prime factor $p$, so multiplying by $p$ to obtain $n=pq$ does not change the set of distinct prime factors, and therefore $F(n)=F(q)$. Conversely, if $p\nmid q$, then the new prime factor $p$ is added, so $F(n)=F(q)(1-p)$.

Since $q<n$, processing $n=1,2,\ldots,K$ in increasing order guarantees that every required previous value is already available. Therefore, after obtaining the least prime factors with a linear sieve, we can compute $F(n)$ in $O(1)$ time for each $n$.

In the same loop, we can immediately add

$$
dF(d)
S\!\left(\left\lfloor\frac{N}{d}\right\rfloor\right)
S\!\left(\left\lfloor\frac{M}{d}\right\rfloor\right)
$$

to the answer. The total time complexity is $O(K)$, and the memory complexity is $O(K)$ because of the least-prime-factor array and the $F$ array.

Furthermore, since $F(n)=\prod_{p\mid n}(1-p)$, we have

$$
\lvert F(n)\rvert
=\prod_{p\mid n}(p-1)
<\prod_{p\mid n}p
\le n.
$$

Thus, throughout the range $n\le3\times10^7$, it is safe to store $F(n)$ in a 32-bit `int`. The important point in this problem is not to derive a new number-theoretic expression different from A, but to keep the same expression and use the multiplicative property of $F$ to reduce the preprocessing time from $O(K\log K)$ to $O(K)$.
