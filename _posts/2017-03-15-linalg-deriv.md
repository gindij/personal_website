---
title: The derivative via linear algebra
date: 2017-03-15
tags: [linear-algebra]     # TAG names should always be lowercase
use_math: true
---

## Introduction
As a math major in college, I had been, for a long time, been under the impression
that calculus and algebra were totally separate parts of math. The types of problems
you thought about in one of them were totally disjoint from the types of problems you
tackled in the other. Continuous vs. Discrete. Algebraic this vs. Analytic that.
As I was watching (a wonderful) [video series](https://www.youtube.com/watch?v=fNk_zzaMoSs&list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab) on linear algebra
by 3blue1brown, I came across the following really cool connection between
calculus and algebra that
was simple, elegant and clever. But, more importantly, it spectacularly illustrates
the connections that one finds between seemingly separate parts of math.
Let’s take a look at finite polynomials.

## Polynomials
Polynomials are mathematical objects of the
form $p(x) = a_0 + a_1x + a_2x^2 + \dots + a_nx^n$, where the $a_i$ are scalars drawn
from a field $F$ and $n$ is an arbitrary natural number. It’s easy to check that
polynomials actually make up a vector space over $F$. Formally, this means that:
1. Polynomials make up a commutative group under addition.
    1. There’s an identity element ($p(x) = 0$ is the identity).
    2. Every element has an inverse (the inverse of $p(x)$ is $-p(x)$).
    3. Addition is associative ($p(x) +(q(x) + r(x)) = (p(x) + q(x)) + r(x)$)
    4. Adding two polynomials always produces another polynomial (this property is
       called closure).
2. If you multiply a polynomial by a scalar, the result is yet another polynomial.
3. Scalar multiplication distributes over polynomial addition (if $p$ and $q$ are
    vectors and $c$ is a scalar, the three must satisfy $c(u + v) = cu + cv)$.
4. Vector multiplication distributes over scalar addition (if $c_1$ and $c_2$ are
    scalars and $v$ is a vector, they must satisfy $(c_1 + c_2)v = c_1v + c_2v$).
5. There must be a multiplicative identity ($p(x) = 1$).
6. If $c_1$ and $c_2$ are scalars and $p$ is a polynomial, then $c_1(c_2v) =
(c_1c_2)v$.

I’m going to skip verifying these, but if you think about them, they’re mostly (if
not all) sort of intuitive. For the rest of the post, we are just going to assume
that polynomials make up a vector space.

## Calculus detour
Let’s jump over to calculus for a minute. Do you remember how we differentiate a
polynomial? For example, if $p(x) = 3x^2 + x + 7$, what is $D(p(x))$?
If we recall our first calculus course, we remember that we were told that we could
differentiate each of $3x^2$, $x$ and $7$ separately and then add the results
together. Furthermore, we have two differentiation rules that will help us
differentiate a single term:
1. $D(x^n) = nx^{n-1}$.
2. $D(cf(x)) = cD(f(x))$ (you can pull out constants).

With these rules in hand, we see that the derivative of $3x^2$ is $3 \cdot 2x = 6x$,
the derivative of $x$ is 1 and the derivative of 7 (or any other constant, for that
matter) is 0. Adding these together, we conclude that $D(p(x)) = 6x + 1$. Okay,
now reread the calculus we just thought through and keep it in mind; we have to jump
back to linear algebra for a second.

## Differentiation is a linear map
If I have two vector spaces $V$ and $W$ over a field $F$, then a map $T:V \to W$ is
said to be linear if:
1. $T(u + v) = Tu + Tv$.
2. $T(cu) = cTu$ (for $c \in F$).

The first rule says says applying a linear transformation to a sum of vectors should
produce the same result as if you applied the transformation to each result and then
added them in the target space. This looks kind of familiar, doesn’t it? Above, when
we computed $D(p(x))$, we took $p(x)$ apart, applied $D$ to each part, and then put
the results back together… In other words, we said that
<div>
$$D(p(x)) = D(3x^2) + D(x) + D(7).$$
</div>

It’s easy to see that this rule, whereby we are allowed to decompose things, work on
them, and put them back together, generally applies to the differentiation of any
polynomial, so we’ve established that the polynomial differentiation operator $D$
satisfies the first property of linear maps!

Furthermore, if we look at the second differentiation rule that helped us up above,
it is exactly the second property of linear transformations! (Just replace $T$ with
$D$ and $u$ with some polynomial $p(x)$.)

We thus see that the operator $D$, which takes the derivative of a polynomial, is
linear!

To sum up what we’ve said so far:
* The space of polynomials is a vector space (we will henceforth call $P$).
* The differentiation operator, $D$, is a linear transformation from $P$ to itself
  (because differentiating a polynomial always gives another polynomial).

Thus, once we produce a convenient basis for $P$, we can actually write down a
matrix that will do differentiation of polynomials for us! But what basis should we
use?

Because polynomials in $P$ can have arbitrarily large degree, our basis will
actually be infinite. The basis we choose is actually inherent in the general
structure of polynomials. Can you see what it might be?
Because polynomials of degree $n$ are just linear combinations of the infinite list
$\{1, x, x^2, \dots\}$ (e.g. $3x^2 + 4x + 3$ can be seen as $3 \cdot 1 + 4 \cdot x +
3 \cdot x^2 + 0 \cdot x^3 + 0 \cdot x^4 +\dots$), we will call this set our
basis (verify span and linear independence!) and now use it to write down a(n
infinite) matrix corresponding to $D$.

Note that the $i$th column of a matrix describes what the transformation does to the
$i$th basis vector of our space. So, in order to write down the first column of
$D$’s matrix, we need to know what $D(1)$ is written as in terms of $P$’s basis
vectors. Well, if $D(1) = 0 = 0 \cdot 1 + 0 \cdot x + \dots$, then the first column
of our matrix must be
<div>
$$\begin{bmatrix} 0 \\ 0 \\ 0 \\ \vdots \end{bmatrix}.$$
</div>

To determine the next column, we look at what $D$ does to our second basis vector,
$x$. $D(x) = 1 = 1 \cdot 1 + 0 \cdot x + \dots$, so the second column of our matrix
would look like
<div>
$$\begin{bmatrix} 1 \\ 0 \\ 0 \\ \vdots \end{bmatrix}.$$
</div>
The last basis vector we need to look at before we can intuit the rest of the
columns is $x^2$. $D(x^2) = 2x = 0 \cdot 1 + 2 \cdot x + 0 \cdot x^2 + \dots$, so
the third column is
<div>
$$\begin{bmatrix} 0 \\ 2 \\ 0 \\ \vdots \end{bmatrix}.$$
</div>
You could probably guess the next column, and the one after that, and, most
probably, all of the ones after that… we finally have this matrix for $D$ (note that
    it’s infinite):
<div>
$$A = \begin{bmatrix} 0 & 1 & 0 & 0 & \dots \\ 0 & 0 & 2 & 0 & \dots \\ 0 & 0 & 0 & 3
& \dots \\ \vdots & \vdots & \vdots & \vdots\end{bmatrix}.$$
</div>
If you represent a polynomial as a(n infinitely long) vector of its coefficients,
then you can actually do differentiation with this matrix. For example, if your
polynomial was $p(x) = 4x^3 + 5x^2 + 29x + 9$, you would perform
<div>
$$A\begin{bmatrix} 9 \\ 29 \\ 5 \\ 4 \\ 0 \\ \vdots \end{bmatrix} = \begin{bmatrix}
29 \\ 10 \\ 12 \\ 0 \\ 0 \\ \vdots \end{bmatrix},$$
</div>
i.e. your derivative is $12x^2 + 10x + 29$, which, using the rules you learned in
calculus class, is demonstrably correct.

## Conclusion
To sum up, we’ve reconceived the space of polynomials as a vector space and used
notions from both linear algebra and calculus to come up with a pretty nice looking
matrix that doesn’t intuitively look like differentiation, but that somehow
perfectly describes it when you look at it through the right lens. There are
connections like these all over mathematics, you just have to know where to look.
