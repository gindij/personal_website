---
title: Fibonacci with difference equations
date: 2020-10-13
tags: [linear-algebra]     # TAG names should always be lowercase
use_math: true
---

## Introduction

The Fibonacci sequence is a mathematical object that has garnered much fascination over the centuries due to its simplicity and knack for rearing its head in all sorts of unexpected places. In this post I want to lead you to a different way of thinking about the sequence that is wholly unintuitive. You can only really arrive at it by using algebra in conjunction with some lucky guesses, as we’ll see. But before we dive into all that, let’s start with a bit of background.

At the beginning of the 13th century, Fibonacci first wrote about the eponymous sequence while pondering a sensible way to model the evolution of a rabbit population over time. As we will discuss below, the definition of the sequence is delightfully simple, yet it seems to show up in a bunch of different places, including:

* The way that branches of trees propagate in nature.
* Numbers of flower petals on certain kinds of flowers.
* The number of possible ancestors on a human $X$-chromosome.
* Any place you've heard about the golden ratio $\Phi$.

It is an object that is a delicious blend of simple and deep. So simple to play around with, in fact, that the reason I thought to write this post was actually the result of an interview that took an interesting (and fun!) turn.

One of my colleagues asked a candidate with some technical expertise to write some code that would do something with Fibonacci numbers. In response, the candidate cited this crazy-looking formula to my colleague without justification. After the interview, my colleague came back to our bay of desks interested as to whether I had ever seen the formula. When I said that I had, he asked if I knew how to prove that it was correct. While I didn't know how to on the spot, I vaguely recalled some concepts from a class I took during undergrad that I thought might help. Together with some googling, I put together what we will work through below. Looking at the work I had done once I finished, I decided that what we’re about to embark on is a cool example of the type of visibility math can give us into things we might not be able to see otherwise. Let’s go!

## Mathematical background

A sequence is just a list of finite, or even infinite, length. As examples, $1, 2, 3, 4, 5$ is a sequence, as are $2, 4, 6, 8, 10, \dots$ (the even numbers) and $2, 3, 5, 7, 11, \dots$ (the prime numbers). Each of these sequences have a first element, a second element, a third element, etc., so when we want to prove general assertions about sequences without explicitly writing down their elements, we denote the elements by $a_1$ (the first element), $a_2$ (the second element), $a_3$ (the third element), …. For the sequence $1, 2, 3, 4, 5$, we would say that $a_1 = 1$, $a_2 = 2$ and so on. Another important characteristic that usually comes along with sequences is some kind of rule that tells you how to come up with a given element. That is, many sequences  come with a formula, in terms of $i$, that can produce the value of $a_i$. In the case of the even numbers, that formula is $a_n = 2n$ (to compute the ith element, calculate $2 \times i$). To get the 3rd even number, compute $2 \times 3 = 6$. To get the 37th, compute $2 \times 37 = 74$.

The Fibonacci sequence is a recursive sequence. This means that it has a special kind of rule whereby the next term is defined in terms of the previous terms. The rule that defines the Fibonacci sequence is:
<div>
$$F_n = F_{n-1} + F_{n-2}.$$
</div>
In English, this says that $n$th element of the Fibonacci sequence is the sum of the two previous elements; the 100th element is the sum of the 99th (which is the sum of the 98th and 97th, and so on) and the 98th (which is the sum of the 97th and 96th, etc.). Each of the two previous elements is the sum of the two elements before them, and so on and so forth. But, you might say, this can’t go on forever, there has to be some bottom at which the recursion ends right? There is indeed! In order to define a recursive sequence, you need the first few elements (usually one or two) and a way of using previous elements to create new ones. In our case, we will set $F_0 = 0$ and $F_1 = 1$.

You’ll notice that in order to calculate the $n$th element of this sequence, you have to do a good deal more work than you do to calculate the $n$th even number. To get the $n$th Fibonacci number, you have to traverse the chain all the way back to the beginning for each element you want to calculate, while the even numbers have what is known as a closed form, i.e. the aforementioned formula in terms of $n$ that you can just plug $n$ into to get the desired element. The question we’ll tackle for the rest of the post is: Does the Fibonacci sequence have a closed form? In other words, is there a formula, in terms of $n$, that I can just plug into to get the $n$th element of the Fibonacci sequence?

## Using difference equations

First, let’s restate the problem. We want to find some formula for $F_n$ that satisfies $F_n = F_{n-1} + F_{n-2}$, or $F_n - F_{n-1} - F_{n-2} = 0$. Our first quantum leap is to guess that $F_n = m^n$ for some real number $m$ that we’re going to calculate. By guessing that $m^n$ is a solution, we are saying that $m^n - m^{n-1} - m^{n-2} = 0$. It’s easy to see that $m = 0$ satisfies this equation, but that’s boring, so let’s look for an $m \neq 0$.

Because we are now considering only nonzero values of $m$, we can divide $m^n - m^{n-1} - m^{n-2} = 0$ by $m^{n-2}$ on both sides so that we are now looking at values of $m$ that satisfy
<div>
$$m^2 - m - 1 = 0$$
</div>
(this is called the characteristic equation).

Using the quadratic formula you probably learned about sometime during middle school, we find two values of $m$ that work: the first is $\frac{1 + \sqrt{5}}{2}$ and the second is $\frac{1 - \sqrt{5}}{2}$. We will refer to these two values as $m_1$ and $m_2$ respectively. Recall that quadratic equations don’t always have real roots - sometimes they’re complex (i.e. they're of the form $a + bi$ where $a,b$ are real numbers and $i$ is $\sqrt{-1}$)! For our second and last quantum leap, we’re going to take for granted the fact that when you’re solving what’s called a (deep breath now) second order homogeneous difference equation with constant coefficients (which $F_n - F_{n-1} - F_{n-2} = 0$ is), if the characteristic equation has distinct real roots (in our case, if $m_1$ and $m_2$ are real and different from one another) then the solution to our difference equation (our formula for $F_n$) has the form
$$
F_n = Am_1^n + Bm_2^n
$$
where $A$ and $B$ are constants. We already found $m_1$ and $m_2$, so we just need to find $A$ and $B$. For this, we use the fact that we start the Fibonacci sequence at 0 and then 1, i.e. $F_0 = 0$ and $F_1 = 1$. Let’s use these to finish the problem. Using $F_0 = 0$, we have
<div>
$$
    F_0 = 0 = Am_1^0 + Bm_2^0 = A + B
$$
</div>
so that $B = -A$. Then we can use $F_1 = 1$ to see that
<div>
$$
    F_1 = 1 = Am_1^1 + Bm_2^1 = A \frac{1 + \sqrt{5}}{2} + B \frac{1 - \sqrt{5}}{2}.
$$
</div>
Because $B = -A$, this is the same as
<div>
$$
    1 = A \frac{1 + \sqrt{5}}{2} - A \frac{1 - \sqrt{5}}{2} = A\biggr(\frac{1 + \sqrt{5}}{2} - \frac{1 - \sqrt{5}}{2}\biggr) = A\sqrt{5}
$$
</div>
so $A = \frac{1}{\sqrt{5}}$ and $B = -A = -\frac{1}{\sqrt{5}}$. Thus,
<div>
$$
    F_n = \frac{1}{\sqrt{5}}\biggr(\frac{1 + \sqrt{5}}{2}\biggr)^n -\frac{1}{\sqrt{5}}\biggr(\frac{1 - \sqrt{5}}{2}\biggr)^n.
$$
</div>
Pulling out the $\frac{1}{\sqrt{5}}$, we have
<div>
$$
F_n = \frac{1}{\sqrt{5}}\biggr(\biggr(\frac{1 + \sqrt{5}}{2}\biggr)^n - \biggr(\frac{1 - \sqrt{5}}{2}\biggr)^n\biggr).
$$
</div>

## Implementation

Just to drive home why I think this is cool: we started out with a sequence defined by adding integers to each other in a pretty simple way and by using some techniques that feel kind of heavy and opaque, we waved a magic wand, $\sqrt{5}$ showed up and we produced a totally alien and unfamiliar, yet correct representation of the elements of the Fibonacci sequence. To check our work I wrote a short computer program that computes the first 10 fibonacci numbers:

(Note: the lines that start with # are not actually code, they are comments to help guide the reader.)
```python
def nth_fibonacci_number(n):
    # Given a number n as input, this function computes
    # the nth Fibonacci number

    # Reflects our assertion that F_0 = 0
    if n == 0:
        return 0

    # Reflects our assertion that F_1 = 1
    if n == 1:
        return 1

    # For any number n greater than or equal to 2, use the
    # formula we came up with. The symbol * is multiplication,
    # / is division, ** is exponentiation and math.sqrt takes a
    # square root. The below is one line, the "\" character is
    # just a python technicality.
    nth_fib = (1 / math.sqrt(5)) * ( ((1 + math.sqrt(5)) / 2) ** n \
     - ((1 - math.sqrt(5)) / 2) ** n)

    # Return the result
    return nth_fib
```

When I ran this code with $n = 0, 1, 2, 3, …, 9$, I got

<div>
\begin{align*}
F_0 &= 0\\
F_1 &= 1\\
F_2 &= 1\\
F_3 &= 2\\
F_4 &= 3\\
F_5 &= 5\\
F_6 &= 8\\
F_7 &= 13\\
F_8 &= 21\\
F_9 &= 34
\end{align*}
</div>

which is what you'd get if you went and calculated the first 10 Fibonacci numbers the usual way.

## Conclusion

In my opinion, what we did here is far less important than how we did it. We started with an object that appeared simple. We then pulled techniques out of the mathematical netherworld to twist it into something entirely unrecognizable -- even, dare I say, scary. Yet somehow, once we were done with the trickery and misdirection, we found something in our hand that… well… just worked. Why did it work? Why should it even exist? Because someone curious enough reached into the abstract and found it. It’s as simple as that.
