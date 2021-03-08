---
title: The Monty Hall paradox
date: 2018-05-24
tags: [statistics]     # TAG names should always be lowercase
use_math: true
---

## Introduction
The field of probability is rife with counterintuitive results that show how necessary the rigor of mathematics is to
correct understanding of certain situations. This post will be about the Monty Hall Problem. It isn’t hard to state, but
the result is somewhat subtle, so I thought it’d be fun to write about.

## The problem
The parlor-trick version of the problem goes as follows: You are on a game show and in front of you are three doors
(labeled 1, 2 and 3). Two conceal goats and one hides a car. The car has a 1/3 probability of initially being behind each
door. Your host, Monty, knows which door the car is behind. If you pick the door with the car behind it, you win. Monty
asks you to select a door and you choose door 1. Monty then opens one of the two remaining doors (door 3, say), revealing a
goat, and then asks you if you want to change your selection to door 2. Does it pay to take him up on his offer?
Think about what you would do before continuing to read. What is your intuition telling you?

At first, many people are ambivalent. They argue that because I have two doors left, it’s equally likely that the car is
behind door 1 as it is behind door 2. Thus, switching neither hurts nor helps. This argument isn’t quite correct, though;
it doesn’t make use of information that Monty provided you with by opening one of the doors for you! It turns out that some
knowledge of conditional probability would greatly increase your chances of going home with that new car. Let’s see why.

## Law of total probability
Let’s see what a probabilistic argument tells us. From the fact that Monty opened door 3, we know that the car has to be
behind door 1 or door 2. Let $S$ represent the event that the switching strategy wins the game. I think the simplest
argument is the one that makes use of the Law of Total Probability (LoTP), which we will simplify to:
Given a partition of a sample space (a set of events that are disjoint from one another and together make up the whole
sample space) $B, B^C$, $P(A)$ (for $A$ in the same probability space as $B$) can be written as $P(A) = P(A|B)P(B) +
P(A|B^C)P(B^C)$ (Note: $B^C$ is the complement of $B$. $B^C$ occurs if $B$ does not).
(The above can be naturally generalized to an infinite partition of the sample space.)
For an intuitive idea of what this means, this picture is helpful:

![Law of total probability](lotp.png)

As you can see, part of $A$ intersects with $B$ and the other part intersects with $B^C$. The LoTP basically tells us that
one way of computing $P(A)$ is to add up the probabilities that A occurred given that either (1) $B$ occurred, or (2) $B$
did not occur (i.e. $B^C$ occurred). Because $B$ either occurred or didn’t, this sum has to give the total probability of
$A$. We are going to apply similar logic to our problem.

As is obvious, at the outset, the car is either behind door 1, door 2 or door 3. Let $D_i$ be the event that the car is
behind door $i$. Because the $D_i$ are a partition of the sample space, I can use the LoTP, so that
<div>
$$P(S) = P(S|D_1)P(D_1) + P(S|D_2)P(D_2) + P(S|D_3)P(D_3)$$
</div>
At the beginning of the game, the car had an equal likelihood of being behind any of the three doors, we can fill in the
right multiplicands in the sum above:
<div>
$$P(S) = P(S|D_1)\cdot \frac{1}{3} + P(S|D_2)\cdot \frac{1}{3} + P(S|D_3)\cdot \frac{1}{3}$$
</div>
Next, observe that $P(S|D_1)$ is the probability that switching wins given that the car is behind the door you initially
chose. This probability is 0, because you would be switching away from the winning door. If the car is behind door 2 (note
that in this case, door 3 was opened and is no longer in contention), switching always gets you the car, i.e. $P(S|D_2) =
1$. The same is true if the car is behind door 3 (and door 2 was opened). Hence
<div>
$$P(S) = 0 \cdot \frac{1}{3} + 1 \cdot \frac{1}{3} + 1 \cdot \frac{1}{3} = \frac{2}{3}.$$
</div>
In other words, switching wins the game for you with probability $2/3$!

## Intuition
Let’s see if we can understand this a bit better without appealing to symbols. When you first chose door 1, you had a $1/3$
chance of winning the car. Stated a different way, you had a $2/3$ chance of *not* getting the car. When Monty opens one of
the unchosen doors and reveals a goat, he is, in effect, providing you with new information. The probability of your
initial choice being correct is still $1/3$, but with your updated understanding of the world, the $2/3$ probability that
the car is not behind your initial choice of door is all resting on the single remaining door.

For a more extreme application of this line of reasoning, consider the a similar problem in which you start with 100 doors
to choose from. You choose door 1 and Monty opens 98 of the remaining doors, revealing goats. Originally, your choice held
a $99/100$ probability of being incorrect. Initially, that $99/100$ was spread evenly over the 99 doors you didn’t select.
With each door Monty opened, that $99/100$ was condensed to fewer and fewer doors, so that by the very end, there is a
single door holding all of that probability that you were initially wrong. You’d be crazy not to switch.*

## Conclusion
The Monty Hall paradox is a fun, relatable problem that is a terrific example of the need for formality in thinking about
probabilities. Our intuition is a powerful tool, but for as many problems as it allows us to solve, it yet more often leads
us astray. In such moments it is crucial to have a systematic, rigorous approach with which to check ourselves and make
sure that we remain on sound logical footing.

*For the more symbolically inclined reader, I want to quickly use the logic from the simple cases to show that it in fact
always pays to switch doors. Suppose we generalize the problem so that there are $n\geq 3$ doors and after you select one
of them, Monty opens $m$ of the remaining doors ($1 \leq m \leq n - 2$) and then asks you if you want to switch. In this
case, after opening $m$ doors, the $(n-1)/n$ probability that you were initially wrong gets evenly distributed across the
$n - 1 - m$ remaining doors, so that each door carries a probability of $(n-1)/(n(n - 1 - m))$. We complete the argument by
verifying that this quantity is always bigger than $1/n$.
To do this, note that showing
$1/n < (n-1)/(n(n - 1 - m))$
is the same as showing
$1 < (n-1)/(n - 1 - m)$, which is clear.
