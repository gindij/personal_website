---
title: Is addition commutative?
date: 2023-07-14
tags: [calculus, infinity]     # TAG names should always be lowercase
use_math: true
---

# Introduction
This will be a quick one. From the very beginning of our mathematical educations, there is a
fundamental fact of which we are made aware: If $a$ and $b$ are numbers of any
kind (natural numbers, integers, rationals, reals, complex), then -- now hold on to your hats --
<div>
\begin{align*}
    a + b = b + a.
\end{align*}
</div>

Earth-shattering -- I know. As some of you are likely already aware, I find infinity fascinating. In this post,
I want to briefly discuss how infinity can mess with some of our most basic assumptions
about the nature of one of our most basic arithmetic operations. Without further ado, let's dive
in.

# Infinite series
In our everyday lives (unless you're a mathematician), we only ever consider what it means to carry
out arithmetic operations on finite collections of numbers. A first course in calculus challenges us
to think about the nature of infinite sums, objects like:
<div>
\begin{align*}
    1 + \frac{1}{2} + \frac{1}{3} + \frac{1}{4} + \dots
\end{align*}
</div>
or
<div>
\begin{align*}
    1 + \frac{1}{2} + \frac{1}{4} + \frac{1}{8} + \dots
\end{align*}
</div>

But what -- you might ask -- is so interesting about these sums? Don't an infinite number of numbers
added together have to add up to $\infty$?

(Mathmatical aside -- feel free to skip:
Before answering that question, it is worth considering what mathmeticians even mean when they ask
about the sum of an infinite number of terms. We could never actually add up infinitely many terms,
so instead, the "sum" of an infinite series is the limiting value of the sequence of the series'
partial sums. That is for the second series, we want to know the limiting value of the sequence
$1, 1 + \frac{1}{2}, 1 + \frac{1}{2} + \frac{1}{4}, \dots$.)

It turns out that some infinite series have finite sums (like the second one) and others do not
(like the first one). There are lots of rules and tests that one can perform to determine
what kind of series one is looking at, but for our purposes, it suffices to know that we call
the ones with finite sums **convergent** and the ones whose sums are infinite **divergent**.

# The harmonic series diverges
The first series we looked at earlier is so famous that it has a name: the Harmonic Series. It
is an example of a series whose underlying sequence has terms that get smaller and smaller, but
which, despite that fact, diverges. For full effect, let's briefly have a look at why it diverges.

Let $H$ denote the sum of the Harmonic Series, and let's compare $H$ to the sum of another,
similar-looking series $H'$:
<div>
\begin{align*}
    H &= 1 + \frac{1}{2} + \biggr(\frac{1}{3} + \frac{1}{4}\biggr) + \biggr(\frac{1}{5} + \frac{1}{6} + \frac{1}{7} + \frac{1}{8}\biggr) + \dots \\
    H' &= 1 + \frac{1}{2} + \biggr(\frac{1}{4} + \frac{1}{4}\biggr) + \biggr(\frac{1}{8} + \frac{1}{8} + \frac{1}{8} + \frac{1}{8}\biggr) + \dots
\end{align*}
</div>
Notice that (1) each of the parenthesized groups of terms in $H'$ sum to $1/2$, and (2) each of the corresponding groups of terms
in $H$ sums to a number that is _greater_ than $1/2$. One of the rules one learns in calculus is (informally) that if you are
comparing two sums and the smaller of them is infinite, the larger one must be infinite too. Since $H > H'$ because of (1), and
$H' = \infty$ because it is a sum of infinitely many $1/2$s, $H$ must be infinite too.

# Can we make it converge?
Even though $H$ diverges, we can make it converge by changing half of the additions into subtractions like so:
<div>
\begin{align*}
    1 - \frac{1}{2} + \frac{1}{3} - \frac{1}{4} + \frac{1}{5} - \frac{1}{6} + \frac{1}{7} - \frac{1}{8} + \dots \\
\end{align*}
</div>
It turns out that this modified series converges! (We will not spend time proving that this series converges, but if you're interested, I wrote another post a while ago covering that; you can check it out [here](https://www.jgindi.me/posts/alternating/).)
To make things even weirder, if you group the terms with even denominators together, the sum of their absolute values diverges. (The same applies to the group of terms with odd denominators, as you might expect.)

For visual intuition that this series converges (in lieu of a proof), this image should help
([source](https://xaktly.com/AlternatingSeries.html)):
<div>
<img src="alternating.png" width="500"/>
</div>

The image shows that it converges to approximately 0.694. I claim that I can make it converge to whatever value
I want...

But how?

# Other arrangements
Let's say that I wanted the sum of the terms of the alternating harmonic sequence to be 2.
Carry out the following algorithm:
1. Add positive terms together until we exceed 2 (we would start with $1 + 1/3 + \dots + 1/15 \approx 2.02$)
2. Add negative terms to the output of step 1 until we were below 2 (we would then add $-1/2$, so we'd end up at around $1.72$)
3. Add positive terms to the output of step 2 until we exceed 2 (starting with $1/17$, an so on)
4. Add negative terms until we fell below 2
5. etc.

Why will this sequence of numbers (2.02, 1.72, ...) produced by the algorithm I proposed converge to 2?
1. As we mentioned earlier, the absolute values of terms in both positive and negative groups both sum to $\infty$, so we will always
   have enough terms that we haven't used that can get us above or below 2 when we need to.
2. The sizes of the successive terms from each group that we use are getting smaller and smaller, so at each step, the amount by which
   we exceed and fall below 2 shrinks.

Taken together, we see that will oscillate around 2, and successive oscillations will be smaller and smaller; that's simply a fancy
way of saying that our series now converges to 2. But didn't it just converge to 0.694?

This is a quirk of trying to sum infinitely many terms. When finitely many terms are involved, rearranging terms doesn't affect
anything. There are even many infinite sums for which the same is true (our second one from earlier, for example).
With infinity, though, it is always important to reexamine facts that are obvious or trivial-seeming in finite territory. We've just
shown that under special conditions, the infinite version of $a + b$ is not necessarily the same as $b + a$.