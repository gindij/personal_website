---
title: Euler's Identity
date: 2018-01-01
tags: [calculus,linear-algebra]     # TAG names should always be lowercase
use_math: true
---
## Introduction
In this post I want to show a few different ways of proving that $e^{i\theta} = \cos\theta + i\sin\theta$. It’s a cute illustration of how it’s often possible and rather cool to look at and solve problems in different ways.

## Approach 1
The first technique is one I encountered toward the end of my tenure as a Calc 2 TA last semester as I was going over Taylor and MacLaurin series with my students. If we look at the MacLaurin series for $\sin \theta$ and $\cos\theta$ are
<div>
\begin{align*}
\cos\theta &= 1 + \frac{\theta^2}{2!} + \frac{\theta^4}{4!} - \dots\\
\sin\theta &= \theta - \frac{\theta^3}{3!} + \frac{\theta^5}{5!} - \dots,
\end{align*}
</div>
so
<div>
$$i\sin\theta = i\theta - \frac{i\theta^3}{3!} + \frac{i\theta^5}{5!} - \dots$$
</div>

Now, let’s look at the MacLaurin series for $e^{i\theta}$. Because
<div>
$$e^x = 1 + x + \frac{x^2}{2!} + \frac{x^3}{3!} + \dots, $$
</div>
we have
<div>
\begin{align*}
e^{i\theta} &= 1 + i\theta + \frac{i^2\theta^2}{2!} + \frac{i^3\theta}{3!} + \dots\\
&= 1 + i\theta - \frac{\theta^2}{2!} - \frac{i\theta^3}{3!} + \dots
\end{align*}
</div>
which, miraculously, is exactly what we get if we interleave (sum up) the terms of $\cos\theta + i\sin\theta$.

## Approach 2
The next approach uses techniques from a first course in calculus. We first observe that if $e^{i\theta}$ is going to be the same as $\cos\theta + i\sin\theta$, then the fraction
$\frac{cos\theta + i\sin\theta}{e^{i\theta}} = (\cos\theta + i\sin\theta)e^{-i\theta} = 1$. We will show that the second 
equality holds. 

To do this, first define
$f(\theta) = (\cos\theta + i\sin\theta)e^{-i\theta}$. Next, we take the (rather annoying) derivative of $f(\theta)$
<div>
\begin{align*}
f’(\theta) &= e^{-i\theta}(-\sin\theta + i\cos\theta) + -ie^{-i\theta}(\cos\theta + i\sin\theta)\\
&= -e^{-i\theta}\sin\theta + ie^{-i\theta}\cos\theta +-ie^{-i\theta}\cos\theta + e^{-i\theta}\sin\theta\\
&= 0\dots
\end{align*}
</div>
If $f’(\theta) = 0$ at all values of $\theta$, $f$ is constant! Which constant? Let’s plug in $\theta = 0$ and find out!
<div>
$$f(0) = (\cos 0 + i\sin 0) e^{-0i} = (1 + 0)(1) = 1$$
</div>
If $f$ takes the value 1 when $\theta = 0$ and $f$ is constant, it must take the value 1 everywhere. To sum up, we have
<div>
$$f(\theta) = (\cos\theta + i\sin\theta)e^{-i\theta} = 1.$$
</div>
Rearranging the second equality by cross multiplying, we see that 
Euler’s identity holds.

## Approach 3
The last technique is my favorite. It uses a bit of linear algebra in concert with differential equations to produce what I think is the most illuminating proof of Euler’s identity. First, consider the differential equation
<div>
$$f’’ = -f.$$
</div>
A differential equation is what is a kind of functional equation. 
Rather than trying to find the value of a real-valued variable,
we are trying to find a function whose derivatives satisfy a given relationship. In this case, we want to find a function $f$ such that
$f$'s second derivative is the same as $f$.

We first note that $\cos\theta$ and $\sin\theta$ are both solutions to this equation:
<div>
\begin{align*}
(\sin\theta)’’ &= -\sin\theta \\
(\cos\theta)’’ &= -\cos\theta.
\end{align*}
</div>
Because our differential equation involves second derivatives, it's 
solutions are sort of analogous to those of a quadratic equation: 
which is to say, there are two! Formally, we say that the solution 
space is a vector space of dimension 2. If $\sin\theta$ and 
$\cos\theta$ are linearly independent solutions, they form a basis 
of the solution space, which means that every solution to our 
differential equation must can be written in the form the form 
$a\cos\theta + b\sin\theta$ for some constants $a,b$. 

To see that $\sin\theta$ and $\cos\theta$ are indeed linearly 
independent, suppose that for all  $\theta \in [0,2\pi]$, 
$a\cos\theta + b\sin\theta = 0$. Let’s pick a particular $\theta$ 
value in this interval. If $\theta = \pi/2$, then we have
$a \cdot 0 + b \cdot 1 = b = 0$ (where the $=0$ at the end is 
because we supposed that $a\cos\theta + b\sin\theta = 0$ for all 
$\theta \in [0,2\pi]$). If we pick another, say $\theta = 0$, we 
have $a \cdot 1 + b \cdot 0 = a = 0.$

So far, we've shown that if $a\cos\theta + b\sin\theta = 0$, then 
we know that $a = b = 0$. This constitutes a proof that 
$\sin\theta$ and $\cos\theta$ are linearly independent. Because
we said the solution space is of dimension 2, they are a basis for 
the solution space to our original equation.

We can separately observe that $e^{i\theta}$ is also a solution to our equation because
<div>
\begin{align*}
(e^{i\theta})’ &= ie^{i\theta}\\
(e^{i\theta})’’ &= -e^{i\theta}.
\end{align*}
</div>

Because $\sin\theta$ and $\cos\theta$ form a basis, we know that
<div>
$$e^{i\theta} = a\cos\theta + b\sin\theta.$$
</div>
for some yet unknown constants $a,b$. Now it just remains to figure out what $a$ and $b$ are. (Can you see what they should be?) 

To find $a$ and $b$, we note that if $f(\theta) = e^{i\theta}$, 
then $f(0) = 1$ and $f’(0) = i$. Using the first condition, we have
<div>
$$1 = a \cdot 1 + b \cdot 0 = a.$$
</div>
Using the second (taking the derivative of both sides before plugging 0 in), we have
<div>
$$i = -a\sin 0 + b\cos 0 = 0 + b = b. $$
</div>
Putting these both together, we have
$e^{i\theta} = \cos\theta + i\sin\theta,$
which is what we wanted.
