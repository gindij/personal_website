---
title: Distinct values in a data stream
date: 2022-09-11
tags: [computer-science]     # TAG names should always be lowercase
use_math: true
---

## Introduction
In this post I detail a randomized algorithm (that looks rather like black magic) to count the number of distinct elements in a data stream.

## Naive solution
Suppose that data is presented as a sequence of values $\sigma = s_1, \dots, s_m$ where, for simplicity, the $s_i \in \lbrace 0, \dots, n - 1\rbrace$. I want to know the number of distinct values that were in the stream. For example, if the sequence were $\sigma = 1,2,3,4,5,5,7$, our algorithm should output 6. How might we accomplish this?

A very simple way is to keep an $n$-bit vector $v$ ($n$ because that is the size of the set our values are being drawn from) where $v_i$ represents whether we have seen the element $i$. Once we have seen all of the data, we sum the values in $v$ and output that as our result. Easy, right?

## Does it work?
The issue here is that for sufficiently large data sets, using $n$ bits of storage is not possible. The above approach is (provably) optimal in terms of space... but that "optimality" is only with respect to deterministic algorithms that produce the correct result every time. What if we used randomization? Might we be able to achieve sublinear space usage?

## A better solution
The rest of this post will detail a randomized algorithm that uses sublinear space and to solve the above problem with a solution that is correct as close to 100% of the time as we'd like... it's rather like magic. Let's see how it works.

The algorithm is as follows:
* Choose $\varepsilon \in (0,1)$.
* Let $t = \frac{400}{\varepsilon^2}$.
* Pick a pairwise independent hash function $h: \{0,\dots,n-1\} \to \{0,\dots,n-1\}$
* Upon receiving $s_i$, compute $h(s_i)$ and update $D$, a datastructure with which we keep track of the $t$ smallest hash values we've computed.
* When we stop receiving values, let $X$ be the $t^{th}$ smallest hash value and output $\frac{nt}{X}$.

Before we move on, note that the algorithm only requires space for (1) the hash function (discussed below) and (2), the data structure $D$, which is a constant amount of space that depends on $\varepsilon$... Assuming that our hash function doesn't take up too much space, the algorithm satisfies our space requirement.

I imagine you're thinking what I'm thinking (or what I was thinking)... namely, that there is absolutely no reason why that should work. Before we dive into some completely mind-blowing mathematical analysis, I want to quickly digress to explain what a pairwise independent hash function is and then to fill in some details and provide an "intuitive" flavor for where this algorithm comes from.

### Pairwise independent hash functions
Imagine we have a hash function $h$, two arbitrary inputs $x_1$ and $x_2$, and their corresponding outputs $y_1 = h(x_1)$ and $y_2 = h(x_2)$. The hash function $h$ is a pairwise independent hash function if knowing the probability that $h(x_1) = y_1$ does not give us any information about the probability that $h(x_2) = y_2$. 

In mathematical terms, $h$ is pairwise independent if
$\Pr[h(x_1)=y_1 \wedge h(x_2) = y_2] = \frac{1}{n^2}$
($n$ is the size of the output space in our case). The natural question we ask when we present a definition is: Do such objects exist? Without going into too much detail about why, be assured they do indeed exist, and we are going to pick ours from the family
$\mathcal{H} = \lbrace h_{ab}: \lbrace 0,\dots,p-1 \rbrace \to \lbrace 0,\dots, p-1\rbrace \rbrace $
where $p$ is prime, $0 \leq a \leq p -1$ and $0 \leq b \leq p-1$, defined, for some input $k$, by $h_{ab}(k) = ak + b \mod p$. In our case, if $n$ is not prime, we can find a prime near $n$ and let $p$ be that prime (an interesting proof for another time is that 
for any $n$, there is always a prime between $n$ and $2n$). Note that the only thing we have to store about this hash function to use it are $a$ and $b$ -- each of which only requires $\log p$ bits of storage (so we are still under the linear space we are trying to avoid).

## How do we get $\frac{nt}{X}$?
Next, I'll try to motivate where $\frac{nt}{X}$ comes from. Suppose that there are $k$ distinct values in the stream (that is, suppose that $k$ is the solution to our problem). Let those values be $a_1,\dots,a_k$. Because we have $n$ possible outputs of our hash function and there are $k$ distinct values, we can expect that the distance between the $h(a_i)$ is $\frac{n}{k}$. In particular, we expect the $t$th smallest value to be at $t \cdot \frac{n}{k}$. Thus, $X \approx \frac{nt}{k}$. Solving for $k$, we see that $k \approx \frac{nt}{X}$, so that's exactly what we output. In the next piece, we will need to distinguish between the right answer and our output so the correct answer will henceforth be referred to as $k$ and we will refer to our output, $nt/X$, as $\hat k$.

## So does the fancy algorithm work?
All that's left to do is now to show that $\hat k$ is very close to $k$. Mathematically speaking, we want to show that
<div>
$$\frac{k}{1 + \varepsilon} \leq \hat k \leq (1+\varepsilon)k$$
</div>
with a probability $\geq \frac{99}{100}$ (where $\varepsilon$ is the parameter we chose in the first step of the algorithm).
If we can show that $\Pr[\hat k > (1+\varepsilon)k] \leq \frac{1}{200}$ and that $\Pr[\hat k < k/(1 +\varepsilon)] \leq \frac{1}{200}$, we get
<div>
$$\Pr[k/(1+\varepsilon) \leq \hat k \leq (1+\varepsilon)k] = 1 -\Pr[\hat k > (1+\varepsilon)k] -\Pr[\hat k < k/(1 +\varepsilon)].$$
</div>

Because each of the probabilities on the right side of the inequality are $\leq \frac{1}{200}$, we can rewrite the above as
$\Pr[k/(1+\varepsilon) \leq \hat k \leq (1+\varepsilon)k] \geq 1 - \frac{2}{200} = \frac{99}{100}$
which is what we want.

So all we have left to do is to show that the two probabilities are indeed both $\leq \frac{1}{200}$.
We will only do analysis of one of the two probabilities because a symmetric argument takes care of the other side. All of this put together means we only have one claim left to prove: $\Pr[\hat k > (1+\varepsilon)k] \leq \frac{1}{200}$.

First, we note that
<div>
\begin{align*}
\Pr[\hat k > (1+\varepsilon)k] &= \Pr[ (nt)/X > (1+\varepsilon)k] \\
&= \Pr\biggr[X < \frac{nt}{(1+\varepsilon)k}\biggr].
\end{align*}
</div>

With this in mind, define a random variable $Y_i$ which takes the value 1 if $h(a_i) < \frac{nt}{(1+\varepsilon)k}$ and 0 otherwise. Now, observe that on average, the odds of $h(a_i)$ taking a value less than $\frac{nt}{(1+\varepsilon)k}$ is the number of hash values between 0 and $\frac{nt}{(1 + \varepsilon)k}$ divided by the number of possible values $h(a_i)$ can take. We can write this mathematically as
<div>
$$
E[Y_i] = \frac{tn}{(1+\varepsilon)kn} = \frac{t}{(1+\varepsilon)k}.
$$
</div>

Next, let the random variable $Y$ be the sum of the $Y_i$. Because expectation is linear, we can infer that $E[Y] = \sum_{i = 1}^k E[Y_i] = k \cdot\frac{t}{(1+\varepsilon)k} = \frac{t}{1 + \varepsilon}$. We also see that in this case,
$\text{Var}(Y) = \frac{t}{1+\varepsilon} - \frac{t^2}{(1+\varepsilon)^2} \leq \frac{t}{1 + \varepsilon} = E[Y]$.
We're almost there!

We can now more readily examine the probability we were interested in above in terms of $Y$. That is, we can say
<div>
$$\Pr \biggr[X < \frac{nt}{(1+\varepsilon)k} \biggr] = \Pr[Y \geq t].$$
</div>
Why? The left hand probability represents the chances that the $t$th smallest hash value we saw was less than some value, let's call said nasty value $M$ for a minute. $Y$ is the number of hash values we saw that were less than $M$. If at least $t$ values hashed to values less than $M$, then $X$, the $t$th smallest hash value will be less than $M$, hence the equality.

Using our variance result from earlier, we have $t = (1 + \varepsilon) E[Y]$, we really want to know $\Pr[Y \geq (1 + \varepsilon) E[Y]]$. This is bounded above by $\Pr[|Y - E[Y]| \geq \varepsilon E[Y]]$ (adding in the absolute value causes inequality rather than equality). Chebyshev's inequality tells us that
<div>
$$\Pr[|Y - E[Y]| \geq \varepsilon E[Y]] \leq \frac{\text{Var}(Y)}{\varepsilon^2 E[Y]^2}.$$
</div>
Because $\text{Var}(Y) \leq E[Y]$, we can write
<div>
$$\frac{\text{Var}(Y)}{\varepsilon^2 E[Y]^2} \leq \frac{E[Y]}{\varepsilon^2 E[Y]^2} = \frac{1}{\varepsilon^2 E[Y]}.$$
</div>
Now recall that earlier, we said that $t = (1 + \varepsilon) E[Y] \iff E[Y] = \frac{t}{1 + \varepsilon}$. We can substitute this in for $E[Y]$ above and get
<div>
$$\frac{1}{\varepsilon^2 E[Y]} = \frac{1 + \varepsilon}{\varepsilon^2 t}.$$
</div>
Because $\varepsilon$ is at most 1, we conclude
<div>
$$\frac{1 + \varepsilon}{\varepsilon^2 t} \leq \frac{2}{\varepsilon^2 \frac{400}{\varepsilon^2}} = \frac{2}{400} = \frac{1}{200}.$$
</div>

Thus, all in all, we've shown that the odds of our return value being an over-estimate is bounded above by 1/200. A similar argument shows that the probability of underestimating is also bounded above by 1/200, so the probability of erring is at most 1/200 + 1/200 = 1/100 which means our probability of success is at least 99/100, as desired.
