---
title: Solving Wordle
date: 2022-01-12
tags: [misc]     # TAG names should always be lowercase
---

## Introduction
For those who don't know what Wordle is, [check it out](https://www.powerlanguage.co.uk/wordle/).
It's essentially a word game that works like the game MasterMind. If you've been on the internet
in the past couple of weeks, you've probably seen your friends or follows posting
little images that show how quickly, and by which path, they solved the day's puzzle.
After trying it, I thought it might be fun to try to write some code that solves the puzzle
(most of the time). The rest of this post will walk through how I came up with the solution,
how I put the code together, and some insights I gleaned using my solver.

## So what are the rules?
Before going any further, I want to review the rules. The game proceeds
as follows. A target word is chosen and hidden from the player. On each turn,
the player guesses a five-letter word. After each guess, the player receives feedback
about the letter at each position of their guess. For each position, the player might
receive:

1. <span style="color:green">**Green:**</span> if the letter of the guess matches the letter of the target at that position.
2. <span style="color:orange">**Yellow:**</span> if the letter of the guess matches the letter at some other position of the target.
3.  <span style="color:grey">**Grey:**</span> if the letter of the guess is not in the target.

For example, if the target word is "taker" and the guess is "talks", the feedback
would be
<span style="color:green">ta</span><span style="color:grey">l</span><span style="color:orange">k</span><span style="color:grey">s</span>
, because the first two letters are exactly right, "l" and "s" are
not in the target word at all, and "k" is in the target but in a different position than
it occupies in the guess.

## Coming up with a solution
At first, I tried to apply some concepts I had been studying as part of a reinforcement
learning class I'd been taking online. It's possible that the formulation I came up
with just wasn't a good one, but a simple approach without any fancy AI turned out
to actually work very well. I've learned, both through my job and some independent
study, that conceptual simplicity is often underrated.

My general approach was simple. After collecting the body of words that Wordle
uses (which can actually be obtained pretty easily by inspecting the page source
of the game's webpage), I thought through what the skeleton of an algorithm would
look like, and I came up with this:

```python
guesses_made = 0
set current guess to an initial guess
while (guesses_made < 6) and (current_guess is not the target):
  get feedback on current guess
  use feedback to reduce the set of valid words
  make make another guess
  increment guesses_made
```

One way of looking at this skeleton is that we begin the game with no constraints
on which words are and are not valid. As
we make guesses and see feedback, we gain additional information that
allows us to further and further constrain the set of available words until -- hopefully --
we've narrowed it all the way down and we're certain of the answer.

As described just above, the algorithm skeleton is missing a few important details,
namely:

* How does the feedback allow us to determine the pool of valid words we can choose from?
* How do we make our next guess given a set of guessable words?

The implementation choices we make to answer those two questions ultimately lead
to different algorithms. In this post, we discuss
some quick-and-dirty, very simple choices that turn out to perform well, but
I'd encourage you to come up with interesting alternatives on your own to see if you
can come up with something even better!

### Using the feedback
Using the feedback requires specifying what kinds of words each type of feedback allows
us to eliminate.

When we receive grey feedback, we know to eliminate all words that contain the grey letter.

When we receive green feedback, at position 2, say, we know to eliminate all words
that do not contain the green letter at position 2.

When we receive yellow feedback, there are two kinds of elimination we can perform. If
we get yellow feedback in position 3, then we know that the letter in our guess at position
3 cannot be in the target word at position 3, so we can eliminate all words that correspond
with our guess at position 3. We can also eliminate any words that do not contain the yellow
letter, as we know it must be in the target somewhere.

Finally, there is the problem of words with the same letter repeated multiple times.
Thinking things through a little bit, we realize that the number of yellow and green
copies of a given letter is a lower bound on the number of copies of that letter that
must be in the target word. For example, if we have a yellow "t" and a green "t" in
the feedback, we know that the target word must have at least 2 "t"s, so we can eliminate
all words with 0 or 1 "t"s.

### Making the next guess
Each time we use feedback to cull the set of valid words, we have to then choose from
a potentially vast set of remaining words. In order to do this, we have to come up
with some heuristic to narrow the field.

In my case, I chose to give each word a score and then chose the word with the highest score (breaking ties randomly if required).
To compute the score, I first came up with the distribution of letters in each position.
For example, at position 1, maybe "s" was the most common letter, making up 6% of the letters
found in position 1 across the set of possible words.
If the word under evaluation contains an "s" at position 1, the word
would accrue a credit of 0.06 for the "s". The sum of these credits across the 5 positions
determines the word score. At each point, I select the valid word with the highest score.

This scoring system has (at least) one obvious weakness! If the target word has letters in certain positions that are very
uncommon for that position, the algorithm will pick other words first and possibly run out of guesses. Trying to figure out
how to remedy this would make the algorithm more robust, but I haven't given it enough thought as of this writing.

## How well does the solver work?
With an allowance of 6 guesses, on a random sample of 5k target words, my solver successfully found the target word about 90% of the time
in an average of 5 guesses.
Increasing the allowance to 9 guesses, it succeeds 98.5% of the time. With 15 guesses, it succeeds on all 5k examples. In the instances
where it fails with 6 guesses, there are, on average, about 7 valid choices left. That's pretty good!

## What is the best word to start with?
Before trying to answer this question, it should be noted that "best" in this context
depends on your algorithm. Different scoring methodologies, for example, would imply
a different ordering on the quality of initial guesses. The results below are obtained
using the algorithm we just described; if you vary the algorithm, you might find something
different.

For each possible initial guess -- around 13k of them -- I chose 200 random target words.
(I could have chosen more than 200, but I was constrained by computation time.)
For each guess, we record the fraction of the 200 problems that were solved successfully and assign
that as a score for that initial guess.

Some of the words that achieved the top 5% of scores were

* chapt
* chimp
* germs
* compt
* match
* chems
* frump
* bumph
* spick
* crumb
* ...

and some of the words in the bottom 5% were

* nanna
* zooea
* gazoo
* zexes
* vairy
* roque
* navvy
* ninon
* ozzie
* nouny
* ...

(there were others, but I don't show them here for brevity). None of the words in the top
5% of scorers repeated letters, while 90% of those in the bottom 5% did, suggesting
that when picking your first word, it would be unwise to pick a word with repeated letters.

I also looked at how much of the best initial guesses were made up of the five most common
(s, e, a, o, and r) and five least common (v, z, j, x, and q) letters of the alphabet.
(Here, most and least common are relative to the 12k Wordle words.) At first, the results
seem a bit counterintuitive -- 51% of the letters in the worst 5% of first guesses are
made of the five most common letters of the alphabet, while only 28% of those the best 5% are!
What gives!?

One hypothesis is that feedback on common
letters may help significantly narrow the field, but repeating them doesn't provide much additional
information, so it's worth diversifying. But then, you might ask, why aren't there words in there
with repeated infrequent letters? I imagine that this is probably because there just aren't that many
words to begin with that have multiple qs, js, vs, xs, or zs. The better scorers use common
letters, but avoid repeating them, so the fraction of common letters is smaller in that set.
If we look at the fraction of letters in high and low scorers that come from the five least common letters,
what we see is striking: the high scorers do not contain *any* letters from the five least
frequent letters, while the lower scorers are 14% infrequent.

## Conclusion
I hope you found this exercise as fun and interesting as I did. I'll also be posting the code
I used soon, so feel free to have a look at it if you're interested
in seeing what the actual implementation looks like.
Even though the insights we arrived at were pretty intuitive, I hope that
you enjoyed putting a little bit of rigor to it. Happy Wordling!


Edit (2022-01-17): I've posted the code [here](https://github.com/gindij/wordle).
