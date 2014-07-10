---
layout: post
title:  "Clean Code"
date:   2014-05-16
---

The “big picture” lessons that have struck me most:

### Code should be adaptable

While it might at first seem simpler to code with the sole purpose of getting 
things working, the process of changing the resulting code—and almost all code 
must change at some point for some reason—will be complicated to the extent 
that it wasn’t designed to be changed. Moreover, changing it will become more 
and more complicated as more changes are made without regard for how things 
may need to change in the future.

### Modularity is an essential quality of adaptable code

This seems to be the broad principle underlying the greatest number of the 
book’s prescriptions. For example, following the [Single Responsibility 
Principle][] by ensuring that each class has only one reason to change means 
that the repercussions of a given change will be limited in scope, rather than 
rippling out to other parts of the system. Similarly, keeping code [DRY][] 
contributes to adaptability by effectively “modularizing” bits of code that 
had previously appeared in multiple places in the system, meaning changes can 
be made in one place rather than needing to be made in many. In fact, it seems 
like the whole notion of [objects][] was developed largely to facilitate the 
insulation of distinguishable chunks of code from one another.

### Code is read more than it is written, so it should be written accordingly

If you’re not careful, your code’s lucidity may have a hidden dependency; 
namely, being in the state of mind you were in when you wrote it, and had 
effectively been meditating on the surrounding system for an extended period. 
This won’t often be the state of mind of the person reading it, so it’s 
important to continually step back and try to see things from the perspective 
of someone less immersed than you are at that moment. Write code and tests 
that tell the reader the story of how the system works.

### Comprehensive test coverage facilitates the process of changing code

A solid test suite allows you to change your code without worrying about 
whether you’re breaking functionality in remote parts of the system. This 
freedom is beneficial because you’ll probably refactor more if you aren’t 
worried about doing mysterious damage to the system.

### Writing tests before the corresponding code promotes writing clean code

It seems to me that the primary reason for this is that it’s one more nudge 
towards modularity. Doing things [test-first][] forces you to start by 
considering what a given chunk of code (whether a function, class, or 
something else) will look like *from the outside*, rather than immediately 
jumping into the details of how you’d currently like to implement it. Thinking 
this way serves as a reminder that other chunks of code will depend on how the 
current chunk responds to messages, regardless of how it works internally.

### Refactor immediately

It can be tempting to just move on once you get something working, but the 
longer you wait before cleaning up your code, the greater the opportunity for 
the “dirty” code to become entrenched in the system. *Clean Code* references a 
succinct formulation of this notion called LeBlanc’s law: “Later equals never.”

[Single Responsibility Principle]: http://en.wikipedia.org/wiki/Single_responsibility_principle
[DRY]: http://en.wikipedia.org/wiki/Don't_repeat_yourself
[objects]: http://en.wikipedia.org/wiki/Object-oriented_programming
[test-first]: http://en.wikipedia.org/wiki/Test-driven_development
