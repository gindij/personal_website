---
title: Probabilistic ballparking
date: 2018-11-4
tags: [stats]     # TAG names should always be lowercase
use_math: true
---

## Introduction
Very often, finding exact answers is a pain; ballpark estimates usually suffice. When you’re nervous to board
a plane, you don’t care to calculate the exact probability that the plane will land safely; you only care
that it’s over $99.99\%$. When you’re trying to figure out how long a project will take your team at work,
you use approximations throughout your calculation because there are far too many unknowns and variable to
compute the exact answer. Will a member of your team get sick and have to take time off? How quickly will
your team members learn a new technology? How productive will the new-hire be? Across computer science, there
are a myriad problems whose solutions are, in a rigorous sense, intractable to calculate exactly. To solve
such problems — they show up all over a variety of industries — we design approximation algorithms that
sacrifice some optimality and/or deterministic correctness for gains in efficiency and simplicity.

## Ballparking
In probability, one particular set of heuristic techniques we use is a set of inequalities called probability
bounds. You would use them for some of the same reasons described above: intractability of an exact
probability calculation; lack of need for an exact answer; generality (they don’t make many complex, esoteric
or restrictive assumptions, so they apply to lots of different problems). In this post, I want to state and
prove a few probability bounds and show how you can apply them. While the estimates might not always give
you something useful to work with, it’s good to be aware of how to use the bounds should the opportunity
present itself.

### Example
As we often do when probability is concerned, let’s think about a sequence of coin flips. Instead of the usual unbiased coin, though, let’s say the coin falls heads with probability $p=1/3$ (and tails with probability $1 - p = 2/3$). In a sequence of 100 flips, what is the probability that greater than or equal to half of them fall heads?

The distribution one would use to model this question is most probably the binomial distribution. If we let the random variable $X =$ # of heads in 100 flips, we would say that $X \sim \text{Bin}(100, 1/3)$ ($X$ represents the number of times a head will fall in 100 flips when the probability of heads on each independent toss is 1/3). In the case of this particular problem, we can calculate the exact probability of 50 or more heads. The formula that computes the exact solution looks like (don’t worry about what the symbols mean — not important, but if you’re interested, take a look at this (https://en.wikipedia.org/wiki/Binomial_distribution)):

<div>
$$\sum_{k=50}^{100} {100 \choose k}(\frac{1}{3})^k(\frac{2}{3})^{100-k} \approx 0.00042.$$
</div>

Without a bunch of cleverness or a computer, it would be very hard, if not impossible, to carry out that computation by hand. Let’s see if those probability bounds I talked about can lend us a hand. Before we do, we need to introduce two facts about binomial random variables. If $X \sim \text{Bin}(n, p)$, the expected value (average value) of $X$, written $E(X)$, is equal to $np$ (the number of trials times the individual probability of success), and the variance of $X$ (a measure of how much $X$ deviates from its average value), written $\text{Var}(X)$, is equal to $np(1-p)$. With that in mind, follow me.

## Markov's inequality
The first probability bound we’re going to look at is known as *Markov’s inequality*. Before we technically state it, in plain English, Markov’s inequality tells us about the probability of a random variable exceeding some value. Formally, if $X$ is a positive random variable (the outcomes of your experiments are strictly positive values), then for any real number $a > 0$,

<div>
$$P(X \geq a) \leq \frac{E(X)}{a}.$$
</div>

Before we prove this, I want to explicitly state that the primary advantage of Markov’s inequality is its generality. It usually doesn’t furnish the most useful bounds, but notice that in order to apply Markov, all we need to know that is that $X$ is a positive random variable. As we will see below, it is also an important building block with which we will come up with better probability bounds. Before we see what it would tell us about the problem we started with, let’s prove it.

### Proof
Let the random variable $I = 1$ if $X \geq a$ and $0$ otherwise. The proof is mostly complete when you notice this convenient, but somewhat subtle fact:

<div>
$$I \leq \frac{X}{a}.$$
</div>

Why? Let’s think about it. If $I = 1$, then, by definition, $X \geq a$. Dividing by $a$ on both sides of $X \geq a$ gives us $\frac{X}{a} \geq 1 = I$. If $I = 0$, the above inequality holds because both $X$ and $a$
are positive. Taking the expectation of both sides (don’t worry — expectation doesn’t change the sign of the
inequality), the above inequality turns into $E(I) \leq E(\frac{X}{a})$. We can pull $\frac{1}{a}$ out of the
right hand term because you can pull constants out of expectation. Further, by the definition of expected
value, $E(I) = 1 \times P(I = 1) + 0 \times P(I = 0) = P(I = 1)$ is just the probability that $X \geq a$ (by
our definition of $I$), so the above expression can be rewritten as

<div>
$$P(X \geq a) \leq \frac{E(X)}{a},$$
</div>

so we’ve finished the proof.

### A bound on the example using Markov
Now that we know that Markov’s inequality holds, let’s see what it can tell us about our problem. Recall that
we defined $X$ to be the number of flips that fall heads in a sequence of $100$ tosses. In particular, we
wanted to know what the odds were that there were more than or equal to $50$ heads, i.e. the probability that
$X \geq 50$. Noting that the expected value of our particular $X$ (with $n = 100$ and $p = 1/3$) is $np = 100
\times 1/3$, we can plug these numbers into Markov and see that

<div>
$$P(X \geq 50) \leq \frac{100 \times \frac{1}{3}}{50} \approx 0.67.$$
</div>

This tells us that the probability that the number of heads we see exceeds $50$ is at most $0.67$. Not so
useful, but do you see how simple that was to compute? Given the exact answer we computed above, this
estimate doesn’t tell us very much, but the computation was so easy.

## Chebyshev's inequality
The next bound I want to look at is called Chebyshev’s inequality. While it looks and feels a bit different
from Markov, it’s spirit is similar. Chebyshev is a statement about the probability that some random variable
deviates from its average by a certain amount. Its formal statement is: if $X$ is a random variable with
finite expected value $\mu$ and finite variance $\sigma^2$ (those two conditions hold enough of the time),
then

<div>
$$P(|X - \mu| \geq k) \leq \frac{\sigma^2}{k^2}.$$
</div>

In English, the probability that $X$ deviates from its mean by at least $k$ is at most $X$’s variance divided by $k^2$.
We will find this bound a bit more useful when we apply it to our problem, but before we do that, we need to prove it.

### Proof
If $X$ is a random variable, then $X - \mu$ is a random variable. Furthermore, if $X - \mu$ is a random variable,
then so is $(X - \mu)^2$. Now we apply Markov with $X$ replaced by $(X - \mu)^2$ and $a$ replaced by $k^2$, and we have:

<div>
$$P((X - \mu)^2 \geq k^2) \leq \frac{E((X - \mu)^2)}{k^2}.$$
</div>

The expression in the parentheses on the left side is equivalent to the expression $|X - \mu| \geq k$ (take the square
root of both sides). Replacing what’s in parentheses with the equivalent formulation and recognizing the numerator on
the right side as the very definition of variance completes the proof because we can rewrite the above as

<div>
$$P(|X - \mu| \geq k) \leq \frac{\sigma^2}{k^2}.$$
</div>

That proof might have actually been mechanically easier than Markov! In addition, Chebyshev’s inequality is usually more
informative and almost as general as Markov, so let’s take a look at what it can tell us about our problem.

### A bound on the example using Chebyshev
Recall that $\mu = np$ in our problem is approximately $33.3$ and that the variance is $\sigma^2 = np(1-p) = 100 \times 1/
3 \times 2/3 = 200/9 \approx 22.2$. The information we’re missing here is what value of $k$ to choose. In this case, we
want to know something about the probability that there are more than or equal to $50$ heads. $50 - 33.3 = 16.7$ so if we
set $k$ to $16.7$, we will be able to apply Chebyshev to know an upper bound on the probability that $X$ takes a value
that is more than $16.7$ away from $\mu$ in either direction. More directly, we would be able to say something about the
probability that $X$ takes a value less than or equal to $33.3 - 16.7 = 16.6$ and greater than or equal to $33.3 + 16.7 =
50$. The probability that $X$ is less than or equal to $16.6$ is superfluous, but it doesn’t affect the correctness of our
upper bound. By Chebyshev, we can conclude that

<div>
$$P(|X - \mu| \geq 16.7) \leq \frac{22.2}{16.7^2} \approx 0.079,$$
</div>

so our upper bound is now around $8\%$. Given the result of the explicit calculation we computed first, this isn’t especially tight either, but it’s much better than Markov and it was almost as easy to compute!

### Conclusion
There are other, more powerful bounds that I won’t go into here because they’re more involved and this post is already long, but hopefully you’ve gotten an idea about where these sorts of things might be useful.
