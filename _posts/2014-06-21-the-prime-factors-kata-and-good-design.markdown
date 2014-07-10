---
layout: post
title:  "The prime factors kata and good design"
date:   2014-06-21
---


I spent part of this week working on the [prime factors kata][], the first 
[programming kata][] I’ve done. While it seems that most katas emphasize 
test-driven development, the prime factors kata seems to underline a 
particular reason for adhering to the practice.

In this [“Coding Dojo” discussion][] of the kata, Uncle Bob says he likely 
wouldn’t have come up with as elegant a solution if he hadn’t written the code 
incrementally, making only the smallest change necessary to get the current 
test to pass. If he had instead tried to just guess at what the solution would 
ultimately look like, he might have ended up with a `generate_primes` method, 
something that might be called “needlessly complex,” given that the kata 
solution doesn’t require such a method.

I sometimes find myself tempted to just start coding, figuring that I have a 
good-enough idea of how things will look when I’ve got everything implemented. 
I’m tempted by this even though I’ve been convinced that an iterative, 
test-driven approach leads to more flexible and modular systems than 
[big design up front][]. But it sometimes just seems so obvious that certain 
entities or bits of code will be necessary components of the final 
implementation that it seems harmless to just get them in there.

What made this kata such a good exercise was that it demonstrated very clearly 
that this sort of jumping-ahead can actually prevent you from coming to the 
most elegant solution to a problem. I know that I would have assumed that a 
`generate_primes` method would be a necessary component of the solution, and 
probably would have thought it was a pretty good place to start, making it 
unlikely that I’d come up with something like the kata solution. Doing this 
kata was an excellent reminder that much of good design is doing the least 
necessary to solve the problem, and TDD can be an effective process for 
finding elegant, minimalistic solutions.

[prime factors kata]: http://butunclebob.com/ArticleS.UncleBob.ThePrimeFactorsKata
[programming kata]: http://en.wikipedia.org/wiki/Kata_(programming)
[“Coding Dojo” discussion]: http://vimeo.com/2499161
[big design up front]: http://en.wikipedia.org/wiki/Big_Design_Up_Front
