---
layout: post
title:  "Ports-and-Adapters Architecture (aka Hexagonal Architecture)"
date:   2014-07-06
---


When you’re putting together a system, it can be tempting to go about things in the 
simplest way possible. And in fact this is usually the right approach. Even if you 
think you know *just* the design pattern for the particular problem you imagine yourself 
facing, it’s worth taking a step back and asking yourself how certain you are of that. 
While useful, design patterns almost always introduce additional complexity to a system, 
and if it turns out that you didn’t really need to use the pattern after all, you’ve 
now spent (a probably nontrivial amount of) time adding needless complexity to your system.

Oftentimes, the honest answer to whether you *know* you will need a particular design pattern 
is “no.” Most software systems have pressures continuously exerted on them from a variety 
of directions (including, for example, changes to the requirements by the client or the 
decision to start using a different database), and it’s really difficult to predict what 
sorts of pressures may alter the context of the problem you anticipate arising and 
justifying the use of whatever design pattern you have in mind.

This is not to say it’s never reasonable to design things a certain way in anticipation of 
future problems. Sometimes, you do know for sure that your system will have a particular 
component added, or will need to interface with some external service. I would also argue 
that it’s reasonable to implement certain design patterns even when you aren’t certain about 
the specific ways your system will be changing. The sort of design patterns I’m talking 
about are those that (a) exist at the level of system architecture and (b) apply 
broadly to many types of systems.

I don’t actually know how many design patterns fall into this category, but I believe 
Ports-and-Adapters is one of them. Ports-and-Adapters Architecture (which I’m going to abbreviate 
PAA) is a design pattern that can improve the flexibility and portability of many different 
systems, regardless of most specifics of what those systems are doing. Really the only 
thing that needs to apply is that the system meets the following criteria:

* It has some core logic that is particular to the problem the system exists to solve
* It uses external services (like a database or UI)
* There’s some possibility one or more of those services will someday change

It seems to me the first two criteria apply to pretty much every software system out there. 
The third only applies to most software systems out there. So there’s a pretty good 
likelihood a given system would benefit from PAA.

While “Ports-and-Adapters” and (especially) “Hexagonal Architecture” make this pattern sound 
pretty complex and a little mysterious, the idea is actually a simple one. PAA means insulating 
your system’s core logic from the details of its external services. It suggests doing so by 
means of “adapters,” or entities that act as intermediaries between the core logic and a given 
service in order to keep the core logic ignorant of that service’s details. So, for example, 
instead of allowing one of your core-logic classes to read from your PostgreSQL database 
directly, PAA would suggest creating an adapter class that your core-logic class could call 
to get the data it needs without needing to know where that data is ultimately coming from. In 
this way, it becomes much easier to keep your core logic in a state where it is easy to reuse 
in other contexts (i.e., with a different set of external services.)

### Implementing Ports-and-Adapters Architecture in a Rails app

It’s not uncommon for core logic to find its way into Rails controllers. When this happens, it 
becomes that much harder to extract what should be framework-agnostic code for reuse in other 
contexts (for example, using another web framework).

Here’s a controller with a responsibility that should be handled by a core-logic entity rather 
than in an external service (in this case, Rails):

{% gist bspatafora/8d1adbf204e12a330f1d %}

The problem is the conditional logic. That if-block is saying “based on something decided at the 
level of core logic, do one thing or another.” While both of the things to be done are exactly 
the sort of thing a controller should be doing (rendering a webpage, possibly with some data 
that the controller itself doesn’t know much about), it is not the responsibility of a controller 
to know how to make the decision between the two. Moreover, while the stuff inside the if and 
else blocks is Rails-specific, the logic of doing one thing or another (regardless of what 
those actions may be) depending on whether some input is valid is not Rails-specific, but is 
not reusable so long as it lives in a Rails controller.

Here’s a refactoring that implements the sort of structure PAA advocates:

{% gist bspatafora/8750c1ad23af4d296ebe %}

Now, the controller simply tells a core-logic entity (`PrimeFactorerAdapter`) that a certain thing 
has happened (“someone wants some prime factors for this number”) and lets that entity decide what 
should happen as a result. Even though the adapter is going to call one of the controller’s other 
two methods depending on the input, the controller is ignorant of the connection between its call 
to the adapter and the adapter’s callback with either `input_was_valid` or `input_was_invalid`. The 
core logic of deciding what to do given valid or invalid input now lives in the core-logic entity 
`PrimeFactorerAdapter` and has become more easily reusable with another web framework (or other 
sort of UI service) as a result.
