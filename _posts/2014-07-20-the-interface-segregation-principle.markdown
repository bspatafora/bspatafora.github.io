---
layout: post
title:  "The Interface Segregation Principle"
date:   2014-07-20
---

> “Clients should not be forced to depend on methods that they do not use”

The Interface Segregation Principle is primarily concerned with a problem that 
can develop in statically-typed languages. In such languages, an interface is 
an actual entity defined explicitly in code.

In duck-typed languages like Ruby, there’s no need to create an explicit 
interface at all. In a duck-typed language, interfaces are implicit constructs 
that emerge when there is a group of entities that all implement some set of 
methods (i.e., have methods with the same names that do the same sort of 
thing, even if the details of what happens differs depending on the entity). 
Oftentimes the best way to define these interfaces is through unit testing, 
which provides some degree of documentation. But interfaces in duck-typed 
languages are still nowhere near as explicit as a statically-typed language, 
for either the human reading the code or the computer interpreting or 
compiling it.

In a statically-typed language like Java, an interface is an actual entity 
that explicitly defines the methods that objects that implement that interface 
will implement.

What sometimes happens in these sorts of languages is that two entities that 
do related-but-different things use the same interface. The example used in 
[PPP][] is a `Door` and a `TimedDoor` both implementing a `Door` interface. 
This can be problematic if one of the entities forces changes on the interface 
(for example, if a `TimedDoor` needs a `timeOut` method to work with some 
other `Timer` object). The fact that any entities implementing the interface 
depend on that interface means that they are coupled to one another, and 
all must change if any of them needs the interface to change. Similarly to the  
Single-Responsibility Principle, the ISP suggests breaking these sorts of 
linkages when they are resulting in changes to one part of the system (the 
`TimedDoor` class) causing unnecessary changes to parts of the system (the 
`Door` class) that otherwise should not need to change.

[PPP]: http://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445
