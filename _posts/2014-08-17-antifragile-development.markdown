---
layout: post
title:  "Antifragile development"
date:   2014-08-17
---

This post is based on a paper called [“Principles of Antifragile Software”][] by Martin Monperrus, a professor at the University of Lille. The paper explores the concept of antifragility, described by Nassim Taleb in his book *Antifragile: Things That Gain from Disorder*, and how software systems can be crafted to use or embody it.

“Antifragile” is defined in the context of the related descriptors, fragile and robust. Upon encountering an error (defined broadly as something that challenges), a fragile system will break and a robust system will not. An antifragile system, in addition to not breaking, will actually improve.

## An example from life

The human immune system variously exhibits all three of these characteristics. Anyone who’s ever come down with a cold has first-hand experience with its occasional fragility. Its impressive robustness becomes apparent when considered in the context how rarely most of us get sick, despite constant encounters with potentially harmful microorganisms. Our resistance to infection is due in part to the plain robustness of generalizing immune strategies like skin and fever, but it also critically depends on our immune system’s ability to remember pathogens, which enables a quicker, stronger response when a pathogen is reencountered.

Immunological memory produces an antifragile system that becomes more resilient as it confronts more “errors.” This illustrates an important point about antifragile systems in general. Whereas robust systems are indifferent to the environment in which they operate, antifragile systems prefer contexts where there are more errors for them to learn from. Or, as Taleb puts it, antifragile systems “love” errors.

## Self-healing software

Monperrus distinguishes between antifragile software and antifragile software development (the former being the result and the latter the process that produces it). His exploration of antifragile software includes a discussion of automatic runtime bug fixing, meaning software that can learn from errors on its own as it encounters them. As a result, the software becomes a little better each time it is presented with a new error.

While some of the self-healing behavior the author identifies may be at this point be theoretical ([DARPA is offering $2 million][] for software that can patch itself after identifying a vulnerability), the general idea seems to be the equivalent of [naturally acquired immunity], in which an organism’s immune system adapts to its environment as it moves through it, rather than preparing for expected threats beforehand. In the same way that letting a kid roll around in the dirt can strengthen their immune system by putting it to work, exposing antifragile software to errors would expands its knowledge of the sorts of threats out there, thereby improving its ability to deal with them.

## Antifragile software development

In his discussion of antifragile software development, Monperrus considers three processes he argues are antifragile: [Conway’s Law][] (which holds that a system’s design tends to mirror the structure and processes of the team that built it), the [“bus factor”][] (how much trouble a project would be in if a given team member got hit by a bus), and test-driven development.

While the other two are important considerations, it seems to me that [test-driven development][] maps very well to the idea of immunization, where a system is treated before the fact for the sorts of challenges it is expected to face. Immunization is error-loving in the sense that the procedure is based on the introduction of errors (in the case of vaccination, deactivated pathogens). TDD is error-loving in two ways.

### Fail first

First, the process of only writing enough code to make the last-written (and initially failing) test pass means the system is actually born out of errors.

Starting with errors and then coming up with their solutions fosters a greater awareness of the side of the system that exists to recognize and handle them. As a result, a system that emerges from this process is more robust than one developed with only the “happy path” in mind.

### Aggressive refactoring

Second, the excellent test coverage that results from well-practiced TDD allows for vigorous and continuous refactoring. This sort of refactoring, where various parts of a system are regularly broken apart and reassembled as requirements change, protects against a system becoming brittle. That is, it ensures that changes to one part of the system do not cause unrelated parts of the system to break.

The continuous “flexing” of the system as a whole ensures that this sort of fragility gets caught and addressed before it becomes systemic.

Changing a complex software system this significantly and so often is daunting if you don’t have confidence in your tests (or if you don’t have any tests at all). When you can only guess at what might have broken elsewhere in the system with each change, you aren’t likely to take on the risk of making a change unless it’s unavoidable.

TDD is an antifragile process. It actually requires that errors be created as a part of its normal functioning, and its resulting coverage provides a great degree of confidence that any errors introduced during refactoring will be known and can therefore be addressed. While the software systems that result may not yet themselves be capable of learning from errors, they will certainly be more robust having been so aggressively exposed to errors throughout their development.

[“Principles of Antifragile Software”]: http://arxiv.org/pdf/1404.3056v1.pdf
[DARPA is offering $2 million]: http://www.tomsguide.com/us/darpa-self-healing-software,news-17761.html
[naturally acquired immunity]: http://en.wikipedia.org/wiki/Immunological_memory#Naturally_acquired_active_immunity
[test-driven development]: http://en.wikipedia.org/wiki/Test-driven_development
[“bus factor”]: http://en.wikipedia.org/wiki/Bus_factor
[Conway’s Law]: http://en.wikipedia.org/wiki/Conway's_law