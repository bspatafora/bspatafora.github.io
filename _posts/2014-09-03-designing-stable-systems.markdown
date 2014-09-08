---
layout: post
title:  "Designing stable systems"
date:   2014-09-03
---

I’ve been working through a book called [“Release It!”][] along with 8th Light’s distributed services discussion group, and I wanted to mark down some thoughts I’ve had so far. Even though I’m not in the best position to gain from the book, given that I haven’t worked on any large-scale, distributed software systems, many of the book’s points have resonated with me.

## The overarching message: Assume failures will occur

As much as you would like (and should try) to put together a system that does not fail, it’s an unrealistic goal for all but the most trivial and isolated systems. First, humans aren’t perfect, so the systems they design aren’t either. Second, distributed systems tend to work over the internet, and network conditions are unreliable. Third, most complex systems connect to numerous external services (like a credit card processing service, for instance), and failure on the part of any of these can easily result in failure in or of your system.

What should you do given this? Plan for failure. Design the system in such a way that when failures do occur, they can be resolved without affecting the normal operation of the system, or at least do not ripple through the rest of the system causing additional damage.

## Cracks in the system

Failure of part or all of a distributed system can occur for many reasons. The most common underlying cause is tight coupling.

Imagine an application that makes calls to some remote service (this could again be a credit card processing service). Now imagine that service is temporarily unable to fulfill requests, perhaps because it’s experiencing unusually high levels of traffic.

The extent to which the client application is affected will depend on whether care has been taken to keep it isolated from other system components, such as the swamped service. If this modularity hasn’t been afforded, the “crack” started by the one component may spread throughout the system.

## “Stability patterns”

The book identifies 8 patterns that can help prevent something like the above from happening when the inevitable failure does occur somewhere in a system. Two I found particularly interesting are circuit breaker and handshaking.

**Circuit breaker** involves wrapping dangerous operations in a proxy entity. Instead of calling the operation directly, clients call the circuit breaker, which will usually then pass the call along to the entity responsible for the dangerous operation. However, if the circuit breaker detects the operation failing too much too fast, it will cause further calls to immediately fail. In this way, the failed component (maybe an overloaded database server) has a chance to recover, rather than continuing to get hammered by requests it cannot fulfill. After a time, the circuit breaker will flip “half-closed,” trying an operation and either reopening if it fails or returning to its functioning, closed state if it succeeds.

**Handshaking** enables the service component itself to avoid getting “kicked while (or until) it’s down” by informing clients when it has reached or will soon reach its service capacity. One way of implementing this is to require that a client first make a “health check” request to the service component, only making its actual request if the server indicates it will be able to complete it.

Requests can be expensive, however, so this pattern also illustrates that there can sometimes be a tension between stability and performance. Taking steps to increase a system’s stability may also increase its complexity, as is true to some extent with both of the above implementations.

This is not to say that these steps should not be taken, only that they be used judiciously. If the author’s deep experience with complex, distributed systems is any indication, there are many cases where a system can be made substantially more resilient with relatively little cost to performance or complexity.

[“Release It!”]: http://www.amazon.com/Release-It-Production-Ready-Pragmatic-Programmers/dp/0978739213
