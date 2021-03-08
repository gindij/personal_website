---
title: The Cantor set
date: 2017-10-27
tags: [infinity]     # TAG names should always be lowercase
use_math: true
---

## Introduction
In this post, I want to talk about a mathematical construct I read about last night that is just downright fascinating. It’s a great example of the way math can help us make sense of the otherwise-opaque. I give you: the Cantor Set!

## The Cantor set
Let $C_0$ be the interval $[0,1]$. Now remove the middle third of the interval to obtain $C_1 = [0, \frac{1}{3}] \cup [\frac{2}{3}, 1]$. Next, remove the middle thirds of the intervals leftover in $C_1$ to construct
$C_2 = ([0, \frac{1}{9}] \cup [\frac{2}{9}, \frac{1}{3}]) \cup ([\frac{2}{3}, \frac{7}{9}] \cup [\frac{8}{9}, 1])$
Iteratively continue to remove thirds from the remaining intervals. Doing this, we get a sequence of unions of closed intervals $C_0, C_1, C_2, \dots$. The Cantor Set $C$ is defined as the intersection of the $C_i$; mathematically, we write
$C = \cap_{i=0}^\infty C_i$.
Visually, the $C_i$ look like

![](cantor_set.jpg)

where the topmost line is $C_0$, the second line is $C_1$, and so on.

## At infinity
The rest of this post will be spent trying to understand how $C$ is composed. What is left in it after an infinite sequence
of cuts?

First notice that 0 and 1 are not deleted during any stage of the process. Generalizing this point, we can see that if some
value in $[0,1]$ is an endpoint of some interval at some point during our chain of cuts, it never gets removed. For
example, when we delete the middle thirds of the left and right parts of $C_1$ to obtain $C_2$, observe that we don’t touch
$0, \frac{1}{3}, \frac{2}{3}$, or $1$. Formally, we would argue that if $x$ is an endpoint of $C_i$, we know two things:
1. $x \in C_k$ for $k \leq i$.
2. $x$ is not removed during the construction of any of the other $C_k$ for $k > i$.
Thus, $x$ is in every one of the $C_i$ so by definition of $C$ (as the intersection of the $C_i$), $x \in C$.
Is there anything else in $C$? If the only numbers left are endpoints of intervals, then $C$ would be a subset of
$\mathbb{Q}$ and we would thus conclude that $C$ is countable.

More on this in a minute. One way we might try convince ourselves that there is indeed not much else in $C$ beside
“endpoints” is to think about how much of the interval $[0,1]$ is left once we’ve made all of our cuts. To do this, we just
need to think about how much we delete on each pass. On the first pass, we delete one interval of length $1/3$ ($1/3^1$).
On the second, we delete 2 intervals of size $1/9$ ($1/3^2$). On the third, we delete, 4 intervals of size $1/27$ ($1/
3^3$). Generalizing this pattern, we see that on the $i$th iteration, we cut $2^{i-1}$ intervals, each of size $1/3^i$. To
count up how much length we cut, we just need the sum of
<div>
$$\frac{1}{3} + 2\biggr(\frac{1}{9}\biggr) + 4\biggr(\frac{1}{27}\biggr) + \dots + 2^{i-1}\biggr(\frac{1}{3^i}\biggr) + \dots = \frac{1}{3}\sum_{i = 1}^\infty \
\biggr(\frac{2}{3}\biggr)^{i-1}$$
</div>

The series is geometric with ratio less than 1, so the sum evaluates to
<div>
$$\frac{1}{3}\biggr(\frac{1}{1 - \frac{2}{3}}\biggr) = \frac{1}{3}\cdot 3 = 1.$$
</div>
But that’s kinda odd… we started with an interval of length 1, and have cut out… all of it? (Mathematically, we say that
$C$ has zero length.)

## So is it countable?
At this point, you (as I did) probably thought that the buck stopped here. As expected, $C$ is sparse and small, probably
even countable. As with many things in set theory, there is a bit more depth yet to investigate. As our final act, we’re
going to show that not only is $C$ not sparse, it’s actually uncountable!

To do this, we are going to take a preliminary result for granted, namely that the set of all infinite sequences of 0s and
1s is uncountable. (If you’re feeling adventurous, take a stab at proving this yourself. If you’re feeling a little less
adventurous but you’re still in the mood for a challenge, a hint is that the proof is a diagonalization argument much like
Cantor’s proof that the real numbers are uncountable.)

## The one-to-one correspondence
We now construct a one-to-one correspondence between sequences of 0s and 1s and elements of $C$. For each element $c \in
C$, define $a_i$ — $i \geq 1$ — to be 0 if $c$ falls in the left part of $C_i$, and 1 if it falls in the right part. (Note:
if $c$ is in the left part of $C_{i-1}$, the “left” and “right” parts of $C_i$ refer to the left and right parts that
result when we cut out the middle third of the left part of $C_{i-1}$.)

Read the sentence in parentheses over again. It is written with unfortunately confusing language but it’s crucial
understanding the construction.

To see that this is actually a one-to-one correspondence, note that given a sequence of 0s and 1s, we can “follow” the sequence to pinpoint the exact, unambiguous element of $C$ that the particular sequence represents. Conversely, the construction of the sequence from two paragraphs ago gives us a way to take an element of $C$ and come up with a unique sequence by looking at exactly where the element falls with respect to each of the $C_i$.
Given that the set of infinite sequences of 0s and 1s is uncountable, this means that $C$ is actually uncountable too!

## Conclusion
When I first read this, my mind was literally blown. Isn’t it amazing?! We’ve somehow come up with a way to remove all of the *length* from an interval without diminishing its *size* in the least! By taking our original inquiry and injecting our investigation with a rigorous mathematical approach, we took $[0,1]$, which has length 1, removed all of its length via our construction of $C$, and yet somehow didn’t affect its cardinality.

Infinities don’t always play nice, but that’s why we love them.
