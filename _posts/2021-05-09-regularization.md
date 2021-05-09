---
title: Probabilistic interpretation of regularization
date: 2021-05-09
tags: [statistics,optimization]     # TAG names should always be lowercase
use_math: true
---

## Introduction
If you've read enough of my posts over the years, you know that some of my favorite
topics to write about are those that can be thought about or studied from
different perspectives. In this post, I want to write about regularization, a
technique used in machine learning to mitigate a common problem called overfitting
-- a problem that crops up when algorithms fit their understanding of the world
so tightly to a particular dataset, that it isn't able to make predictions about
data that it hasn't seen. Regularization can be thought about as a term to add
to the optimization objective that directly discourages overfitting, or it can be
thought of in an interesting statistical way.

## A helpful example: simple linear regression
Let's assume that we're building a linear regression model. That is, assuming that
$b$ represents the number of bedrooms in the house, we _assume_ (more on this in a second)
that the relationship between the number of bedrooms and the price of the house
is linear:
<div>
$$
  p(b) = \theta b + \epsilon.
$$
</div>
Here, $p$ is price, $b$ is the number of bedrooms in the house, and $\epsilon$
is a random number that represents the error in our model, or the part of our model that
the data we are using do not explain. What the formula above says is that _we believe_
that we can model the relationship between #bedrooms and price with a linear model.
This _does not_ say that we believe that the actual relationship is linear. This is a
very important distinction. We believe that the linear model might be _useful_, not
necessarily that it is _correct_ or _true_.

(Another way of thinking about our equation, or model, is that it says once we know
the the parameter $\theta$ and the particular number of bedrooms $b_0$, the randomness
has been confined to the variation of prices around a known mean: $\theta b_0$.)

## Fitting the parameters
One natural way to find the best parameter $\theta$ for a set of data is to find the
the value of $\theta$ that literally best fits the input data! To better understand what
this means, let's suppose that we have (#bedroom, price) pairs $(b_i, y_i)$ for $i=1,\dots,100$,
and a current guess at a parameter $\theta$. To evaluate $\theta$, a natural measure of how
well $\theta$ fits is to find the average (square) error, where the error for each example
can be expressed as the difference between $\theta b_i$ (our prediction) and $y_i$
(the actual price). Mathematically, we can write this measure down as
<div>
$$
  J(\theta) = \frac{1}{100} \sum_{i=1}^{100} (\theta b_i - y_i)^2.
$$
</div>
Now that we've decided what constitutes a good choice of parameter, we can employ
tools provided by calculus to actually calculate what value of $\theta$ is best
by solving the optimization problem (replacing 100 with the more general $m$) given by
<div>
$$
  \text{argmin}_\theta \sum_{i=1}^m (\theta b_i - y_i)^2.
$$
</div>
(This is the value of $\theta$ that minimizes $J$. Without going into detail,
in this case, it turns out that the best value is $\theta = \frac{\mathbf y^T \mathbf b}{\mathbf b^T \mathbf b}$,
where $\mathbf b = (b_1,\dots, b_m)$ and $\mathbf y = (y_1,\dots,y_m)$.)

## Regularization
One important concern when fitting a machine learning model is whether or not
your model is too tightly fit to the data that you have. Because models are fit using
a finite sample of data, it is possible, even likely, that your data is not representative
of what can occur "in the wild." As such, the model you've built may be terrific on the data
it used to train, but does not actually generalize to situations it hasn't encountered.
There are various techniques for combating this problem, but the one we will discuss
here is one called regularization.

### The intuitive motivation
In models with more than one feature, overfitting tends to occur because certain
of the features have parameters that are too large, i.e., that their impact is overstated
in the model. As such, rather than just finding the parameters that minimize the least
squares objective, we want to find _small_ parameters that minimize the objective.
For the simple regression case, we would add a term to the objective:
<div>
$$
\text{argmin}_\theta ~~ \sum_{y_i, b_i}(\theta b_i - y_i)^2 + \frac{\lambda}{2} \theta^2
$$
</div>

Intuitively, if $\theta$ is large, the objective value that we are trying to minimize
will also be large, so the optimizer will not be encouraged to pick that value of $\theta$,
even it fits the data pretty well. Adding this term causes the optimizer to trade off
goodness of fit and simplicity (in the sense of parameters that aren't too large).
The constant $\lambda$ controls our preferences with respect to that tradeoff: larger
values of $\lambda$ will encourage smaller value of $\theta$.

### Statistical interpretation
While the intuitive motivation is usually enough, there is a cool
statistical interpretation of what is going on here that I think is worth pointing out.
If we instead think of finding $\theta$ by carrying out [maximum (log) likelihood estimation](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation) (MLE),
then regularization naturally arises when we add the additional assumption to our
model that $\theta$ comes from a normal distribution centered around zero with variance
$1/\lambda$ (we can tune $\lambda$ to change the width of the bell curve). Making this assumption
essentially pins down a probability density function for the parameter $\theta$:
$P(\theta) = \frac{\sqrt{\lambda}}{\sqrt{2\pi}}\exp(-\lambda(\theta - 0)^2/2)$.
Taking $\log$s (this doesn't affect the optimization problem we need to solve), we have
<div>
$$
  \log P(\theta) = \log\biggr( \frac{\sqrt{\lambda}}{\sqrt{2\pi}} \biggr) - \frac{\lambda}{2} \theta^2.
$$
</div>
Adding this assumption about the prior distribution over $\theta$ and ignoring
constants (with respect to $\theta$), we would need to solve the modified MLE problem:
<div>
\begin{align}
  \text{argmax}_\theta~ \log(P(\mathbf y ~|~ \mathbf b, \theta)P(\theta))
  &= \text{argmax}_\theta ~ -\sum_{y_i, b_i}(\theta b_i - y_i)^2 - \frac{\lambda}{2} \theta^2\\
  &= \text{argmin}_\theta ~ \sum_{y_i, b_i}(\theta b_i - y_i)^2 + \frac{\lambda}{2} \theta^2
\end{align}
</div>
which is exactly what we had intuitively motivated in the previous section!

We've just uncovered the statistical interpretation of regularization!* Using (this flavor
of) regularization is actually imposing a Gaussian prior onto $\theta$. As we force the
width of $\theta$'s bell curve to become smaller by increasing $\lambda$, we are encoding the
fact that larger values of $\theta$ are less likely and should therefore be penalized more
heavily during the optimization process.

## Conclusion
In this post, we encountered a cool technique that underlies many statistical models called maximum likelihood estimation (MLE), and showed that a common technique used to combat overfitting actually has a nice statistical interpretation, too!

Happy Mother's Day to all!

*The regularization we discuss here is called L2 regularization. Regularization comes in
other forms too. The most popular other choice is called L1 regularization, and it can
actually be interpreted as imposing a Laplacian (rather than a Gaussian) prior.
