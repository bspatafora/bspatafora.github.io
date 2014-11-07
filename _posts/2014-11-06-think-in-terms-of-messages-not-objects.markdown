---
layout: post
title:  "Think in terms of messages, not objects"
date:   2014-11-07
---

I’m working my way through Sandy Metz’s book, *Practical Object-Oriented Design in Ruby*. As like 4 or 5 residents promised, it’s an excellent book. Metz has an extremely good grasp on the theory behind designing flexible and comprehensible software, and zeros in on the subset of that theory that provides the highest bang for your buck. She’s also got a great way of framing and explaining the concepts she discusses.

## Don’t think in terms of objects

The thing that has struck me most so far is her reframing of how to think about the stuff inside an object-oriented system. Maybe it’s because of the name, but I (and I think many others) started out thinking about object-oriented software in terms of classes and objects. The first thing I did when planning out my first Tic-tac-toe for example was list out the classes and objects I thought I would need.

One problem with this is that I was almost certainly doing too much upfront design. But Metz identifies a more interesting flaw in this approach. What really matters in an object-oriented system, she argues, isn’t the objects, but the messages sent between them.

## Start with what is needed, not how it’s made

One reason this is a useful reframing is that it refocuses you on [objects as clients][].

When you start off by thinking in terms of objects, it’s hard not to think about what those objects do; that is, the details of their internal behavior, the stuff they’re supposed to hide from other objects. This creates, I’ve found, a greater likelihood that you will unknowingly endow your objects with some of your knowledge as omniscient system architect, increasing how much those objects know about their context and so increasing their rigidity and fragility.

To think in terms of messages, on the other hand, is to think in terms of interfaces. This approach will lead to more modular—and therefore adaptable—software.

## Let the messages guide you

Another benefit of Metz’s approach is that avoiding starting off thinking about objects actually leads to better-designed objects.

This echoes the “avoid big design up front” argument. You may think you know what objects you’ll need and what they’re do just by thinking about your system in the abstract, but you probably don’t. What Metz advocates instead is is thinking about the messages that will need to be sent before you even begin to consider who will send them.

In this way, you can start grouping messages based on the underlying data they’ll need access to, and from there start constructing your first object. While it’s not a cure-all, this approach is likely to lead to objects with narrower, more focused responsibilities.

As Metz herself would point out, theory is only as good as it’s applicable. But while “think in terms of messages” is pretty abstract advice, I feel like the increased clarity it has given me will directly lead to better designed software.

[objects as clients]: http://bspatafora.com/blog/designing-from-the-outside/