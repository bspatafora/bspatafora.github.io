---
layout: post
title:  "Designing from the outside"
date:   2014-10-26
---

In sitting down to start on my third Tic-tac-toe, I tried a starkly different approach. Where before I had started from the inside and worked out, this time I tried to take the perspective of a client object, deciding what messages I’d want to call before deciding even what object they would live in, much less how the corresponding methods were to be implemented.

In part, this was because of my relative newness when working on the first two. When I started the first, I was very much focused on practicing TDD, and when I started the second, I was still practicing, and balancing that with trying to figure out how to use this Clojure thing.

## Starting with code that’s hard(er) to test

But it wasn’t just the additional experience under my belt that changed my approach, though that certainly played a role. I think that the practice of TDD may actually have had the largest influence. One rule of TDD is that it should be [easy to test][]. If you’re having trouble testing a bit of code, it may be a sign that there’s a problem with how you wrote it. One side effect of this rule is that, at least when you’re getting started, it biases you towards testing the objects that are inherently easier to test.

[As I recently learned][], the objects that tend to be easiest to test are known (to some) as “processors.” Processors are objects take an input, do something with it, and return output (or in some cases change state). Testing them is straightforward. You simply decide on a test input to pass in, set an expectation about what will come out, then check to see whether the actual result is equal to the expected result. Processors tend to make for satisfying tests, and easy ones.

There’s a second class of objects called “collaborators.” Collaborators make calls to processors and aggregate their behavior in order to achieve some higher level behavior. In order to test a collaborator, you oftentimes need to create test doubles that stand in for the processors used within the collaborator. Doing the work to set up the tests for collaborator objects is less fun, and harder, than simply passing an input into a processor and seeing what comes out, so in my first two Tic-tac-toes I saved these tests (and corresponding objects) for last.

## Try to know as little as the object you’re creating

But for my third Tic-tac-toe, I started by thinking about the collaborators first. (I’d like to say I also started by testing them first, but I was unfortunately still working with an unfamiliar language and didn’t feel experienced enough creating test doubles, though I’ve since gotten more comfortable with both.)

I was influenced both by the Windy City Rails talk I linked above, and by reading [POODR][], Sandy Metz’s book about design patterns in Ruby. In it, she talks about the most important part of a system being not the constituent objects themselves, but the messages that get passed between them. By thinking about the types of objects that do their work largely by sending messages (i.e., collaborators), you focus more on the interfaces exposed by the service objects being called. You think first about the *what*, only spending time figuring out the *how* once you know what it is you need (i.e., the sort of thing returned), and what sort of information you’ll be providing to get it (the parameters).

By thinking in this way, you start closer to the ideal state of your collaborator objects; namely, stupid. Objects become coupled to other objects to the extent that they know things about those objects, such as how it is they do what they do. If you’ve just designed the processors your collaborator will use, it becomes much easier to accidentally inject your own knowledge of those processors into the collaborator as you write it.

While this approach carries the danger of planning too much and getting too locked in to the architecture of your system from the start, rather than letting what actually needs to be in place emerge organically, I think the benefits outweigh the risk, especially since it can be mitigated by an awareness of it. Looking at the structure of this third Tic-tac-toe, and how much more decoupled the system feels, I feel confident that thinking from the perspective of a client object first is a worthwhile strategy.

[As I recently learned]: http://www.windycityrails.org/videos/2014/#4
[easy to test]: http://blog.8thlight.com/josh-cheek/2011/10/01/testing-code-thats-hard-to-test.html
[POODR]: http://poodr.com