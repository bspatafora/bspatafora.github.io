---
layout: post
title:  "Introduction to Big O notation"
date:   2014-06-15
---


A quick and dirty overview, based heavily on the excellent lessons craftsman Mark Grant has been giving apprentices lately.

So, computers are useful. But in order for them to be of use, they need humans to tell them what to do (at least for now).

We call the instructions telling a computer how to solve a particular problem an algorithm. An example of a problem we might want a computer to solve is finding a word in a dictionary.

## Brute-force search

There are lots of ways to tell a computer how to do this. Lots of algorithms for searching a list. Some are better than some others in every regard, so it’s not worth considering the ones they’re absolutely better than (at least once you’ve determined which algorithms fall into that group).

The interesting subset of algorithms-for-whatever-given-task is the one in which each algorithm has some advantages and some disadvantages relative to every other, meaning each one has its uses (at least in theory). For example, some algorithms might take longer to run, while others require more memory. Some might work well for a small data set, but become unusable once the data set grows to a certain size.

Back to the dictionary problem. One approach would be to provide the computer with these instructions:

  1. Move to the beginning of the list.
  2. Read the word.
  3. Check whether the word is the one we’re searching for.
      * If so, return the word and terminate.
      * If not, move to the next word and repeat from step 2.

Of course, these instructions would have to be even more basic for a computer to be able to follow them, but they’re good enough for the purposes of this explanation.

## Evaluating our algorithm

A few things are intuitively clear: This algorithm would work well enough for small inputs (i.e., short dictionaries), but would not be a very efficient way of finding a word in a 1,000,000 word list. And even if it would perform very well in the case that we’re looking for the word [aa][], it seems like there must be a better way to find a word near the middle or at the end of the dictionary than first reading through every prior word.

It would be nice to have a way of talking about how well an algorithm performs more formal than gut feeling. Some metric we could use to compare the things. One candidate might relate to the number of steps each iteration of an algorithm takes. Another could involve the number of additional iterations that must be performed as the size of the input increases.

## Binary search

To see which is more useful, let’s consider an alternative search algorithm that takes advantage of the fact that we’re working with ordered data:

  1. Move to the middle of the list.
  2. Read the word.
  3. Check whether the word is the one we’re searching for.
      * If so, return the word and terminate.
      * If not, move to the middle of whichever half we we know the word to be in (based on whether the word we’re searching for comes before or after the one we just read) and repeat from step 2.

Both of these algorithms involve a similar number of steps per iteration, yet the second will perform much better as input size increases. This is due to the difference between the two algorithms in how the number of additional-iterations-required increases as input size increases. In the first algorithm, increasing the dictionary size from 500 to 1000 words requires 500 additional iterations, whereas the same increase necessitates only 1 additional iteration of the second algorithm.

## Big O notation

In Big O notation, the first algorithm is O(n), where O simply indicates we’re using Big O notation and the stuff in the parentheses (in this case, just n) describes how the rate at which the algorithm performs will change as input size changes. When I say the algorithm is O(n), I’m referring to the worst case. For example, in the worst case this algorithm would need to iterate 500 times before finding the word in a 500-word dictionary. You can also consider the best or average cases; here, it’s simpler to just consider the worst case.

The second algorithm is O(log n), meaning that a doubling of the input size will only require one additional iteration. This is very good.

In general: Big O notation provides a sense of how well or poorly an algorithm will perform with inputs of various sizes. It does not tell you how to calculate exactly how many steps the computer will need to take at any given input size. The notations above leave out information about the number of steps involved in each iteration. Instead, they describe only the [general trend][] of the algorithm’s performance as input size increases. But this information is usually enough for determining which candidate algorithm to use for a particular problem.

[aa]: http://en.wikipedia.org/wiki/Lava#.CA.BBA.CA.BB.C4.81
[general trend]: http://bigocheatsheet.com/#chart
