---
layout: post
title:  "To write antifragile software, learn to love errors"
date:   2014-08-17
---

I just stumbled across a short paper called “Principles of Antifragile Software” by a professor named Martin Monperrus. It explores the concept of antifragility, described by Nassim Taleb in his book *Antifragile: Things That Gain from Disorder*, and how software systems can be crafted to embody it. The paper itself discusses a whole lot more than I do, and is definitely [worth the read][].

“Antifragile” is defined in the context of the related descriptors, fragile and robust. Upon encountering an error (defined broadly as something that challenges), a fragile system will break and a robust system will not. An antifragile system, in addition to not breaking, will actually improve.

## An example

The human immune system variously exhibits all three of these characteristics. Anyone who’s ever come down with a cold has first-hand experience with its occasional fragility. Its impressive robustness becomes apparent when you consider how rarely most of us get sick, despite constant encounters with potentially harmful microorganisms. Our resistance to infection is due in part to the plain robustness of generalizing immune strategies like skin and fever, but it also critically depends on our immune system’s ability to remember pathogens, which enables a quicker, stronger response when a pathogen is reencountered.

Immunological memory produces an antifragile system that becomes more resilient as it confronts more “errors.” This illustrates an important point about antifragile systems in general. Whereas robust systems are indifferent to the environment in which they operate, antifragile systems prefer contexts where there are more errors for them to learn from. Or, as Taleb puts it, antifragile systems “love” errors.

## Antifragile software

Monperrus distinguishes between antifragile software and antifragile software development. His exploration of antifragile software includes a discussion of automatic runtime bug fixing, meaning software that can learn from errors on its own as it encounters them. While I don’t know anything about the details of how to go about making self-healing software (though now that I know it exists, I’d like to learn), it seems to be the equivalent of [naturally acquired immunity], in which an organism’s immune system adapts to its environment as it moves through that environment, rather than preparing for expected threats beforehand.

## Antifragile software development

In his discussion of antifragile software development, the author considers three processes he argues are antifragile: [test-driven development][], the [“bus factor”][] (how much trouble a project would be in if a given team member got hit by a bus), and [Conway’s Law][] (a system’s design tends to mirror the structure and processes of the team that built it).

While the other two are very important considerations, it seems to me that excellent test coverage (however one arrives at it) is a precondition for the “immunization” of a software system that results from vigorous and continuous refactoring. This sort of refactoring, where various parts of a system are regularly broken apart and reassembled as requirements change, “flexes” the system as a whole, ensuring that no part of it can become too brittle over time.

Changing a complex software system this significantly and so often is daunting if you don’t have confidence in your tests (or if you don’t have any tests at all). When you can only guess at what might have broken elsewhere in the system with each change, you aren’t likely to take on the risk of making a change unless it’s unavoidable. But if you know you can rely on your tests to tell you when you’ve broken something, introducing “errors” is no longer something to be feared. Instead, they can be confidently sought out and dealt with before they can grow into something really scary.

[worth the read]: http://arxiv.org/pdf/1404.3056v1.pdf
[naturally acquired immunity]: http://en.wikipedia.org/wiki/Immunological_memory#Naturally_acquired_active_immunity
[test-driven development]: http://en.wikipedia.org/wiki/Test-driven_development
[“bus factor”]: http://en.wikipedia.org/wiki/Bus_factor
[Conway’s Law]: http://en.wikipedia.org/wiki/Conway's_law