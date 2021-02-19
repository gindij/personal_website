---
title: Two approaches to one problem
date: 2018-09-16
tags: [combinatorics]     # TAG names should always be lowercase
use_math: true
---

## Introduction
In non-mathematical disciplines, it is very often the case that approaching the same question in different ways will lead you to different conclusions. One of the beautiful qualities of math and the type of reasoning it requires is that for any given problem, there might be (and usually is) a plethora of different approaches. The discovery of a new approach adds perspective about the problem and allows the solver’s understanding of a particular area to deepen and expand.
In helping my cousin with some homework recently (she figured out one of the solutions below before I did), I came across a wonderful example of the way different approaches inform each other. One is far more intuitive than the other, but I didn’t realize there was a more intuitive solution until after solving it in a more complex, algebraic way.
Without further ado, let’s begin.

## The problem
Let’s say you’ve got a class of 5 students, and you have them arrange themselves as follows. You tell them to stand in a circle and you give each pair of them a string to hold tight between them. If people are dots and the strings between them are the line segments, the arrangement might look like this:

<img src="k5.png" alt="drawing" width="300" height="300"/>

The question we want to answer is:**How many intersections are there in the middle of the circle?**
For the case when there are 5 students, the answer, by inspection of the picture above, is 5 (the vertices of the pentagon in the middle). For the remainder of this post, we will concern ourselves with the more general version of this question, which is: If there are $n$ people, can we find a formula -- in terms of $n$ -- that tells us the number of intersections in such an arrangement?

The remainder of this post will meander through two approaches; the first algebraic, and the second intuitive.

## Algebraic approach
The first thing to do to make the problem more approachable is to simplify it. In this case, we will narrow our focus to the number of intersections that the strings emanating from *one* person are a participant in. If we can do that, we can multiply by the number of people there are* and divide by the number of times we overcount each intersection, and voila, problem solved.
At this point we observe two simple facts:

1. There are a total of $n$ people.
2. Each intersection is going to be counted 4 times. Why? Because each intersection requires two strings, each of which has two people holding the ends. Thus, once I’ve come up with my formula for the number of intersections per person, I have to remember that simply multiplying the result by $n$ is going to count each intersection 4 times, so we need to divide the total number of intersections we find by 4.

From (1) and (2), we can deduce that our answer is going to have the form:
<div>
$$\frac{n}{4} \cdot \text{intersections per person}$$
</div>

The last thing we need to do is figure out the way to express the number of intersections per person. We will derive this in steps:
1. Each string that crosses the middle of the circle splits the circle into two groups. For simplicity, let’s call them the right-hand and left-hand groups.
2. The number of edges that intersect the splitting string is exactly the number of strings that pass from the right-hand group to the left-hand group.
3. Let’s say that there are $k$ people in the right-hand group. Because there are 2 people holding the splitting string, the left group must have the remaining people, i.e. $n - k - 2$. Each of the $k$ people in the right group shares a string with each of the people in the left group, so the number of strings that cross the splitting string is given by $\text{(# of people in right group)} \cdot \text{(# of people in left group)} = k (n - k - 2)$.
4. We now just have to treat each string that a person is holding as a splitting string, use the formula from (3) and add the results up.

If you understand steps (1)-(4), the rest is just boring, but unfortunately necessary algebra. If you’re new to discrete math, you should try to work this out yourself; there are some summations in there that you’ll encounter over and over again and it’s a good way to get familiar with some of them. If algebra isn’t your thing, trust me that this approach works and skip to the intuitive approach below.

The goal now is to simplify
<div>
$$\sum_{k=1}^{n-3} k(n-k-2).$$
</div>
(We will multiply by $n/4$ at the end.) Our first step will be to distribute the $k$ to get:
<div>
$$\sum_{k=1}^{n-3} nk - k^2 - 2k.$$
</div>
From now on, I’m going to write the summation without the indices (because typing them is tedious), but they’re implied. Splitting up into three summations, we have:
<div>
$$\sum nk - \sum k^2 - \sum 2k.$$
</div>
Pulling out constants, this is equal to
<div>
$$n\sum k - \sum k^2 - 2 \sum k = (n-2)\sum k - \sum k^2.$$
</div>
We know (but if you don’t, try to prove it yourself), that $\sum_{i=1}^m i = m(m+1)/2$. Once you’ve proven that, you can use a similar method to prove that $\sum_{i=1}^m i^2 = m(m+1)(2m+1)/6$. Substituting these in and recalling that $m$ in our case is $n - 3$, we have
<div>
$$\frac{(n-2)(n-3)(n-3+1)}{2} - \frac{(n-3)(n-3+1)(2n-6+1)}{6}$$
</div>
Simplifying that monster,
<div>
\begin{align*}
  \frac{3(n-2)(n-3)(n-2) - (n-3)(n-2)(2n-5)}{6}
  &= \frac{(n-3)(n-2)(3n - 6 - (2n - 5))}{6} \\\\
  &= \frac{(n-3)(n-2)(n-1)}{6}.
\end{align*}
</div>
Multiplying that by $n/4$, we end up with the surprisingly neat formula for the total number of intersections:
<div>
$$\frac{n(n-1)(n-2)(n-3)}{4!}.$$
</div>
For those who have seen this formula before, this is exactly the number of ways to choose a group of 4 people from a population of size $n$. Once I had spent all this time doing all that algebra, it occurred to me that there had to be an easier way to articulate the solution...

## Intuitive approach
The simple way to solve the problem is to use one observation (that we actually made earlier): each intersection corresponds exactly with a single group of four people. If you draw the picture for $n = 4$, you’ll clearly see the single intersection. This observation finishes the problem for us… Can you see how?
If we know that each intersection corresponds with exactly one group of four people, that means the total number of intersections must be exactly equal to the number of groups of four people you can choose from a group of size $n$, i.e. $\frac{n(n-1)(n-2)(n-3)}{4!}$.

## Conclusion
Neither of the above approaches is better than the other, per se. Each solution requires its own set of deductions and observations, the collection of which leave you with a richer understanding of how you might solve similar problems in the future. In this case, it didn’t even occur to me that there might be a simpler solution until I had already worked hard to find a more involved approach. Enjoy the process! The things you end up understanding the most deeply are the things you can think of and explain from a bunch of different angles.
Thanks for reading!

*This is a nice example of a phenomenon in mathematics called symmetry. We say that an object is symmetric under some transformation $T$ if the object doesn’t change when you apply $T$ to it. In our case, notice that in the picture at the beginning of the post ($n = 5$), if you calculate the solution for any particular person $p_1$, you have solved it for all of the others -- just rotate the next person you want to solve it for into $p_1$’s position and notice that the problem you’re solving for the new person is of exactly the same form as the one you solved for the first person!
