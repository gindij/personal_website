---
title: How many infinities are there?
date: 2020-04-05
tags: [infinity]
use_math: true
---

(This post assumes you've read, at least, [this](https://www.quora.com/q/iqjtzyaumcdpirqz/Big-and-bigger-Part-1-one-to-one-correspondences) and [this](https://www.blogger.com/blog/post/edit/754026028311034636/3224454722535965349#).)

All of the posts on infinity that I've written to this point have pointed to two different infinite sizes, or cardinalities. The first is the countable kind, the kind we associate with the natural numbers $\mathbf{N}$. The other is the kind we associate with the real numbers, $\mathbf{R}$; we call $\mathbf{R}$ an uncountable set.

Before we proved that $\mathbf{R}$ has a different size than $\mathbf{N}$, we made a convincing intuitive
case that there is really only one kind of infinity. That infinities come in at least two varieties was one
of many counterintuitive, foundational results Cantor added to the foundation of rigorous transfinite
mathematics. But there is yet another question we have still not answered: Are there more than two
transfinite cardinalities, or do all infinite sets have either $|\mathbf{N}|$ or $|\mathbf{R}|$ elements?

This post concerns a result that Cantor proved in the same 1891 paper in which his diagonalization argument for the uncountability of the reals appears. It is called Cantor's Theorem, and it shows that there are actually infinitely many infinities. What does that mean? How is that possible? Let's dive in and see.

Before we get into it, we establish a small amount of notation. If $a$ is an element of $A$, we write $a \in
A$. The size of the set $A$, or $A$'s cardinality, is denoted $|A|$. The power set of a set $A$, denoted
$P(A)$, is the set of all subsets of $A$. For example, the power set of $A = \\{1, 2\\}$ is $P(A) =
\\{\emptyset,\\{1\\}, \\{2\\}, \\{1, 2\\}\\}$, where $\emptyset$ is the empty set. There is actually a
straightforward argument that shows that if $|A| = n$, then $|P(A)| = 2^n$.
It goes like this. Each element $a$ in $A$ is either in or not in each subset of $A$. Thus, constructing a
subset requires $n$ choices, each of which is between two options ("in" or "not in"); the number of such
subsets is thus $2 \times \dots \times 2 = 2^n$.

Cantor's Theorem is quite compact. It says that $|P(A)| > |A|$; in English, the number of subsets of $A$ is
strictly greater than the size of $A$. But based on what we just showed, shouldn't this be obvious? For any
$n \geq 0$, $2^n > n$, after all. What is so revolutionary or helpful about Cantor's Theorem? As is often the
case with theorems in set theory, the subtlety stems from having to extend the result to infinite sets.

The beauty of Cantor's Theorem is that with one elegant argument, Cantor proves the above for any set,
finite, countably infinite, or uncountably infinite. The way he does it is by using a proof technique called
proof by contradiction. The technique consists of the following steps:

Assume the opposite of what you want to show.
Show that the proposition from (1) leads to some absurdity.
The assumption that led to the absurdity must be false, so the opposite (your original claim), must be true.

The proof proceeds as follows. First, we assume that $|A| = |P(A)|$. In order to handle infinite sets, this
means that we are assuming that each element $a \in A$ can be matched with exactly one element $S_a$ of
$P(A)$ (a subset of $A$) with none left over. For each element-subset pair, $(a, S_a)$, either $a \in S_a$ or
$a \notin S_a$. Now consider the set of $b \in A$ for which $b \notin S_b$. That is, consider the set of all
elements that are not contained in the sets they map to; because this group of elements is a subset of $A$,
we can refer to it as $S_c$ for some $c \in A$. We now have to answer a simple question: Is $c \in S_c$?

If $c \in S_c$, then $c$ is an element that is not in the set it maps to, namely $S_c$... which is absurd.
But then surely, $c \notin S_c$... right? If $c \notin S_c$, though, it means that $c$ is an element that is
in the set it maps to, namely $S_c$. But if it is in $S_c$, we get into the same pickle we were in in the
first case. Thus, $c$ cannot be in $S_c$ and $c$ cannot not be in $S_c$. We have thus reached our desired
contradiction! Our assumption, that $|A| = |P(A)|$, must be false.

We're not quite done, though. We've shown that $A$ and $P(A)$ are not the same size, but which is bigger?
Well the $S_c$ that did not have a matching $c$ was a member of $P(A)$, so we must have $|P(A)| > |A|$,
proving the theorem.

Cantor's Theorem answers this post's original question. Can you see how? It says that if you take any
infinite set $A$, its power set $P(A)$ furnishes a larger infinity. But then we get an even larger infinity
when we form $P(P(A))$, and a yet larger one when we consider $P(P(P(A)))$.

If two infinities weren't enough, now you've got as many as you like.
