---
title: The Basel problem
date: 2017-05-26
tags: [misc]     # TAG names should always be lowercase
use_math: true
---

## Introduction
In this post, I want to talk about what the Basel Problem is and how Euler solved it.
Even today, Euler remains one of the most accomplished mathematicians there ever was.
His work impacted and created a multitude of fields across mathematics: number
theory, graph theory and topology, real and complex analysis, and parts of physics.
His solution to the Basel Problem, which we will discuss below, catapulted him to
fame in 1734.

## Background
Before we attack the Basel Problem itself, I want to set the stage. Students who have
taken an introductory course in calculus are familiar with the harmonic series:
<div>
$$\sum_{n=1}^\infty \frac{1}{n} = 1 + \frac{1}{2} + \frac{1}{3} + \frac{1}{4} +
\dots.$$
</div>
This series is the first example students are usually given of a series that diverges
even when its individual terms tend to 0. The fact that the harmonic series diverges
was originally proved by Oresme in the 15th century, but his work was lost. About 250
years later, in the mid 17th century, the Bernoulli brothers re-proved the result.
Their success sparked their interest in the convergence/divergence of other infinite
series, one of which happened to be a natural extension of the harmonic series:
<div>
$$\sum_{n=1}^\infty \frac{1}{n^2} = 1 + \frac{1}{4} + \frac{1}{9} + \frac{1}{16} +
\dots.$$
</div>

The Basel Problem was to find the sum of this series. As a note before we see how
Euler did it, figuring out *whether* an infinite sum converges is typically a *much*
easier problem than *computing* the sum. It’s easy to show that $\sum \frac{1}{n^2}$
converges using any one of a number of simple convergence tests (e.g. comparison,
integral), but finding the actual sum is a different matter entirely. This brings us
to our main result.

## Euler's Argument
What we want to prove can be stated quite succinctly.

>**Theorem:** $\sum_{n=1}^\infty \frac{1}{n^2} = \frac{\pi^2}{6}$.

Euler started off by considering the infinite polynomial
<div>
$$f(x) = 1 - \frac{x^2}{3!} + \frac{x^4}{5!} - \dots + \frac{(-1)^k x^{2k}}{(2k +
  1)!} + \dots.$$
</div>
For $x \neq 0$,
<div>
$$f(x) = \frac{xf(x)}{x} = \frac{x - \frac{x^3}{3!} + \frac{x^5}{5!} - \dots +
\frac{(-1)^k x^{2k + 1}}{(2k + 1)!} + \dots}{x}.$$
</div>
Note that the numerator of $f(x)$ is actually the Taylor expansion of $\sin(x)$, so
we can actually (for $x \neq 0$) write
<div>
$$f(x) = \frac{\sin(x)}{x}.$$
</div>
Provided that we can find the roots of $f(x)$, we can factor it. In our case, $f(x) =
0$ when $\sin(x) = 0$, so the infinitely many roots $f$ are $k\pi$ for all integers
$k$. We can thus factor $f$ as:
<div>
$$f(x) = (1 - \frac{x}{\pi})(1 + \frac{x}{\pi})(1 - \frac{x}{2\pi})(1 +
\frac{x}{2\pi})\dots.$$
</div>

(This factorization comes from a theorem in algebra that states that for a polynomial
$p(x)$, if the roots of $p$ are $a_1, a_2,\dots, a_n$ and $p(0) = 1$, then you can
factor $p(x) = (1 - x/a_1)(1 - x/a_2)\dots(1-x/a_n)$.)

Next, we observe that each pair of factors of the form $(1 - \frac{x}{k\pi})(1 +
\frac{x}{k\pi})$ can be combined into $1 - \frac{x^2}{k^2\pi^2}$, so f now looks
like
<div>
$$f(x) = (1 - \frac{x^2}{\pi^2})(1 - \frac{x^2}{2^2\pi^2})(1 - \frac{x^2}{3^2\pi^2})\dots$$
</div>
Okay… this is good, because $f$ smells of both $\pi$ and the squares of natural numbers…

To force these pieces into place, Euler then multiplied out our factorization and collected the coefficients of like powers of $x$. In particular, as it pertains to the our problem, he only cared about the coefficient on the $x^2$ term of $f$. The only $x^2$ terms in $f$ are those that are produced by multiplying an $x^2$ term with a 1. Adding up these terms, we get the following sum:
<div>
$$-\frac{x^2}{\pi^2} - \frac{x^2}{4\pi^2} - \frac{x^2}{9\pi^2} - \dots = x^2\biggr[-\frac{1}{\pi^2}\biggr(1 + \frac{1}{4} + \frac{1}{9} +\dots\biggr)\biggr],$$
</div>
so the coefficient is
<div>
$$-\frac{1}{\pi^2}\biggr(1 + \frac{1}{4} + \frac{1}{9} +\dots\biggr).$$
</div>

From our original representation of $f$ (before we multiplied it by $\frac{x}{x}$),
we know what the coefficient should be, namely $-\frac{1}{3!}$. Equating the
coefficient we found with what we know it should be, we have
<div>
$$-\frac{1}{3!} = -\frac{1}{\pi^2}\biggr(1 + \frac{1}{4} + \frac{1}{9} +\dots\biggr).$$
</div>
which, rearranged a bit, solves the Basel problem because it means that
<div>
$$\frac{\pi^2}{6} = 1 + \frac{1}{4} + \frac{1}{9} + \frac{1}{16} + \dots.$$
</div>

## Conclusion
Before I conclude, I want to clarify that this argument was not entirely rigorous.
Without justification, he produced much of his argument by extending finite results
to the infinite.

Nonetheless, his solution to the Basel Problem is a great example of the ingenuity
with which Euler attacked many of the open mathematical conundrums of his day.
Despite its reputation as rote and formulaic, mathematics requires a wild
imagination. Euler’s was one of the wildest, and mathematicians who inherited his
legacy could not be more thankful.
