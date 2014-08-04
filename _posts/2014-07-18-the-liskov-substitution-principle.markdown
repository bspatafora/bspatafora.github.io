---
layout: post
title:  "The Liskov Substitution Principle"
date:   2014-07-18
---

> “Subtypes must be substitutable for their base types”

The Liskov Substitution Principle is a guidline for using inheritance, and in a sense represents the proper definition of inheritance. So it’s worth asking: What does it mean for one object to inherit from another?

Inheritance is often described as an “is-a” relationship. But what does this phrase mean? The phrase itself doesn’t tell you much if you don’t already have a pretty good conception of what inheritance is.

Someone just learning about object-oriented programming could be forgiven for thinking that if one object “is-a”nother object that the two objects are the same thing. But instead, the phrase is meant to convey the idea that the two objects are the same *type* of thing.

Even though most programmers can fill this definition out based on experience and prior knowledge, it begs the question of what needs to be true of two things for them to be considered to be the same type.

Uncle Bob suggests in [PPP][] that, in the context of inheritance, one object has an “is-a” relationship with another when it *behaves as the first one behaves*. That is, if the base class defines a certain behavior, any classes derived from it similarly implement that behavior. They may distinguish themselves from their parent class by defining new behaviors, or even by redefining the implementation details of some of its inherited behaviors, but any redefined methods will do the same type of thing as the corresponding methods in the base class (e.g., a `rpm` method will still return a float even if the details of how it is obtained change).

Put another way, a subclass should adhere to the contract defined by its superclass. If clients of the superclass are used to certain things needing to be the case before invoking one of the class’s methods (e.g., the input must be either a symbol or a string), that understanding should hold true when using any of its derivatives. If the class’s clients expect certain things to be true after invoking a method, invoking that method on one of its subclasses should meet those expectations. Ensuring that subclasses do the kind of thing clients of their super class have come to expect results in a system whose behavior is more consistent and predictable, two things that in turn make it easier to add to or maintain that system.

[PPP]: http://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445
