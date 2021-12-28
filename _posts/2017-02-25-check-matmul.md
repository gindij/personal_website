---
title: Randomized matrix multiplication checking
date: 2017-02-25
tags: [computer-science,linear-algebra]     # TAG names should always be lowercase
use_math: true
---

## Introduction
Warning: This post is pretty technical. It details a part of a lecture from a class
I'm in this semester. It requires some mathematical maturity. I'll to my best to make
it as accessible as possible, but how effective I'll be at that remains a mystery...
Here we go!

## Background
The simplest algorithm for matrix multiplication takes $O(n^3)$. As of right now, the
fastest algorithm we have for matrix multiplication runs in $O(n^{2.373})$ time. This
algorithm is very complicated and I won't go into detail here (mostly because I don't
know them), but should we devise an algorithm that is even faster than that, we might
want an efficient algorithm to check that it computes the correct result. A little
bit of thought shows us that the best conceivable complexity for matrix
multiplication is $O(n^2)$ --- that is the amount of time it would take us to write
out the result even if no additional computation was necessary. This post details a
randomized algorithm that checks the correctness of matrix multiplication in $O(n^2)$
time. Now, to the formal problem statement...

Suppose you had three $n \times n$ matrices $A, B$, and $C$ and I wanted to know
whether $AB = C$.

## First approach
A first approach is to choose a random entry $C_{ij}$ and check that it was computed
correctly by checking that it equals the dot product of row $i$ of $A$ and column $j$
of $B$. Each such check takes linear time, so we can do up to $n$ of them and still
stay at our desired runtime of $O(n^2)$. This seems clever enough... I mean, $n$
entries is pretty good, right? If all of those entries were computed correctly, we
can be reasonably sure that $AB = C$... right?

## Trying again
Nope! The problem with this approach can be better understood by asking the following
question: If there is some entry of $C$ that was computed incorrectly, what are the
odds that the above algorithm would catch it? Well, if there are $n^2$ entries in $C$
and we choose $n$ of them to check, provided that we pick them uniformly at random,
we have a $\frac{n}{n^2} = \frac{1}{n}$ chance of catching the entry that was
computed incorrectly. Because asymptotically, $n^2$ runs away from $n$, as our
matrices get bigger, our odds of catching a mistake shrinks. That isn't good! How
else might we go about this?...

Consider the following algorithm:
1. Generate an $n$-bit vector $r$ where each component of the vector is selected
   uniformly at random from $\\{0,1\\}$.
2. Compute $ABr$ and $Cr$.
3. If $ABr = Cr$, return true, else return false.

Note that step 1 takes $O(n)$ time. Because $ABr = A(Br)$ and $Br$ is just a vector
of length $n$, step 2 takes $O(n^2 + n^2) = O(n^2)$ time. The last step takes linear
time, so, in total, the above approach takes $O(n^2)$ time --- just what we need. But
how does it compare to the alternative at finding mistakes?

## Does it work?
There are two cases to consider here:
1. $AB = C$
2. $AB \neq C$

In the first case, there aren't any mistakes to catch, so our analysis need only
consider the second case. Define $D = C - AB$. What we are formally interested in is
the probability that $ABr = Cr \iff (C - AB)r = Dr = 0$ when $AB \neq C \iff C - AB =
D \neq 0$. In other words, we are interested in $\Pr[Dr = 0 ~|~ D \neq 0]$. To
compute this probability, suppose there was indeed an entry of $C$ that was computed
incorrectly. Without loss of generality, assume that $D_{11} \neq 0$. Note that this
means that $(AB)_{11}$ and $C_{11}$ are different, so $D_{11}$ is of interest to us ---
it is the elusive mistake. To better understand what's going on here, consider the
following:
<div>
$$\begin{bmatrix}D_{11} & \dots & D_{1n}\\\vdots & \ddots &\\&
&\end{bmatrix}\begin{bmatrix}r_1\\ r_2\\ \vdots\end{bmatrix}=\begin{bmatrix}D_{11}r_1 + \dots + D_{1n}r_n\\ \vdots\end{bmatrix}$$
</div>
(computation of $Dr$).

The only way the algorithm can be fooled is if, somehow, $D_{11}r_1 + \dots +
D_{1n}r_n = 0$. That is, if the first entry of $Dr$ is $0$, we find ourselves in a
potential case wherein $Dr = 0$ even though $D \neq 0$ --- in English, we find
ourselves in a case, when $ABr = Cr$ even though $AB \neq C$. What is the probability
of this happening? Some thought suggests that we are looking for the probability that
$D_{11}r_1 + \dots +D_{1n}r_n = 0$, or, equivalently, the odds that $r_1 = -
\frac{D_{12}r_2 + \dots D_{1n}r_n}{D_{11}}$. Recall that $r_1 \in \\{0,1\\}$. If that
ugly fraction is neither 0 nor 1, we're good to go because $r_1$ cannot possibly take
on that value. If, however, that fraction does equal 0 or 1, then there is a
$\frac{1}{2}$ chance that we assigned $r_1$ that value. Thus, $\Pr[Dr = 0 ~|~ D \neq
0] \geq \frac{1}{2}$, which means that our algorithm will catch a mistake at least
half of the time.

## Conclusion
Isn't math cool?!?! In lecture, this was used to show that
randomization is a powerful tool that allows to do all kinds of mathematically
rigorous magic. I was blown away; I hope you were too.
