---
title: TSP is inapproximable
date: 2017-04-26
tags: [computer-science]     # TAG names should always be lowercase
use_math: true
---

## Introduction
As an introductory computer science student, I was enamored by the Traveling Salesman
Problem (if you’ve never heard of it, see [Travelling salesman problem -
Wikipedia](https://en.wikipedia.org/wiki/Travelling_salesman_problem)). It is very
easy to state and has very simple and important practical applications, yet somehow
my professors were telling me that we don’t, at present, have an efficient
algorithm to solve it. There are heuristics, yes, but you can (easily) design
pathological inputs on which whatever heuristic you design performs horribly.

## Approximation algorithm
It’s often the case that when faced with $NP$-hard optimization problems (such as
TSP), instead of heuristics, we try to design *approximation algorithms*, or
algorithms that provably produce outputs within some acceptable factor of the
answer. To be a bit more precise about it:
1. An algorithm $A$ is an $\alpha$-approximation for a maximization problem $P$ if on every instance $I$ of $P$, $A$ produces a solution of size at least $OPT/\alpha$ (where $OPT$ is the size of the optimal solution for the instance $I$).
2. $A$ is an $\alpha$-approximation for a minimization problem if $A$ produces a solution of size at most $\alpha OPT$.

Note that $\alpha \geq 1$.

## Inapproximability
There are myriad approximation algorithms out there for a bunch of different
problems; I may make them the subject of a future blog post… but in the remainder of
this post, I want to actually show that for any $\varepsilon > 0$, it is impossible
to come up with a $(1-\varepsilon)$-approximation for TSP unless $P = NP$ (in which
case we wouldn’t need approximations, we would have a polynomial time algorithm to
solve the problem exactly!). In other words, unless $P = NP$, not only do we not
have an efficient algorithm for TSP, we can’t even *approximate* it efficiently!
The proof of this fact, which I find surprising and kind of amazing given the
amount of effort and brainpower that have been thrown at this problem over the
years, is pretty simple, so I thought it would be fun to go through it here.

(Before we do, if you aren’t familiar with it, read [Hamiltonian path problem -
Wikipedia](https://en.wikipedia.org/wiki/Hamiltonian_path_problem))

## The proof
We'll start with a sketch of the proof, then move on to the actual proof.

**Proof idea:** The proof is by contradiction. We will assume we have a polynomial
time approximation for TSP and use it as a black box to solve a known $NP$-complete
problem, HAM-CYCLE, in polynomial time. Assuming $P \neq NP$, this is impossible, so
such an approximation cannot exist.

**Proof:** Suppose that $A$ is a polynomial time approximation for TSP. Use $A$ to
construct the following algorithm $A’$ which, on some input graph $G = (V,E)$,
computes a solution (a YES or NO) to HAM-CYCLE:

1. Create the graph $G’ = (V, E’)$ by completing $G$ (i.e. by adding edges to $E$
until there are edges between every pair of vertices in $V$).
2. Give the edges in $E$ weights of 0, and give those in $E’ - E$ weights of 1. (Note
that $G’$ is an instance of TSP.)
3. Use $A$ to approximate the least cost tour $T$ in $G’$.
4. Output NO if $T$ has weight $> 0$ and YES otherwise.

We just need to argue that $A$ outputs a tour of weight 0 if and only if there is a
hamiltonian cycle in $G$. To see this, note that by definition, $A$ finds a tour
whose combined weight is within some factor of the optimal tour on $G’$. If the
optimal tour on $G’$ can indeed be made up only of edges from $G$, it has weight 0,
in which case $A$ would have to return an answer within some factor of 0… namely 0.
If the A finds a tour with weight $> 0$, then it must not have been able to find a
tour using only edges of $G$, in which case we can safely output that there is no
hamiltonian cycle in $G$. **QED.**

## Conclusion
And with that, just a few short words, we were, assuming $P \neq NP$, able to rule
out all possible approximation schemes that anyone could ever think of! Imagine all
the time and effort we’ve saved!
