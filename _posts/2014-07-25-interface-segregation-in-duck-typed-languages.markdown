---
layout: post
title:  "Interface segregation in duck-typed languages"
date:   2014-07-25
---

I’ve been thinking a lot about the Interface Segregation Principle recently. 
When I first read the chapter on it in [PPP][], I thought I had a pretty good 
grasp of it. I thought this despite never having worked in a statically-typed 
language.

Both of the languages used in PPP—C++ and Java—are statically-typed. In many 
chapters, the use of statically-typed languages seems only to be for need of 
an example language. Given the popularity of statically-typed languages at the 
time PPP was written, this isn’t much of a surprise. But in the chapter on the 
ISP, the focus on statically-typed languages seemed less incidental.

My thinking this may have been influenced by something I’d heard during my 
student apprenticeship. At one point, I remember overhearing some residents 
talking about how Sandi Metz crosses out the ISP in one of her talks on the 
SOLID principles and Ruby, saying that Ruby developers didn’t need to worry 
about it because duck-typing took care of it for them. (I’ve since found and 
watched [the talk][], which she gave at GORUCO 2009.)

But, having talked with a few craftsmen about this question over the last 
week, there seems to be a different opinion at 8th Light. But before I get 
into how the notion of interface segregation may apply to duck-typed 
languages, I just want to briefly recap how it applies to statically-typed 
languages, and why it might seem as though it only applies to such languages, 
at least based on how it's treated in PPP.

PPP provides two examples demonstrating violations of the Interface 
Segregation Principle. Though they differ in domain and scope, these examples 
are united by the fact that they illustrate a narrower conception of the 
principle than the conception the craftsmen I talked with have. The problem in 
the examples in PPP is that multiple implementations of an abstract interface 
have their fates bound together such that changes to one can force changes to 
other classes implementing the same interface, even though they don’t use the 
same methods of the interface. This happens because in a statically-typed 
language, a class can only implement an interface if it defines all of the 
methods specified by that interface. In such languages, it can be a hassle to 
update a class that implements a given interface to match the updated 
definition of that interface because of how long it can take to recompile 
after such a change. For this reason, PPP suggests that if multiple kinds of 
classes are using different parts of an interface, that that interface should 
be split into multiple interfaces, each of which could serve an individual 
type of client object.

This specific problem doesn’t exist at all in duck-typed languages. That’s 
because the notion of a concrete interface doesn’t exist in such languages. 
Instead, the notion of an interface emerges out of the implicit relationship 
between entities with the same public facing methods. So the problem 
identified by the examples in PPP is not a problem at all in a language like 
Ruby.

But there may be reason to take a broader conception of the Interface 
Segregation Principle, one broad enough to apply to duck-typed languages as 
well as statically-typed ones. This broader principle is closely related to 
the Single Responsibility Principle. It warns that if you bind things together 
that shouldn't go together, your system will break more easily than if you 
separated those things and prevented changes from some of them from affecting 
the others.

The difference between this conception of the ISP and the Single 
Responsibility Principle is that the SRP takes the perspective of the service 
entity—that is, the entity providing an interface for other parts of the 
system to use—while the ISP takes the perspective of the client entities that 
use these service entities. But just as with the SRP, ISP violations result in 
code that is less expressive and more fragile. An interface serving many 
different kinds of entities will be more difficult to understand for the 
simple reason that there is more to understand in one place, and it’s likely 
there won’t be a strong conceptual relationship among the different sorts of 
clients using the interface. And in addition, the added complexity can result 
in a system in which changes to a certain type of thing inappropriately affect 
other parts of the system that shouldn’t need to change.

[PPP]: http://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445
[the talk]: http://www.confreaks.com/videos/240-goruco2009-solid-object-oriented-design
