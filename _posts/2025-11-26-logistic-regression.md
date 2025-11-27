---
title: Why the sigmoid?
date: 2025-11-26
tags: [machine-learning]
use_math: true
---

# Introduction
Logistic regression is often the first classification algorithm a machine learning engineer encounters. It is the workhorse of binary classification; it's simple, interpretable, and surprisingly effective. If you have taken a standard introductory course, you have likely been presented with the model definition as a fait accompli: take a linear predictor, wrap it in a sigmoid function, and voila, you have a probability.

But why? Why the sigmoid function specifically? Why not use a clipped straight line, or a different S-shaped curve like the cumulative normal distribution? Is it just an arbitrary choice that happens to work well, or is there something fundamental about the mathematics that demands this specific shape?

# Logistic regression
For various reasons we will not cover here, directly using linear regression for classification does not work well. If $x$ is $d+1$-dimensional, and $\beta = (\beta_0, \dots, \beta_{d})$ are model parameters that we adjust during training, then we can take our linear model $\beta^\top x$ and force it to only take values between 0 and 1 by pushing it through the sigmoid function. In other words, we model the probability that $y$ is 1 given features $x$ as
<div>
$$
    p(y=1|x) \approx \frac{1}{1 + \exp(-\beta^\top x)} = \sigma(\beta^\top x).
$$
</div>
The sigmoid function $\sigma$ has an output range between 0 and 1, but there have to be other such functions, right? Why use this one and not some other one? In the remainder of this post, we look at two ways to understand why the choice of sigmoid is deeper than it seems.

# Range mapping
(For this section, we will refer to $p(y=1|x)$ as $p$.)

Assuming we want to leverage linear regression as much as possible, we want to find something that we can model using $\beta^\top x$. The most obvious thing we could try first is to model the probability directly:
<div>
$$
    p \approx \beta^\top x.
$$
</div>

One simple reason this doesn't work is just that the output ranges don't match. To put a finer point on it, since $p$ is a probability, it can only take values between 0 and 1, whereas $\beta^\top x$ can take any real value. The plot below helps to visually understand our problem:

<img src="line_bad.png">

Because of this, we need to find something else we can model with $\beta^\top x$.

Instead of modeling $p$, what if we modeled $p/(1-p)$, a quantity known as the odds? Things get a little bit better here, since the odds range from 0 (when $p$ is very small) to $+\infty$ when ($p\approx 1$). However, remember that $\beta^\top x$ can take *any* real value, including negative values all the way to $-\infty$! So while this is a little bit better, it still doesn't quite work.

One thing to notice about the odds is that they cover the domain of the $\log$ function. So what if we modeled the log odds $p / (1-p)$ instead of the odds? In this case, the ranges match! When the odds are close to 0, the log odds approach $-\infty$. As the odds approach $\infty$, so do the log odds! At this point, our model is
<div>
$$
\log\biggr( \frac{p}{1-p} \biggr) \approx \beta^\top x
$$
</div>

We can solve this for $p$ using some simple algebra to see that this is equivalent to a model for $p$. To do this, first we exponentiate both sides
<div>
$$
\frac{p}{1-p} \approx \exp(\beta^\top x)
$$
</div>
Next, we multiply both sides by $1-p$ and distribute on the right.
<div>
$$
p \approx \exp(\beta^\top x) - p\exp(\beta^\top x)
$$
</div>
Next, we move the $p$ terms to the left side and pull them out to get
<div>
$$
p (1 + \exp(\beta^\top x)) \approx \exp(\beta^\top x)
$$
</div>
Finally, dividing by $1 + \exp(\beta^\top x)$, we have
<div>
$$
p \approx \frac{\exp(\beta^\top x)}{1 + \exp(\beta^\top x)}.
$$
</div>
We can see that this is actually the sigmoid by dividing the numerator and denominator by $\exp(\beta^\top x)$, which gives us our the final form of our model for $p$:
<div>
$$
p \approx \frac{\exp(\beta^\top x) / \exp(\beta^\top x)}{1 / \exp(\beta^\top x) + \exp(\beta^\top x) / \exp(\beta^\top x)} = \frac{1}{\exp(-\beta^\top x) + 1} = \sigma(\beta^\top x).
$$
</div>
(Remember that $1/\exp(\beta^\top x) = \exp(-\beta^\top x)$.)

This derivation is intuitive and visually satisfying. It solves the "unbounded output" problem by mapping the infinite range of a linear model to the unit interval of a probability. However, a skeptical reader might still ask: "Why did we choose to model the log-odds specifically?"

While the log-odds are a convenient choice for range mapping, they are not the only choice. We could have chosen other functions to map $(-\infty, \infty)$ to $[0,1]$ (such as the Probit function, which uses the cumulative distribution of a Gaussian). To understand why the sigmoid is not just a convenient choice, but the mathematically "natural" choice, we need to dig a layer deeper into statistical theory.

# The exponential family
Another, perhaps deeper way to arrive at the sigmoid function is to make an assumption about the conditional distribution of the target $Y$ given the input $X$. If we assume that
<div>
$$
Y|x \sim \text{Bern}(p(x)),
$$
</div>
i.e., that given a value for $x$, that $Y$ is a Bernoulli random variable with success probability $p(x)$ (note the dependence on $x$), then we can write the probability mass function for this distribution
<div>
$$
P(Y|X=x; p(\beta^\top x)) = \begin{cases}
    p(x) &\text{ if } Y=1 \\
    1 - p(x) &\text{ if } Y=0
\end{cases}
$$
</div>
From this point forward, we will make our notation less cumbersome and refer to $p(\beta^\top x)$ simply as $p$.

There's a more compact, cleverer way of writing $P(Y|X=x; p)$ that packs both cases from the previous formulation into a single expression:
<div>
$$
P(Y=y|X=x; p) = p^y (1 - p)^{(1-y)}.
$$
</div>
The key is that $y$, which is binary, acts as a switch that turns on the relevant term for each case. When $y = 1$, $1-y=0$, so $P(Y=1|X=x; p)$ resolves to $p$. Similarly, when $Y = 0$, the expression resolves to $1 - p$.

Let's first rewrite
<div>
\begin{align*}
P(Y=y|X=x; p) &= p^y (1 - p)^{(1-y)} \\
&= \exp(\log(p^y (1 - p)^{(1-y)})) \\
&= \exp(y\log(p) + (1 - y)\log(1 - p)) \\
&= \exp(y\log(p) + \log(1 - p) -y\log(1 - p)) \\
&= \exp((\log(p) - \log(1 - p))y + \log(1 - p)) \\
&= \exp(y\log(p/(1 - p)) + \log(1 - p))
\end{align*}
</div>

This is great! But why go through this derivation?

It reveals that the log-odds are the **natural parameter** of the Bernoulli distribution. By setting our linear predictor $\beta^\top x$ equal to this natural parameter, we are using what is called the **canonical link function**. This specific choice is mathematically "safe": it guarantees that the log-likelihood function with respect to the parameters $\beta$ is concave. In practical terms, this means our loss function has no local optima (at which we can get stuck), making the model robust and simpler to train. To see the model we get for $p$ when we model the log odds as $\beta^\top x$, we... just have a look at the earlier section on range mapping.

# Conclusion
In this post, we've seen two ways of motivating the use of the sigmoid function in logistic regression. The first is easier to grasp and more intuitive, but the second gives a glimpse of the mathematical depth that is often skipped over when engineers study ML for the first time. I hope you enjoyed!


