---
title: Existence vs construction
date: 2024-07-17
tags: [misc]     # TAG names should always be lowercase
use_math: true
---

# Introduction
In everyday life, we show that things exist by producing an example. In order to believe that black sheep exist, I would need someone to show me one. To believe the claim that pigs can fly, I would need to actually observe a flying pig. If you can show me the thing, then I will believe it exists.

In mathematics, existence is a more abstract concept. Under certain assumptions about logic, a mathematical object can be proven to exist without ever being explicitly constructed or observed. For instance, we can show that a function $f$ has a root $x_0$ (this means that $f(x_0) = 0$) without knowing the concrete value for $x_0$. This can feel counterintuitive, but in the mathematical world, these two concepts are actually separate, with the task of construction often significantly more difficult than the task of existence.

In this post, we will take a high-level tour through a few examples of problems for which existence is much simpler than construction. Departing from our everyday intuition that existence and construction go hand in hand requires creativity and willingness to think outside the box. Without further ado, let's jump in!

# Testing for Convergence vs. Finding the Sum
One area in which we can readily observe the difference in difficulty between showing that something exists and constructing it concerns infinite sums. Typically, when we're dealing with infinite sums, there are two questions we care about:
1. Is the sum finite?
2. What is the sum? (This question only really makes sense if the answer to (1) is yes.)

The Basel Problem, posed in 1650 by Pietro Mengoli and solved by Euler in 1734, asks for the sum of the infinite series $S = \sum_{i = 1}^n \frac{1}{n^2}$. Showing that $S$ is finite -- i.e., that a finite sum exists -- simply requires observing that $n$'s exponent in the denominator (2) is greater than 1. Finding the sum however, requires a more elaborate argument which we won't spend time on here. Spoiler alert: $S = \pi^2 / 6$! What the irrational, transcendental $\pi$ has to do with this very integer-y sum is not obvious, but it's another story for [another post](https://www.jgindi.me/posts/euler-basel/).

Establishing the fact that $S$ is finite just required a simple test. Producing the actual sum, however, took almost 85 years! This is weird! Next, we'll look at an example from number theory where the difficulty of construction is the foundation upon which cybersecurity rests.

# Primality Testing vs. Integer Factorization
One of the most deeply studied parts of number theory -- the study of properties of the positive whole numbers -- is the prime numbers. A number $p$ is prime if its only divisors are $p$ and $1$. So how do we tell if a number is prime?

The simplest algorithm is to enumerate the numbers between $2$ and $p - 1$. If one of them divides $p$ evenly, the number is not prime. A slightly better way to do it is based on the fact that if $p = ab$, then unless $p$ is a perfect square, then either $a < \sqrt{p}$ and $b > \sqrt{p}$ or vice versa. Thus, instead of searching all the way up to $p$, we can search up to $\sqrt{p}$. But we're not done! Another observation we can make is that since, at bottom, all numbers are products of primes, we only need to search the _primes_ up to $\sqrt{p}$.

Much more (very sophisticated) work has been done improving these methods (and some of these improvements may at some point be the subject of another post), but the important thing to say here is that we now have algorithms that run relatively quickly for establishing the primality of a natural number. These efficient methods are often unlike the methods I mentioned earlier, since they do not rely on "trial divisions" (where we check different candidate divisors by dividing $p$ by them).

Suppose now that instead of deciding if a number is prime, we change the problem statement slightly to: Find the prime factors of $p$. While it seems like being able to efficiently answer the "Is it prime?" question should require finding the factors along the way, once again, this turns out not to be the case. As of now, there are no published "efficient" integer factorization algorithms, though there are algorithms that are "almost efficient". (Here, efficiency is a measure of how long it takes to factor an integer $n$ as a function of $n$'s size.)

Many of the cryptographic protocols responsible for securing data on the internet rely on the (likely) computational hardness of factoring numbers efficiently. If an efficient algorithm were to be found, the internet could become a far less safe place to entrust with our credit card information.

# The Probabilistic Method
The probabilistic method, often used when studying finite structures, is a very interesting and general technique that manages to cleverly prove that an object with certain (rare) properties exists without actually finding one. The method does this by using randomness in the following ingenious way. First, we construct a "random" instance of the object. Then, we show that the object has the desired property with nonzero probability. Since, if we sample an object at random, the "probability" of drawing the object with the properties we care about is
<div>
$$
    \frac{\text{# of configurations with property}}{\text{# of configurations}},
$$
</div>
if the probability is nonzero, it means there must be at least one instance that has our property. Without using the method to actually carry out a proof here, I'll set a problem up to give a "concrete" sense of a problem we can solve with this.

A graph is a collection of nodes connected to one another by edges. A _complete_ graph is a graph where every node is connected to all of the other nodes. If such a graph has $n$ nodes, then it has $n(n - 1) / 2$ edges. Examples of complete graphs on $n = 2, ..., 7$ vertices are shown below. We typically refer to the complete graph on $n$ vertices as $K_n$.

<img src="complete.png" alt="drawing" width="550" height="400"/>

An edge coloring is an assignment of colors from some set (e.g. {red, blue}). The question we care about here is: Given a complete graph with $n$ vertices ($K_n$) and an integer $r$ (where $r < n$), is it possible to color each edge either red or blue in such a way that no group of $r$ vertices ($K_r$) has monochromatic connecting edges?

(Read that again if you need to.)

More interesting than the proof itself (which produces a relationship between $n$ and $r$ for which such colorings exist) is the way the proof begins. We start by constructing a "random" graph where each edge is colored red or blue independently at random, and then make an argument about the probability that a coloring of the desired kind occurs.

One can imagine sitting down and drawing some small examples to try to gain an intuition for this problem. Maybe you decide that you'll draw a few examples for $n=5$ vertices (ten edges) or $n = 10$ vertices (45 edges). But then you realize that you need to test different values of $r$ also, and before you know it, it just becomes too difficult to gain intuition by proceeding directly. In this and many other cases, the probabilistic method comes to our aid and allows us to sidestep the hard problem of construction, and trade it in for the often easier problem of existence.