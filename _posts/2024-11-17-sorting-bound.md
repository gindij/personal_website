---
title: A bound on sorting performance
date: 2024-11-17
tags: [computer-science]     # TAG names should always be lowercase
use_math: true
---

# Introduction
If you wake any programmer up in the middle of the night and ask them to name an algorithm, a sizable fraction would probably invoke some kind of sorting procedure. Some might name [quicksort](https://en.wikipedia.org/wiki/Quicksort#:~:text=Quicksort%20is%20a%20divide%2Dand,or%20greater%20than%20the%20pivot.), some [merge sort](https://en.wikipedia.org/wiki/Merge_sort), still others [insertion sort](https://en.wikipedia.org/wiki/Insertion_sort), and some might troll you by naming [Bogosort](https://en.wikipedia.org/wiki/Bogosort).

The first three of those algorithms are all what are known as comparison-based sorts: all of them work by comparing elements and making decisions based on the results of the comparisons. In this post, I want to talk about a lower bound on efficiency for comparison-based sorting algorithms. In other words, I want to show that if you invented a new comparison-based sorting algorithm, then even without knowing how it works, I could tell you what its best conceivable runtime is (as a function of the input size).

To get a better sense for what I mean, let's dive in.

# Comparison-based sorting
To understand what we mean by comparison-based sorting, let's walk through one of the algorithms I mentioned earlier: merge sort.

Merge sort essentially works by sorting the first half of the input, sorting the second half, and then merging the two sorted results. But how do we sort the first and second halves? We sort the first half of the first half, sort the second half of the first half, then merge them. And so on and so forth. In order for this recursive process to work, though, the process has to bottom out, right? Right! It bottoms out when a "half" is empty or has one element, since empty and singleton lists are (trivially) sorted.

To show this with an example, let's say we start with the input list [1, 3, 8, 4, 5, 2, 6, 9, 7]. We would
1. Split the list into two halves: [1, 3, 8, 4] and [5, 2, 6, 9, 7].
2. Split the first half into two halves: [1, 3] and [8, 4].
3. Split the first half into two halves: [1] and [3].
4. Each of [1] and [3] is sorted, so we merge them into [1, 3].
5. Split [8, 4] into two halves: [8] and [4].
6. Each of [8] and [4] is sorted, so we merge them into [4, 8].
7. Now we merge [1, 3] and [4, 8] into [1, 3, 4, 8].
8. Carry out the same recursive process for [5, 2, 6, 9, 7] to get [2, 5, 6, 7, 9].
9. Merge [1, 3, 4, 8] with [2, 5, 6, 7, 9] to get the final result: [1, 2, 3, 4, 5, 6, 7, 8, 9].

The "comparison"s that happen in merge sort occur in the merging stage, which we won't go into detail about here. Now that we've seen one example of a comparison-based sort, we will now turn to thinking about sorting more generally using decision trees.


# Performance bound
So how can we possibly say anything important about the efficiency of a whole class of algorithms without considering every possible implementation?

First, let's suppose we have some input list of size $n$. The _indices_ of this list -- i.e., the numbers $1, \dots, n$ -- has $n! = n \cdot (n-1) \cdot (n-2) \cdot \dots \cdot 3 \cdot 2 \cdot 1$ possible orderings, exactly one of which puts the _elements_ in sorted order. We want to say something about the minimum number of comparisons required to find this ordering.

One way to think about this sorting problem is to use the abstraction of a decision tree. To make this more specific, the leaf nodes (the nodes the bottom of the tree) each represent one possible ordering of the list. The other nodes (called internal nodes) represent comparisons between elements at different indices of the list. An example of this tree is shown in the image below ([source](https://genome.sph.umich.edu/w/images/b/b8/Biostat615-lecture6-presentation.pdf)):

<img src="tree.png" width="550" height="350"/>

Each of the ovals represents a comparison between the elements at two _indices_ of the array. To understand how to read this tree, let's say that our input array is called $A$. At the root node of the tree, if $A[1] \leq A[2]$, then we would proceed to take the left branch and compare $A[2]$ with $A[3]$. If $A[2] \leq A[3]$, then we would take the left branch again and reach the leftmost leaf, which would indicate that $A$ was already in sorted order. With other input orderings, though, the index order that results in $A$ being sorted might be some other leaf, which we could similarly determine by doing a bunch of comparisons. (The key to avoiding confusion here is to remember that the numbers in the ovals are _indices_, not the actual elements of the input list.)

Now, if there are $n!$ possible orderings of $A$, then there must be $n!$ leaves in the tree. Furthermore, we know that the length of the longest root-to-leaf path is the largest possible (worst-case) number of comparisons we would need to do to get our sorted order. Thus, in order to understand the best possible worst-case performance of our comparison-based sort, we want to find **the length of the longest possible root-to-leaf path in this decision tree.**

Let's suppose that the algorithm always completes after $h$ steps. Another way of stating what we want to find is to say we're looking for a lower bound on $h$. With $h$ comparisons, we can distinguish between $2^h$ orderings, since each comparison has two possible outcomes and the _indices_ are distinct (even if the elements aren't). In order to make sure we find the sorted order, we need to make sure that the $n!$ possible orderings can all be covered with $h$ comparisons (i.e., by checking at most $2^h$ orderings). In other words, we need this inequality to hold:
<div>
$$
    2^h \geq n!.
$$
</div>

Taking the log (base 2, as is customary in computer science) of both sides, this can be rewritten as
<div>
$$
    h \geq \log(n!).
$$
</div>

That's great!... But what is $\log(n!)$? On the one hand, $n!$ is huge, but on the other, maybe the $\log$ tames it? Well, we know that $n! \geq n(n-1)\dots (n/2) \geq (n/2)^{n/2}$, so we can rewrite our inequality again as
<div>
$$
    h \geq \log(n!) \geq \log((n/2)^{n/2}) = \frac{n}{2}\log \biggr( \frac{n}{2} \biggr).
$$
</div>
(The equality holds because of a property of logarithms: $\log(a^b) = b\log(a)$.) Ignoring constants, we get that $h$ must is bounded below by $n \log n$. To put it in a way that underscores how cool this proof is, what we're saying here is that no comparison sort can work using a worst-case number of comparisons that is (ignoring constants) smaller than $n \log n$. Again, we did this without looking at any particular implementations!

# Coda: can we do better?
There's one question left to answer: What if we relax the requirement that our algorithm be based on comparisons? Can we achieve a better worst-case performance than $n \log n$?

The answer is yes, and if our inputs follow a couple of additional (important) assumptions, we can do it with a pretty simple algorithm at that. If the elements of the input list are nonnegative integers that take values up to some maximum $M$, we can use the following algorithm:
1. Create a list $C$ of zeros of size $M$. The counting array $C$ effectively acts as a frequency table for the input, where $C[j]$ holds the count of occurrences of the integer $j$.
2. For each element of the array, increment the count at that element's index. In other words, if you see a 5, increment $C[5]$.
3. Once you've iterated through the entire list, iterate over $C$ and add $C[j]$ copies of $j$ to the output list.

This algorithm, called counting sort, is probably the most famous non-comparison sort. If the initial array has $n$ elements and $C$ has size $M$, then the algorithm takes $n + M$ steps to complete (ignoring constants). This can be good or it can be bad. It can be awesome if $M$ is not too much larger than $n$, since then (ignoring constants again), it would take approximately $n$ steps, which is faster than our comparison-sort lower bound of $n \log n$. If $M$ is very large, however, say $M \approx n^2$, then we've lost our performance edge. This algorithm also doesn't work with non-integers, which makes it less generally applicable than we'd like.

# Conclusion
In this post, we started with a hands-on example, established theoretical bounds using a decision tree formulation of the sorting problem, and finally explored how changing our assumptions about the inputs can unlock faster algorithms.

Sorting algorithms are a cornerstone of computer science, and understanding their limits helps us appreciate their clever design and implementation. The balance between theory and practice highlights the necessity of mathematics to the design of efficient algorithms that power our world.