---
layout: post
title:  "Learning through testing"
date:   2014-05-02
---

Since joining 8th Light as a student apprentice, I’ve spent a lot of time reading and hearing and thinking about software testing. By all accounts, it’s one of the most well-worn and valuable tools at a [craftsman’s][] disposal.  There are as many arguments for why this is as there are advocates for the practice, but it has so far seemed to me that they fall roughly under two broad themes:

1. Maintaining a comprehensive test suite facilitates the process of changing code
2. Writing tests before the corresponding code promotes writing clean code

I’ve heard more, and more nuanced, arguments for the second class, perhaps because it’s the more contentious of the two. But one argument I haven’t heard—at least not as explicitly or in the form that it struck me—is that you should write tests because the writing tests you back. A concrete example is probably the best way to explain what I mean.

As a part of my apprenticeship, I’ve been working on implementing a game in a programming language. (I don’t think the particulars are important here, except that I was writing the game to be playable via a command-line interface.) When I first wrote my `play` function—the high-level description of how to step through a single iteration of the game—it included code that looked something like the following:

    if computer_player_has_won:
      print_to_console "Computer wins!"

At the time, I didn’t think twice about the soundness of this. After all, it did exactly what I wanted it to—I’d even factored out the `computer_player_has_won` bit! The first hint something was amiss came when I realized I had no idea how to go about testing the function. (The irony that I might have avoided this problem entirely if I had been consistently [testing first][] is not lost on me.)

The solution to this particular problem came in the form of a blog post my mentor Arlandis passed along. It’s called [Testing Code That’s Hard To Test][] and it’s well worth a read-through if the mistake I made isn’t obvious to you.

The proximate change that resulted from this sequence of events was that I factored the CLI-specific output command out of the function. But the broader and more consequential effect was that it cued me in to the very idea of a (big, important) distinction between an application’s core logic and the particular way it handles input and output. It wasn’t the code I was writing, but rather the tests I was trying to write that forced me to improve my conceptual understanding of the system I was putting together.

In a way, the tests were testing me, because writing them was contingent on developing a greater awareness of what it was I was trying to do.

[craftsman’s]: http://8thlight.com/principles
[testing first]: http://en.wikipedia.org/wiki/Test-driven_development
[Testing Code That’s Hard To Test]: http://blog.8thlight.com/josh-cheek/2011/10/01/testing-code-thats-hard-to-test.html
