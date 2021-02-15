---
title: Fibonacci with linear algebra
date: 2020-10-27
tags: [linear-algebra]
use_math: true
---

## Introduction

After writing a post about one interesting way to arrive at a closed-form for the $n$th term of the Fibonacci
sequence, a friend pointed out a few alternative ways to get there, one of which felt particularly natural.
It requires some linear algebra, so I guess that in some sense, it could be considered "unnatural," but I
especially like it because the argument's flow requires fewer arbitrary-seeming leaps. With that said, this
post will be brief (as I'm a little busy at the moment), so if there's anything that doesn't make sense or is
incorrect, please do reach out and let me know.

## Fibonacci matrix

The Fibonacci sequence can be viewed through the lens of matrices. In particular, if we start with the matrix
<div>
$$
A = \begin{bmatrix} 1 & 1 \\ 1 & 0 \end{bmatrix},
$$
</div> we can see that the Fibonacci sequence can be materialized by repeatedly multiplying $A$ by itself.

To see this, first notice that if we are considering the Fibonacci sequence that starts with $F_0 = 0$ and
$F_1 = 1$, then we note that
<div>
$$
A = \begin{bmatrix} F_2 & F_1 \\ F_1 & F_0 \end{bmatrix}.
$$
</div>
Suppose now that
<div>
$$
A^{n-1} = \begin{bmatrix} 1 & 1 \\ 1 & 0 \end{bmatrix}^{n-1} = \begin{bmatrix} F_{n-1} & F_{n-2} \\ F_{n-2} &
F_{n-3} \end{bmatrix}
$$
</div> for $n \geq 3$. Then
<div>
$$
A^n = A^{n-1}A = \begin{bmatrix} F_{n-1} + F_{n-2} & F_{n-2} + F_{n-3} \\ F_{n-2} + F_{n-3} & F_{n-2}
\end{bmatrix} = \begin{bmatrix} F_n & F_{n-1} \\ F_{n-1} & F_{n-2} \end{bmatrix}.
$$
</div>
Thus, to get the $n$th element of the Fibonacci sequence, we need only get the upper left entry of $A^n$. If
we had a fast way to obtain $A^n$ instead of actually carrying out iterated matrix multiplication, we could
obtain the $n$th element without doing very much work.

## Diagonalizing $A$

To do this, we will look to diagonalize $A$. This means that we will try to write
<div>
$$
A = VDV^{-1}
$$
</div> where $D$ is a diagonal matrix and $V$ is a matrix with eigenvectors of $A$ as columns (we have to
actually find $D$ and $V$ that work). What's nice about a diagonal representation is that
<div>
$$
A^n = (VDV^{-1})^n = VDV^{-1}VDV^{-1}\dots VDV^{-1} = VD^nV^{-1}.
$$
</div>
If $D$ is written as a matrix with $\lambda_1,\dots,\lambda_n$ on the diagonal, then $D^n$ is simply $D$ with
the elements on the diagonal each taken to the $n$th power, like so:
<div>
$$
D = \begin{bmatrix} \lambda_1 & 0 \\ 0 & \lambda_2 \end{bmatrix} \implies D^n = \begin{bmatrix} \lambda_1^n &
0 \\ 0 & \lambda_2^n \end{bmatrix}.
$$
</div>

Now that we've laid out our approach, it's time to carry it out. (I will spare the algebra required to keep this post brief.)

## Using $A$'s eigenvalues

The eigenvalues of $A$ (as you might expect if you read the earlier post about Fibonacci) are
$\lambda_1,\lambda_2 = \frac{1 \pm \sqrt 5}{2}$, and the corresponding eigenvectors are $v_1, v_2 =
\begin{bmatrix} \frac{1 \pm \sqrt 5}{2} \\ 1 \end{bmatrix}$. Thus, we have
<div>
$$
D = \begin{bmatrix} \frac{1 + \sqrt 5}{2} & 0 \\ 0 & \frac{1 - \sqrt 5}{2} \end{bmatrix} ~~~~ V =
\begin{bmatrix} \frac{1 + \sqrt 5}{2} & \frac{1 - \sqrt 5}{2} \\ 1 & 1\end{bmatrix}.
$$
</div>
With these in hand, we see that $A$ is indeed diagonalizable, so that $A^n$ can be written as $VD^nV^{-1}$,
and voila! with two matrix multiplications and exponentiating the diagonal entries of $D$, we can very
quickly and efficiently come up with large Fibonacci numbers quickly.
