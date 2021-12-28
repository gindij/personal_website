---
title: The Alternating Series test
date: 2017-09-27
tags: [calculus]     # TAG names should always be lowercase
use_math: true
---

## Introduction
While I was in college, I spent a few semesters TAing Calc II (Calc BC if you do it in high school). Both when I took the class and when I TAed it, I found the part of the course devoted to infinite series the most interesting by far. It was (and still is) amazing to me that you can — informally — add together an infinite number of terms and get a finite result. In fact, until the 19th century, the above was considered paradoxical and incorrect. To help determine whether or not different series converge, mathematicians developed a suite of tests whose statements are simple enough to be taught to introductory calculus students but whose power cannot be overstated.

In this post, we will prove that the “alternating series test” is valid. While the explanation might be a little bit involved, I hope to include all of the necessary background here. Hopefully, all you’ll need to follow this post is some patience and willingness to challenge yourself a little; this proof certainly challenged me, but I think the effort was well worth it.

If you are unfamiliar with what an infinite series is, see [Series (mathematics) - Wikipedia](https://en.wikipedia.org/wiki/Series_(mathematics)).

## The problem
Before we start, we should understand the problem we are trying to solve. Lets say we have some infinite series, and let’s write it down as
<div>
$$\sum_{n = 1}^\infty a_n = a_1 + a_2 + a_3 + \dots.$$
</div>
The above series may converge or it may not. We can modify this rather plain series by adding together the terms of the
same underlying sequence with alternating signs, forming what is known as an **alternating series**. Symbolically, an
alternating series has the form
<div>
$$\sum_{n=1}^\infty (-1)^{n-1}a_n = a_1 - a_2 + a_3 - \dots.$$
</div>
(Notice that the alternation comes from the fact that -1 to an even power is 1 and -1 to an odd power is -1.)
If the non-alternating series converges to a finite sum, then the alternating series clearly does as well.* If not,
though, would introducing alternation maybe force convergence? If so, we say that $\sum (-1)^{n-1}a_n$ **converges  conditionally**;
if not, oh well… some things just aren’t meant to be.

## Preliminaries
More specifically, the AST is concerned with what conditions we need to place on our underlying sequence, $a_n$, so that
alternation implies convergence. It turns out that whether an alternating series converges is quite easy to check; the
only two things we need to verify are:
1. The terms of the original (non-alternating) sequence are decreasing. Symbolically, we want $a_1 \geq a_2 \geq a_3
\geq \dots$
2. $a_n \to 0$.

Our aim for the rest of this post is to prove that if $a_n$ satisfies (1) and (2), then $\sum (-1)^{n-1} a_n$ converges
to a finite sum. In order to do this, though, we need a bit of machinery from real analysis, which we discuss next.

## Completeness and the monotone convergence theorem
Real analysis (in an oversimplified sense) is the study of properties of the real numbers and functions of a real
variable (things like continuity, differentiability, integrability and some other related stuff). The first thing one
often does in a real analysis class is to formally discuss what the real numbers are, why we need them, and how to
construct them. To go into all of that here would take us pretty far afield, but in short, the real numbers is the first
example of a truly continuous set in the sense that there are no holes. (Although the rational numbers are dense, there
are holes where irrational numbers — e.g. $\sqrt{2}$ — should be. Integers and natural numbers clearly have holes, e.g.
between 1 and 2.)

One of the challenges students (myself included) typically face when discussing and trying to wrap your head around the
above for the first time is how to rigorously define this idea that there are no holes (mathematically called
**completeness**). Most commonly, the definition you settle on is that every sequence of real numbers that has an upper
bound has a *least* upper bound (same with lower bounds and a *greatest* lower bound).

A textbook I’ve been going through actually shows that in addition to the above characterization — often referred to as
the Axiom of Completeness — there are (at least) 4 other *equivalent* ways to characterize completeness, one of which we
will use in the proof that we’re going to attempt below. It’s called the Monotone Convergence Theorem (MCT), and it
states: any sequence (of real numbers) that is (1) bounded and (2) monotone converges. (A sequence is said to be
**bounded** if there is a number $M$ such that all terms of the sequence are contained in the interval $[-M, M]$. A
sequence is **monotone increasing** if every term is greater than or equal to the preceding term and **monotone decreasing** if each term is less than or equal to the preceding term; if a sequence is monotone increasing or monotone
decreasing, we say it’s **monotone**, as you might expect.)

The MCT is pretty powerful. Boundedness and monotonicity are often intuitive properties that we can hand-wavily infer
about a sequence we are examining and with MCT, we can transform those properties into what is often times the holy grail
of sequence (and series) analysis: convergence!

Another technicality we need to address before we tackle our main result is that we need to clarify what we mean when we
write $\sum_{n=1}^\infty a_n$. What does it *actually* mean for $\sum_{n=1}^\infty a_n$ to “converge” to some finite
value $S$?

When we say that the sum converges, what we mean is that as we add more and more terms, we get closer and closer to $S$.
In other words, to know whether a series converges, we need to know whether or not what we call the sequence of **partial sums** of $a_n$ — $a_1, a_1 + a_2, a_1 + a_2 + a_3, \dots$ — converges. Technically speaking, let $s_n$ denote the $n$th
partial sum of the $a_n$; that is, $s_n = a_1 + a_2 + \dots + a_n$. Then saying that$ S = \sum_{n=1}^\infty a_n$ is the
same as saying $S = \lim_{n \to \infty} s_n$. That last sentence is just formalism; what is really important here is that
to show that some series converges to a number, we need only show that the partial sums of the sequence’s terms converge.
With this in mind, we’re ready for the main result.

(Before continuing, make sure that you understand the main points from the previous two paragraphs.)

Before writing a proof, I often find it helpful to have context for the way the proof is going to unfold so that as I’m writing the proof, I’m able to remember where I am and how it’s supposed to help me get where I want to go. The statement we are trying to prove here is:

> **Alternating Series Test:** If $a_n$ is decreasing and $a_n \to 0$, then $\sum (-1)^{n-1} a_n$ converges.

## Proof sketch
The proof will proceed by the following steps:
1. We will consider two subsequences of the sequence of partial sums: the first will be the subsequence of partial sums that add up an odd number of terms and the second will be the subsequence of partial sums that add up even numbers of terms. We will call the “odd” one $s_{2n+1}$ and the “even” one $s_{2n}$.
2. We will show that each of the subsequences are bounded.
3. We will show that each of the subsequences are monotone.
4. (2) and (3) imply that both converge by MCT, say to limits $L_1$ and $L_2$ respectively.
5. We will show that $L_1 = L_2$ and will call the shared limit $L$.
6. We note that the “whole” sequence of partial sums can be made up by interleaving terms of the even and odd subsequences.
7. If the two subsequences converge to L and we can interleave them to form the original sequence, then it also must converge to $L$. The original sequence was the equivalent partial sum expression of our alternating series, so the proof is complete.


## Proof
Now, for the proof.

The odd subsequence looks like:
<div>
$$a_1, a_1 - a_2 + a_3, a_1 - a_2 + a_3 - a_4 + a_5, \dots$$
</div>
Recall that the $a_n$ are decreasing. This means that subtracting $a_2$ and then adding back a little less than $a_2$ to
$a_1$ is going to leave you slightly short of $a_1$. When you subtract $a_4$ from and then add $a_5$ to $a_1 - a_2 +
a_3$, you’re going to end up slightly short of $a_1 - a_2 + a_3$. Extending this logic, we arrive at two conclusions.
First, we see that the odd partial sums are monotonically decreasing because with every pair of $a_k$ that we tack on to
obtain a successive term, we subtract some amount and then add back a bit less than we got rid of. Second, we see that
once we depart from $a_1$ (and start our adding and subtracting madness), we never quite make it back. In other words,
we can bound the odd partial sums by $a_1$.

(There are formal, symbolic ways of representing all of this, but if you understand the above line of reasoning, you’ve
understood the gist, IMO.)

Next, we note that for each $k$, the partial sum $s_{2k} \leq s_{2k+1}$ because the last term of every odd partial sum adds some small positive amount to the previous sum, which was made up of an even number of terms. For example, when $k = 1$, we have
<div>
$$s_2 = s_{2 \times 1} = a_1 - a_2 + a_3 - a_4 \leq a_1 - a_2 + a_3 - a_4 + a_5 = s_{2 \times 1 + 1} = s_3.$$
</div>
Thus, the even partial sums must be bounded too!

Further, notice that the even partial sums monotonically increase. We can see this by observing that each time we tack a
pair of $a_i$, we add a little bit and then subtract a little bit less. When we go from
$s_2 = a_1 - a_2$ to $s_4 = a_1 - a_2 + a_3 - a_4$, we take $a_1 - a_2$ and change it slightly by adding $a_3$ and then subtracting a little bit less than $a_3$ ($a_4$).

But wait! We now have established that both the odd and even sums are monotone and bounded, so they must both converge!
Let’s call the limit of the sequence of odd partial sums $L_1$ and the limit of the even partial sums $L_2$. To prove
that $L_1$ and $L_2$ are the same, all we need to do is show that the difference between them is 0. To see this, we
simply observe that
<div>$$\lim_{k \to \infty} s_{2k+1} - s_{2k} = \lim_{k \to \infty} a_{2k+1} =  0.$$</div>
Thus $L_1 = L_2$; we will henceforth refer to the limit as $L$.

We can reconstruct the sequence of partial sums representing our original series by interleaving terms of the odd and
even subsequences. Because both subsequences tend to $L$, we can conclude that the sequence constructed from the
interleaved subsequences also tends to $L$. This completes the proof, as we’ve provided the desired finite limit for our
alternating series’ partial sums.

## Conclusion
Sometimes we’re taught formulas and tests in class and are — rather unfortunately — not challenged to understand why
they work as well as they do. As I’ve spent a little bit of time going back through some of the things I took for
granted when I first encountered them, I’ve found that the stuff behind the curtain is often very interesting and
illuminating. In this particular case, I hope you have too.





*If adding a bunch of positive (the same type of argument will work if some terms are negative) terms together gives some finite positive result, then subtracting some of the terms instead of adding them will certainly give a finite result as well. The sum of the alternating series is thus bounded above by the sum of the non-alternating terms.
