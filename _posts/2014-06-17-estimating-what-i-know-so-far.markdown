---
layout: post
title:  "Estimating—What I know so far"
date:   2014-06-17
---

Not much, since this is the first week I’ve had to make hard estimates of how much time particular things will take, but I’ve heard some things, and read some things, and have a rough sense of what the process entails.

In an [agile][] process, estimates are done on user stories. These stories are essentially feature descriptions, but they are phrased in such a way that, as the name suggests, they tell a “story” about a particular sort of person who has some need that they would like to have satisfied by an imagined feature. A user story for my Tic-tac-toe game, for example, might look something like this: “As an avid Tic-tac-toe player, I want to be able to see the move histories and outcomes of previous games.” There are probably lots of different reasons why user stories are supposed to be the way they’re supposed to be, but I think one of them is that they help define a good granularity.

Estimation can happen at any level of granularity, from the entirety of a large project to its tiniest details, but the concept of the user story narrows this range significantly. The most important pressure it exerts is to focus the attention of the involved parties down to the level of the small and the self-contained. In the Tic-tac-toe user story, for example, the identified feature is something that could be added on a timescale of hours or days rather than weeks or months, and it talks about a single feature in isolation.

This is beneficial for a couple reasons. First, it’s a lot easier to estimate how much time it will take to implement something like this than it is to imagine how long it’ll be before the project as a whole is complete. The latter estimation would be less accurate for the simple reason that it involves more estimating. Second, smaller stories make it possible to show new features to stakeholders more frequently, which produces feedback that can be used to progressively mold the system based on what is actually needed.

But though its primary pressure is towards more granularity, the user story also establishes a boundary to keep things from going too far in that direction. It has a generalness that not only leaves the implementation details of the feature to whomever is actually going to be implementing it, but also seems to recognize that estimating is inherently difficult.

Estimating is trying to predict the future, and that’s a hard thing to do. It helps to use the PERT system of estimation, which forces you to consider multiple possible futures (based on optimistic, realistic, and pessimistic outlooks). It helps to have a lot of experience to draw on in trying to imagine what an implementation will look like and what might impede progress.  But things come up, both in development and outside of it. This is why, in addition to using all the tools at your disposal to make your estimates as accurate as possible, it’s essential to be aware of and honest about the fact that the best you can do is minimize—not eliminate—uncertainty.

[agile]: http://en.wikipedia.org/wiki/Agile_software_development
