---
title: Finding eigenvalues
date: 2021-03-08
tags: [linear-algebra]     # TAG names should always be lowercase
use_math: true
---

## Introduction
Over the past few months, I've been working on some optimization-related projects
at work. Making optimization algorithms efficient and effective often comes down to command of
numerical linear algebra, otherwise known as the intersection of linear algebra and computers.
It is one thing to discover an algorithm for certain problems that works well in
the ether. It is another entirely to ensure that the algorithm works well once
it violently collides with the physics of finite precision computers. As someone
who has come to deeply appreciate the power of mixing elegance and implementation,
I decided to delve more deeply into the subject by making my way through
_Numerical Linear Algebra_ by Trefethen and Bau.

This post works through one of the chapters about developing an algorithm to find
the largest eigenvalue and its corresponding eigenvector of a symmetric positive
definite matrix $A$.

## Review of eigenvalues and eigenvectors
Eigenvalues and eigenvectors are central in applied linear algebra. They have
applications across machine learning, communication systems, mechanical engineering,
optimization, and many other disciplines. In essence, an eigenvector $v$ of a matrix
$A$ is a vector that is exclusively stretched (not rotated) when acted upon by $A$.
An eigenvalue $\lambda$ of $A$ that corresponds to $v$ is the stretch factor. Formally,
$v$ is an eigenvector of $A$, with corresponding eigenvalue $\lambda$ if we have
<div>
$$Av = \lambda v.$$
</div>
For the rest of the post, we will assume we're dealing with a symmetric
positive definite matrix.

## A helpful characterization
Our first step will be to develop a helpful characterization of eigenvalues. To do
this, given a matrix $A$ and a nonzero vector $x$, we consider the problem
<div>
$$\text{minimize}_\alpha ~~ \| Ax - \alpha x\|_2^2.$$
</div>
We are essentially trying to find a scalar that is the closest approximation we
can find to an eigenvalue corresponding to the vector $x$, _i.e._ an $\alpha$ such
that $Ax \approx \alpha x$. We can easily solve this minimization problem by
setting the derivative of the objective (w.r.t. $\alpha$) to 0 and solving for $\alpha$.
Carrying this out, as a function of $x$, we get:
<div>
$$
  \alpha(x) = \frac{x^TAx}{x^Tx}
$$
</div>

What we are interested in are the critical points of $\alpha(x)$ as a function of $x$.
Using the vector analog of the quotient rule for taking derivatives, we have
<div>
\begin{align*}
  \nabla_x \alpha(A, x) &= \frac{2Ax}{x^Tx} - \frac{(2x)(x^TAx)}{(x^Tx)^2} \\\\
  &= \frac{2}{x^Tx}\biggr(Ax - \biggr(\frac{x^TAx}{x^Tx}x\biggr)\biggr) \\\\
  &= \frac{2}{x^Tx}(Ax - \alpha(x)x).
\end{align*}
</div>

Suppose $v$ is a critical point of $\alpha$, _i.e._ $\nabla \alpha(v) = 0$. For that $v$,
we would have $Av = \alpha(v)v$. That is, $v$ is an eigenvector of $A$, with eigenvalue
$\alpha(v)$. Conversely, if $v$ is an eigenvector of $A$ with corresponding
eigenvalue $\lambda$, then we have $r(v) = \lambda \frac{x^Tx}{x^Tx} = \lambda$.
We've now shown that the vectors $v$ that make the derivative 0 are _exactly_ the
eigenvectors of $A$. For each of those eigenvectors, $\alpha$ produces the corresponding
eigenvalue.

This characterization of eigenvalues and eigenvectors is important because it gives
us an iterative way to think about these mathematical objects with a definition that is more amenable
to computation. The function $\alpha$ is important enough that is has a name, the
_Rayleigh quotient_, and it is crucial to our development of the algorithms below.

Thus far, given an arbitrary vector, we've found a way to come up with an
eigenvalue-like scalar that corresponds to it. Intuitively order to find a bona
fide eigenvalue of $A$, we have to iteratively nudge our initial eigenvector estimate
toward eigenvector-hood. As our initial guess tends toward an eigenvector $v$, $\alpha(v)$
tends toward an eigenvalue of $A$.

## Power iteration
Power iteration is not our destination but it is a conceptual building block that
we will spend a moment on here. Ultimately, it has some limitations, but its ideas
will help us later.

### The algorithm
The algorithm finds the largest eigenvalue and corresonding eigenvector of a matrix
$A$. To do this, it starts with an arbitrary vector $v_0$ and computes $v_i = Av_{i-1}$,
for $i = 1,\dots, m$ (normalizing each of the results). It then uses the estimate
$v_i$ to compute our $i$th estimate of $\lambda_i$ by computing $\alpha(v_i) = v_i^TAv_i$
(no denominator because $|| v_i || = 1$). As we will show momentarily, as $i \to \infty$,
$\lambda$ converges to the largest eigenvalue $\lambda_1$ of $A$ and $v$ converges to an
eigenvector $v_1$ corresponding to $\lambda_1$. Before we prove anything, here is code
that implements power iteration in the Julia programming language.

```julia
function power_iteration(A, iters)
    m = size(A, 1)
    v = rand(m)
    v = v / norm(v, 2)
    λ = 0.
    for i = 1:iters
        # update v
        u = A * v
        v = u / norm(u, 2)
        # Rayleigh quotient
        λ = v' * A * v
    end
    return λ, v
end
```

In essence, what we've provided is a way of finding the largest eigenvalue
and its eigenvector beginning from a crude estimate of the eigenvector.

### Why does it work?
To show that the sequences of iterates converge in the way that we claimed, we
just need to show that the sequence $v_i$ converges to an eigenvector of $A$ (
because we've already shown that given an eigenvector, $\alpha$ produces an eigenvalue).

Let's say that $\\{q_i\\}$, $i = 1,\dots,m$, make up an orthogonal basis of eigenvectors of $A$
corresponding to the eigenvalues $\lambda_i$ (this set exists because $A$ is symmetric).
We can also assume, without altering the proof, that $|\lambda_1| > |\lambda_2| \geq \dots \geq |\lambda_m|$.
Because $v_k = c_kA^kv_0$ for some sequence of constants $c_k$ (because of the
normalization at each step), we can use the expansion of $v_k$ in the basis $\\{q_i\\}$ as
<div>
\begin{align*}
    v_k &= cA^kv_0 \\\\
    &= cA^k(a_1q_1 + \dots + a_mq_m)\\\\
    &= c(a_1\lambda_1^kq_1 + \dots a_m\lambda_m^kq_m)\\\\
    &= c\lambda_1^k(a_1q_1 + a_2(\lambda_2/\lambda_1)^kq_2 + \dots a_m(\lambda_m/\lambda_1)^kq_m)
\end{align*}
</div>
Because $\lambda_1$ is greater than all other eigenvalues, as $k \to \infty$, all
but the first of the terms in the parentheses in the last line go to zero, so we have
$v_k \to c_ka_1\lambda_1^kq_1$, which is a scalar multiple of $q_1$, the eigenvector
of $A$ corresponding to the largest eigenvalue. (We do not need to worry about
the sign of the constants; the important thing is that the one-dimensional
subspace spanned by the $v_k$ is the same as the one spanned by $q_1$.)

### Limitations
Unfortunately, power iteration is not really used in practice for a couple of reasons.
The first is that it only finds the eigenpair corresponding to the largest eigenvalue. The second
is that the rate at which it converges depends on how much larger $\lambda_1$ is than $\lambda_j$
for $j > 1$. If, for instance, $\lambda_1$ and $\lambda_2$ are close in magnitude,
the convergence is very slow. There is a modification we can make to mitigate some of
these issues, which we'll discuss next.

## Inverse iteration
Let's see if we can find a better, more reliable way to find these eigenvectors. Suppose
that $\mu$ is a scalar that is _not_ an eigenvalue of $A$ and let $v$ be an eigenvector
of $A$ with associated eigenvalue $\lambda$. We can show that $v$ is also an eigenvector
of $(A - \mu I)^{-1}$ by
<div>
\begin{align*}
    (A - \mu I)v &= Av - \mu v \\\\
    &= (\lambda - \mu) v
\end{align*}
</div>
If we multiply on the left by $(A - \mu I)^{-1}$ and then divide on both sides by
$\lambda - \mu$, we have $(A - \mu I)^{-1}v = v / (\lambda - \mu)$. In other words,
$1/(\lambda - \mu)$ is an eigenvalue of $(A - \mu I)^{-1}$. (The invertibility of
$A - \mu I$ follows from the fact that $\lambda_i - \mu \neq 0$ for
each $i$.)

(You might be thinking: "What if $\mu$ is exactly equal to or very close to an
eigenvalue of $A$?" While we won't go into detail here, it turns out that these
cases doesn't really cause additional computational issues.)

What's nice about all this through is that if we take $\mu$ to be
a reasonable estimate of one of the eigenvalues $\lambda_i$ (more on this in a bit),
then we will have $(\lambda_i - \mu)^{-1}$ much larger than $(\lambda_j - \mu)^{-1}$
for $j \neq i$. We can thus conduct power iteration on $(A - \mu I)^{-1}$ and converge
very quickly to an eigenvector of $A$ -- essentially because we've magnified the
difference between one eigenvalue of $A - \mu I$ and the rest. Before we move on,
here is code (in Julia) that carries out inverse iteration:

```julia
function inverse_iteration(A, μ, iters)
    m = size(A, 1)
    v = rand(m)
    v = v / norm(v, 2)
    # compute the matrix we want to invert for reuse. we could also factor
    B = A - μ * I(m)
    for i = 1:iters
        # applying the inverse matrix is same as solving system
        w = B \ v
        # normalize
        v = w / norm(w, 2)
    end
    return v
end
```

At this point, we have a way of turning vectors into reasonable eigenvalue estimates
(the Rayleigh quotient) and a reasonable way of turning eigenvalue estimates into
eigenvectors (inverse iteration). Can we combine these somehow? The answer is yes,
and this discussion is the final leg of our journey.

## Rayleigh quotient iteration
We can put the two algorithms together by repeating two operations:
1. Use an inverse iteration step to refine our estimate of the eigenvector using
the latest estimate of $\lambda$.
2. Using Rayleigh quotient to turn the refined eigenvector estimate into a refined
eigenvalue estimate.

As the eigenvalue estimate $\mu$ becomes better, the speed of convergence of inverse
iteration increases, so that this natural combination yields our best algorithm yet.
Without detailing the convergence proof, this algorithm converges _extremely_ quickly:
on every iteration, the number of digits of accuracy on the eigenvalue estimate _triples_!

Here is the code in Julia:
```julia
function rayleigh_iteration(A, iters)
    m = size(A, 1)
    v = rand(m)
    v = v / norm(v, 2)
    λ = v' * A * v
    for i = 1:iters
        # inverse iteration step
        w = (A - λ * I(m)) \ v
        v .= w ./ norm(w, 2)
        # Rayleigh quotient
        λ = v' * A * v
    end
    return λ, v
end
```

## Conclusion
In this post, we went through a couple of different algorithms that help us find eigenvalues
and eigenvectors. While we typically first learn that eigenvalues and eigenvectors should be thought
about in the context of characteristic polynomials and determinants, it turns out that for both
theoretical (due to Abel) and computational (ill-conditioning of root-finding algorithms)
reasons, an iterative approach is actually _required_ for finding them in practice.

In addition to wanting to cement my understanding of these algorithms as well as possible
before moving to the next lecture in the textbook, I thought this was a cool case of
different approaches combining their strengths to yield an algorithm more effective than
the individual parts.

Thanks for reading!
