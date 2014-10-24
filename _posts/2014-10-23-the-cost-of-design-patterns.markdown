---
layout: post
title:  "The cost of design patterns"
date:   2014-10-23
---

Design patterns are wonderful things. They allow you to avoid reinventing the wheel, instead learning from those that came before. Design patterns represent accumulated knowledge, knowledge that no one developer could hope to gather on their own. They are the tried-and-true, the solutions to problems that have come up time and time again.

For these reasons, it’s important to know your design patterns, and to be able to refer to them by their names, both to be able to implement them and to be able to speak about them when brainstorming solutions with team members. But implementing them does have a cost. That cost is complexity.

## Design patterns add complexity

Any time you implement a design pattern, you are adding structure to your code base, and additional structure means additional complexity. Now, that complexity may be worth it. Sometimes, complexity helps manage greater complexity.

For example, the [proxy pattern][] adds complexity by adding a layer of indirection to your system. Instead of calling the object of interest, a client calls the proxy object, which in turn sends a corresponding message to the actual target, then passes the result back to the original caller. This is obviously more complex than the situation it replaced, but if the object wrapped by the proxy is itself a complex one, either because it has a much larger interface than the client needs or because it changes often, the additional complexity incurred by using the proxy pattern may be justified. Though the additional structure of a design pattern necessarily adds complexity, the net result of adding the design pattern can be a reduction in complexity if it has been applied correctly.

## Wait until you *know* you need it

When you’re considering about using a design pattern, it’s essential to think about whether it’s worth the cost. You want to be sure that its complexity will buy you less complexity overall. To avoid the cost when it is not needed, you should avoid using design patterns until you feel forced to use them.

Even though you might have a serious hunch that the code you’re writing is a perfect candidate for design pattern X, you should wait to implement it until you feel the pain that that design pattern is meant to alleviate. If you do so before you actually encounter the need, it’s premature optimization (in this case, for complexity rather than performance), and there’s a good chance you’ve applied it in the wrong place.

Further, it may turn out that you didn’t need that design pattern after all, in which case you have not only additional complexity, but needless complexity. At this point, you have a choice to either leave your system in state of needless complexity, or waste time removing the complexity that you needlessly added. Better not to get to that point in the first place.

[proxy pattern]: http://en.wikipedia.org/wiki/Proxy_pattern