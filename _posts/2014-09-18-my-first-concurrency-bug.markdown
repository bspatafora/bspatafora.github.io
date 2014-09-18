---
layout: post
title:  "My first concurrency bug"
date:   2014-09-18
---

I’m writing a simple HTTP server in Java. Among other things, it needs to pass two tests. First, it needs to be able to accept a PUT request to add a string to a resource, then a DELETE request to remove it. The state of the resource (i.e., whether it holds  a string like “data=cosby” or not) must persist for at least as long as a server process runs. The acceptance test verifies whether the server can do this by following each of the above requests with a separate GET request, with the expectation that the the previously-sent string be either present in (when following the PUT request) or absent from (after the DELETE) the body of the response. The second test requires that the server be multi-threaded (i.e, able to accept and respond to many requests simultaneously).

## A sometimes bug

I’d implemented the functionality to pass these tests, and had moved on to another story, when one of my tests broke. The test that broke was an integration test I’d written to provide a from-within counterpart to the from-outside-the-system PUT-GET-DELETE-GET acceptance test. This surprised me, as it had been passing since I wrote the code to make it pass. Moreover, the functionality I’d moved on to implementing should not have had any bearing on the server’s ability to handle PUT and DELETE requests.

So I ran it again, just in case my computer had gotten in the way of some passing cosmic ray as JUnit was executing. This time, it passed. Hmm. I ran it a few more times, and they all passed as well. Maybe the cosmic ray thing had actually happened! Then it failed again.

## A race condition

I remembered from Clean Code that concurrency issues can seemingly occur at random and for no apparent reason (and also that lazy developers will sometimes try to explain away infrequent-enough concurrency issues as cosmic ray interference). The concurrency bug theory seemed likely, given that the test that failed was the only one running multiple threads.

After a goodly amount of head scratching, a rubber duck session, and a lot of reading up on handling multithreading issues in Java, it turned out the real issue was that I needed to enable and run this test on a single-threaded version of my server. The issue had actually been that the thread handling the final GET request (the one that’s supposed to run after the DELETE request and verify it has done its thing) was occasionally getting started before the thread for the DELETE request that was supposed to precede it.

## The perils of concurrency

Even though the solution didn’t end up being as interesting as I’d assumed it would be, the process of arriving at it forced me to consider both the issues that can arise when using multi-threading and some potential solutions.

In particular, I’ve become aware that if I send my multi-threaded server a series of requests, it’s very possible that they may get processed out of order. I had at first assumed that the reason for the sometimes-changed order was that the DELETE thread was sometimes taking longer to complete than the GET thread, even with the head start of being created first in the test. But after some failed experiments using Java [Locks][], which can be used to ensure only one thread at a time can execute some chunk of code, it seems that the issue was actually caused by the GET thread starting before the DELETE thread.

I also realized that, while it wasn’t the source of this bug, I’ve introduced the possibility that two threads try to update the same, shared resource at once. This is known as thread interference, and can result in the resource storing a different value than intended. Java allows you to create [synchronized][] methods, which are one strategy for avoiding such interference by ensuring that concurrent writes and reads by different threads don’t interleave and result in the storage of an incorrect value.

The whole episode made me more aware of how important it is to consider the consequences of moving away from serial processing. Given how complex and difficult to reason about concurrency bugs can become, I’m going to be more cautious about my use of multi-threading going forward.

[Locks]: http://docs.oracle.com/javase/tutorial/essential/concurrency/newlocks.html
[synchronized]: http://docs.oracle.com/javase/tutorial/essential/concurrency/syncmeth.html