---
title: BNPCHS 2026 Final Round Mirror (Rated Div. 2) Solution
date: 2026-09-06 22:40:00 +0900
categories: [Competitive Programming, TLX]
tags: [tlx, bnpchs, competitive-programming]
math: true
---

This post summarizes the solutions to problems A through F of BNPCHS 2026 Final Round Mirror (Rated Div. 2). The code has been uploaded separately to GitHub, so I will focus here on why the observations and formulas used by the submitted code are valid.

## A. Le Flection

Let the operation of placing a string into the mirror once be denoted by $F(T)=T+\operatorname{rev}(T)$. If this operation has been performed at least once, the resulting string must be an even-length palindrome. Indeed, if $P=F(T)$, then $\operatorname{rev}(P)=\operatorname{rev}(\operatorname{rev}(T))+\operatorname{rev}(T)=T+\operatorname{rev}(T)=P$, so $P$ is always a palindrome. Therefore, if the given string $S$ has odd length or is not a palindrome, it cannot be created even with one operation, and the answer is immediately $0$.

Now suppose that $S$ is an even-length palindrome. Then at least one operation is possible. If $T$ is the first half of $S$, the palindrome property implies that the second half is $\operatorname{rev}(T)$, so $S=T+\operatorname{rev}(T)=F(T)$. The remaining question is whether the string could have passed through the mirror two or more times.

Let $P$ be the result of the first operation. Since $P$ is already a palindrome, the second operation gives

$$
F(P)=P+\operatorname{rev}(P)=P+P.
$$

In other words, from the second operation onward, the operation simply concatenates the previous string with itself. The same applies to the third operation because $PP$ is itself a palindrome:

$$
F(PP)=PPPP.
$$

In general, the result after $k\ge1$ operations is the palindrome $P$ obtained immediately after the first operation, repeated $2^{k-1}$ times.

Therefore, if the current length is $L$, going back by one more operation requires the first and second halves of the current string to be exactly equal. That is,

$$
S[0,\tfrac{L}{2})=S[\tfrac{L}{2},L)
$$

must hold. The preceding string has length $L/2$, and it must itself be the result of at least one mirror operation, so its length must be even. Thus, the length condition for going back one additional step is

$$
\frac{L}{2}\equiv0\pmod 2,
\qquad\text{that is},\qquad
L\equiv0\pmod 4.
$$

This is why the submitted code first checks whether the entire string $S$ is an even-length palindrome, initializes the answer to $1$, and then compares the two halves while the current length $L$ is divisible by $4$. If the halves are equal, it replaces $L$ with $L/2$ and increments the answer by $1$.

For example, `aaaaaaaa` has length $8$ and is a palindrome, so one operation is possible. At length $8$, `aaaa` and `aaaa` are equal, so a second operation is possible and $L$ becomes $4$. Again, `aa` and `aa` are equal, so a third operation is possible and $L$ becomes $2$. Now $2\not\equiv0\pmod4$, so the preceding result can no longer be an even-length string that has passed through the mirror at least once. Therefore, the maximum is $3$.

The total length of all strings compared across the steps is $N+N/2+N/4+\cdots=O(N)$, so the overall time complexity is $O(\lvert S\rvert)$.

## B. The Delicateness of Dr. Zsoka

The value obtained by pairing two concoctions $i$ and $j$ is

$$
d(i,j)=\max(\lvert A_i-A_j\rvert,\lvert B_i-B_j\rvert).
$$

Rather than handling this $L_\infty$ distance directly, the key is to transform the coordinates as

$$
X_i=A_i+B_i,
\qquad
Y_i=A_i-B_i.
$$

For any two real numbers $u$ and $v$, the following identity holds:

$$
\max(\lvert u\rvert,\lvert v\rvert)
=
\frac{\lvert u+v\rvert+\lvert u-v\rvert}{2}.
$$

Substituting $u=A_i-A_j$ and $v=B_i-B_j$ gives

$$
\boxed{
d(i,j)
=
\frac{\lvert X_i-X_j\rvert+\lvert Y_i-Y_j\rvert}{2}
}.
$$

Therefore, the total delicateness over all pairs is

$$
\frac{1}{2}
\left(
\sum_{(i,j)}\lvert X_i-X_j\rvert
+
\sum_{(i,j)}\lvert Y_i-Y_j\rvert
\right).
$$

Thus, if we construct a perfect matching that maximizes the sum of absolute differences in the $X$ coordinates and simultaneously maximizes it in the $Y$ coordinates, the total answer is also maximized.

First, consider the one-dimensional problem. Suppose there are $N=2m$ values sorted as $z_1\le z_2\le\cdots\le z_{2m}$, and we want to pair them to maximize $\sum\lvert z_i-z_j\rvert$. Once we designate the smaller and larger value in every pair, the sum becomes

$$
\sum z_{\text{large}}-\sum z_{\text{small}}.
$$

There are exactly $m$ values on the larger side. Their sum is maximized by selecting the largest $m$ values, while the sum on the smaller side is minimized by selecting the smallest $m$ values. Therefore, the maximum is

$$
\sum_{k=m+1}^{2m}z_k-\sum_{k=1}^{m}z_k.
$$

To attain this value, it is sufficient for every pair to contain one value from the lower half and one from the upper half. The exact correspondence between elements of the two halves does not matter.

For every point $i$, define the bit $s_x(i)\in\{0,1\}$ to indicate whether $X_i$ belongs to the upper half in the ordering by $X$, and define $s_y(i)\in\{0,1\}$ analogously for the ordering by $Y$. Every point then belongs to one of the four groups

$$
(s_x,s_y)\in\{(0,0),(0,1),(1,0),(1,1)\}.
$$

For a pair to maximize the total $X$ distance in the one-dimensional sense, its two points must have different values of $s_x$. To maximize the $Y$ distance as well, their values of $s_y$ must also differ. Therefore, we should pair the opposite groups as

$$
(0,0)\leftrightarrow(1,1),
\qquad
(0,1)\leftrightarrow(1,0).
$$

The required group sizes are automatically equal. Let the group sizes be $n_{00},n_{01},n_{10},n_{11}$. Since the upper half by $X$ contains exactly $m$ points,

$$
n_{10}+n_{11}=m.
$$

Likewise, the upper half by $Y$ contains exactly $m$ points, so

$$
n_{01}+n_{11}=m.
$$

Therefore, $n_{10}=n_{01}$. Comparing the lower-half counts in the same way also gives

$$
n_{00}=n_{11}.
$$

Consequently, the two pairs of opposite groups can be matched one-to-one in any order.

In this matching, every pair contains one lower-half and one upper-half element with respect to $X$, so $\sum\lvert X_i-X_j\rvert$ attains its maximum possible value. At the same time, every pair also contains one lower-half and one upper-half element with respect to $Y$, so $\sum\lvert Y_i-Y_j\rvert$ is also maximized. Therefore, the original sum of $L_\infty$ distances is maximized.

For the implementation, sort the points independently by $X=A+B$ and $Y=A-B$, mark whether each point belongs to each upper half, divide the points into four groups, and output pairs from opposite groups in order. Sorting dominates the running time, so the time complexity is $O(N\log N)$.

## C. Soggy Sushi

Because OR and AND operate independently on each bit, it is enough to consider one bit at a time. At some point in the process, let the state of one bit be the binary array $b_1,b_2,\ldots,b_N$. The prefix OR operation is

$$
b'_i=b_1\lor b_2\lor\cdots\lor b_i,
$$

while the prefix AND operation is

$$
b'_i=b_1\land b_2\land\cdots\land b_i.
$$

The state may appear complicated for an arbitrary bit array, but the key observation is that, after either OR or AND has been performed once, the set of positions containing a one is always a single interval.

Suppose prefix OR is performed once. Let $l$ be the first position containing a one in the original array. For $i<l$, the prefix contains no one, so the result is zero. For $i\ge l$, the prefix contains at least one one, so the result is one. Therefore, the positions containing a one form the interval

$$
[l,N-1].
$$

If the original array contains no ones, the set is empty. Conversely, after prefix AND is performed, a one remains only until the first zero in the original array, so the positions containing a one form an interval of the shape

$$
[0,r].
$$

Thus, after the first occurrence of `O` or `A`, the positions containing a one for each bit can be represented by a single interval $[l_b,r_b]$.

We now only need to determine how the three operations affect this interval. First, `R` reverses the array, so

$$
[l,r]\longrightarrow[N-1-r,\,N-1-l].
$$

When `O` is performed and at least one one currently exists, every position from the first one to the end becomes one. Therefore,

$$
[l,r]\longrightarrow[l,N-1].
$$

An empty interval remains empty.

Finally, consider `A`. If $l=0$, the ones are consecutive from the first position, so another prefix AND leaves $[0,r]$ unchanged. If $l>0$, the first element has a zero in this bit, and that zero belongs to every prefix AND, so the entire bit becomes zero. Therefore,

$$
[l,r]\xrightarrow{A}
\begin{cases}
[0,r], & l=0,\\
\varnothing, & l>0.
\end{cases}
$$

The difficulty is that before the first `O` or `A`, the positions containing a one may have an arbitrary shape. However, only `R` can appear before that point, so there is no need to update the state after every operation. We only need the parity of the number of `R` operations. Let $p$ be the position of the first non-`R` operation. If the number of preceding `R` operations is odd, reverse the initial array once; if it is even, leave the array unchanged. Then apply $S_p$ to the actual array once in $O(N)$ time, using prefix OR if it is `O` and prefix AND if it is `A`. From that point onward, it is enough to maintain the interval $[l_b,r_b]$ for each of the 30 bits.

If the entire string consists only of `R`, the solution is even simpler. An even number of reversals leaves the original array unchanged, while an odd number produces the reversed array, so the parity of the number of `R` operations completely determines the answer.

Otherwise, after the first `O/A`, every later operation can be processed in $O(1)$ time per bit. At the end, for every bit $b$, restore the actual values by OR-ing $2^b$ into every position in its interval $[l_b,r_b]$. Since the number of bits is fixed at 30, the total amount of work is

$$
O(30(N+Q)),
$$

which is effectively $O(N+Q)$.

## D. Pipe Inspection

First, let us determine which vertices can actually be candidates for removal. When a vertex $p$ is removed, its current degree is two. The two edges connecting it to its neighbors $x$ and $y$ are deleted, and one new edge $x-y$ is added. Thus, $x$ loses one edge and gains one edge, so its degree does not change; the same holds for $y$. No matter how many times the operation is repeated, the degrees of all surviving vertices remain equal to their original degrees. Therefore, a vertex whose initial degree is not two can never be removed and must remain. Its weight $W_i$ is always included in the answer.

The degree-two vertices form the interiors of maximal paths connecting vertices whose degrees are not two. Contractions on different paths do not affect one another, so each path can be optimized independently. Consider one path

$$
v_0,v_1,\ldots,v_{m-1}.
$$

The endpoints $v_0$ and $v_{m-1}$ do not have degree two, so they must remain, and only the internal vertices can be removed. The color condition for the operation concerns not the color of the vertex being removed, but whether the two vertices that remain immediately beside it at that moment have different colors.

First, suppose that every vertex on the path has the same color. Whichever internal vertex we choose, its two neighbors always have the same color. Even after any number of contractions, all remaining vertices still have the same color. Therefore, not even one removal is possible, and we must pay the cost of every internal vertex.

Now suppose that the path contains at least one color change. Divide the path into maximal monochromatic runs, which are maximal consecutive segments of equal color. For example, the color sequence

$$
000\mid11\mid0000\mid1
$$

has four runs. The first and last runs contain the mandatory endpoints; call the runs between them internal runs.

Consider one internal run whose color is $c$. The runs immediately to its left and right both have color $1-c$. If we attempted to remove every vertex in this internal run, then when only its last vertex remained, both of its neighbors would have color $1-c$. The removal condition requires the two neighboring colors to differ, so this last vertex could not be removed. Therefore, at least one vertex must remain in every internal run.

Conversely, it is always possible to leave exactly one vertex. Suppose the run has the form

$$
(1-c),c,c,\ldots,c,(1-c).
$$

If we try to remove the occurrence of $c$ closest to the left boundary, its neighbors have colors $1-c$ and $c$, so they are different and the removal is allowed. Repeating this process lets us leave any one desired vertex. The last remaining vertex has equal colors on both sides, which is exactly why it can no longer be removed. Thus, it is optimal to leave exactly one vertex in each internal run. Since the vertices have weights, we only need to pay

$$
\min_{v\in\text{run}}W_v
$$

for that run.

The situation is different for the first and last runs, which contain the endpoints. The endpoints themselves must remain anyway. Every internal vertex in the first run can be removed one by one starting from the boundary where the color changes, because the two neighboring colors differ each time. The last run is symmetric, so all of its internal vertices can also be removed. Therefore, no additional cost needs to be paid for degree-two vertices belonging to the endpoint runs.

Consequently, if a maximal degree-two path contains at least one color change, the costs of its two mandatory endpoints have already been counted globally, and we only add the cheapest vertex from each internal monochromatic run. If there is no color change at all, none of the internal vertices can be deleted, so we add every internal cost. Each vertex and edge of the entire tree is visited only a constant number of times, giving an overall time complexity of $O(N)$.

## E. Comfy Pillow

Farm $i$ contains $X_i$ geese, each providing a feather with value $2^{Y_i}$. Visiting this farm therefore allows it to contribute one of the amounts

$$
0,2^{Y_i},2\cdot2^{Y_i},\ldots,X_i2^{Y_i}.
$$

First, consider whether it is possible to make exactly $M$ using a given set $S$ of visited farms. For every $b\ge0$, define the total capacity of the farms whose denominations are at most $2^b$ as

$$
C_b(S)=\sum_{\substack{i\in S\\Y_i\le b}}X_i2^{Y_i}.
$$

Also define

$$
R_b=M\bmod2^{b+1}.
$$

Every contribution from a farm with $Y_i>b$ is a multiple of $2^{b+1}$, so it cannot affect the lower $b+1$ bits of $M$, which are represented by $R_b$. Therefore, a necessary condition for making exactly $M$ is

$$
\boxed{C_b(S)\ge R_b}
$$

for every $b$.

For denominations that are powers of two, this condition is also sufficient. Consider the bits from low to high. The farms with $Y=0$ can match the required parity in units of $2^0$, and every two unused units of $2^0$ can be carried into the next bit as one unit of $2^1$. We can repeat the same process for $2^1,2^2,\ldots$. At each step, the condition that the total capacity in denominations no greater than the current denomination is at least the required lower-bit residue guarantees exactly that no shortage occurs.

At the final value $b=59$, we have $M<2^{60}$, so $R_{59}=M$, and the condition also guarantees that the total capacity itself is at least $M$. Therefore, the inequalities above are necessary and sufficient for constructing exactly $M$ from the selected farms.

We now need to minimize the number of visited farms. Selecting one farm $i$ contributes

$$
V_i=X_i2^{Y_i}
$$

to every applicable condition above. When processing bit $b$, only farms with $Y_i\le b$ can contribute to $C_b$, so we keep those farms in a priority queue. Let $S_b$ be the total capacity of the farms selected so far. If

$$
S_b<R_b,
$$

then farms that appear later with $Y_i>b$ can only create multiples of $2^{b+1}$ and can never resolve the current deficit in the lower $b+1$ bits. Therefore, we must select additional farms from those that have already appeared.

To minimize the number selected, it is optimal to choose the currently available farms in decreasing order of $V_i$. Indeed, among any $k$ currently available farms, the maximum total capacity is obtained by choosing the $k$ largest values of $V_i$. Thus, if some solution can exceed the current threshold with $k$ farms, the top $k$ farms can also do so. Conversely, if even the top $k$ farms are insufficient, no selection of $k$ farms can succeed. Therefore, repeatedly taking the largest $V_i$ from the priority queue until $S_b\ge R_b$ gives the minimum number of additional farms required at the current step.

It is also important that this greedy choice cannot become disadvantageous at later bits. A farm already selected at step $b-1$ was necessary to satisfy the lower bits at that time, so it cannot be replaced by a farm appearing later with $Y_i\ge b$. The contributions of those new farms are zero modulo the previous modulus $2^b$. Among the farms that must be added at the current step, choosing a larger $V_i$ also makes every later value $C_{b'}$ for $b'\ge b$ larger, so it can never be worse for the future. Therefore, accumulating the greedy choices made at every step minimizes the total number of farms.

The submitted code processes $b=0,1,\ldots,59$ in order and adds every farm with $Y_i=b$ to a max-heap. The current required value is

$$
R_b=M\bmod2^{b+1}.
$$

If the sum `sum` of the selected capacities is smaller than this value, the code repeatedly extracts the farm with the largest $V_i=X_i2^{Y_i}$ from the heap. If the heap becomes empty before the threshold is reached, no selection can construct the required lower bits, so the answer is $-1$. Each farm enters the heap once and is removed at most once, giving a time complexity of $O(N\log N)$.

## F. Singular Chunk

When the current value is $X=x$ and we choose $d$, the next value is

$$
q=\left\lfloor\frac{x}{d}\right\rfloor.
$$

The condition is

$$
\gcd\left(\left\lfloor\frac{x}{d}\right\rfloor,x\bmod d\right)=1.
$$

Writing $x=qd+r$ with $r=x\bmod d$, we obtain

$$
\gcd(q,r)=\gcd(q,x-qd)=\gcd(q,x).
$$

Therefore, all values of $d$ producing the same quotient $q=\lfloor x/d\rfloor$ either all satisfy the condition or all fail it. The condition simplifies to

$$
\boxed{\gcd(x,q)=1}.
$$

For a fixed $x$, we can also count the entire interval of values $d$ producing quotient $q$ at once. At a starting point $l$, define

$$
q=\left\lfloor\frac{x}{l}\right\rfloor.
$$

The last value that maintains the same quotient is

$$
r=\left\lfloor\frac{x}{q}\right\rfloor.
$$

Thus, every $d\in[l,r]$ leads to the same next state $q$. If $\gcd(x,q)=1$, the multiplicity of this transition is

$$
r-l+1.
$$

In other words, we can group the transitions using floor-division grouping instead of examining the values of $d$ one by one.

Even so, using every value $x=1,2,\ldots,N$ as a state would be too large because $N\le10^8$. However, because the starting state is $N$, every state that is actually reachable has the form

$$
\left\lfloor\frac{N}{t}\right\rfloor.
$$

Indeed, if the current state is $x=\lfloor N/t\rfloor$ and we next choose $d$, then

$$
\left\lfloor\frac{x}{d}\right\rfloor
=
\left\lfloor\frac{\lfloor N/t\rfloor}{d}\right\rfloor
=
\left\lfloor\frac{N}{td}\right\rfloor,
$$

which has the same form again.

There are only $O(\sqrt N)$ distinct values of $\lfloor N/t\rfloor$. For $t\le\sqrt N$, there are approximately $\sqrt N$ large quotient values. After that, the quotient itself is one of $1,2,\ldots,\sqrt N$, so the total number of states is approximately $2\sqrt N$.

The submitted code constructs the distinct values in

$$
V=
\left\{
\left\lfloor\frac{N}{1}\right\rfloor,
\left\lfloor\frac{N}{2}\right\rfloor,
\ldots
\right\}
$$

in decreasing order and uses only these values as DP states. For each state $x$, it groups $d=2,3,\ldots,x$ by quotient intervals. For $q=\lfloor x/d\rfloor$, if $\gcd(x,q)=1$, it creates an edge $x\to q$ with weight $r-l+1$.

It remains to count the paths that reach $1$ in exactly $K$ operations. Define $f_t(x)$ as the number of sequences that start from state $x$ and reach $1$ in exactly $t$ operations. The initial condition is

$$
f_0(x)=[x=1],
$$

and the transition is

$$
\boxed{
f_{t+1}(x)
=
\sum_q
\operatorname{cnt}(x,q)f_t(q)
}.
$$

Here, $\operatorname{cnt}(x,q)$ is the number of values $d$ that satisfy the condition and produce $\lfloor x/d\rfloor=q$. It is exactly the quotient-interval length $r-l+1$ derived above. The submitted code starts with `dp[id(1)] = 1`, increases the number of steps by following the edges, and finally outputs the value at the starting state $N$. This is precisely the DP described by the recurrence.

There is also a simple upper bound. Since $d\ge2$ always holds, the next state satisfies

$$
X'=\left\lfloor\frac{X}{d}\right\rfloor
\le
\left\lfloor\frac{X}{2}\right\rfloor.
$$

Therefore, before reaching $1$, the number of operations for which the state can remain positive is at most

$$
\lfloor\log_2N\rfloor.
$$

Thus, if

$$
K>\lfloor\log_2N\rfloor,
$$

the answer is immediately zero. Since $N\le10^8$, there are only about 26 valid values of $K$, so the number of DP layers is also very small.

The number of states is $O(\sqrt N)$, and the number of distinct $\lfloor x/d\rfloor$ intervals for one state $x$ is $O(\sqrt x)$. Summing this over all quotient states bounds the number of edges by approximately $O(N^{3/4})$. Since the number of valid DP steps is $O(\log N)$, the total amount of work is approximately

$$
O(N^{3/4}\log N),
$$

which is fast enough for $N\le10^8$. All calculations are performed with a modular integer modulo $998244353$.
