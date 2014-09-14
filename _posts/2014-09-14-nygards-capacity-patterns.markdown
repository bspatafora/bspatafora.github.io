---
layout: post
title:  "Nygard’s capacity patterns"
date:   2014-09-14
---

An overview of patterns that can help to increase a system’s capacity, from [*Release It!*][], Michael Nygard’s book on designing systems to survive the pressures of production.

  * **“Pool connections”**
      * Creating a connection to a database takes a significant amount of time relative to other system transactions
      * It’s possible to increase performance by maintaining a “pool” of already-created connections; transactions that require a connection can then use one from the pool instead of incurring the costs associated with creating one
      * This pattern only increases performance as long as the resource pool is right-sized; too small and processes will have to sit and wait for an available connection, too large and the database itself may become overburdened maintaining all the connections
  * **“Use caching carefully”**
      * If some content is being retrieved from a database much more frequently than it is being changed, it’s a good candidate for caching, which can bring significant performance gains when applied correctly
      * Content that isn’t accessed frequently relative to how often it changes probably shouldn’t be cached, because the caching itself uses resources
      * Must ensure that caches don’t grow so large that they cause memory constraints and potentially decrease the system’s capacity
      * It’s also important to ensure that cached items get removed or replaced when the underlying resource changes
  * **“Precompute content”**
      * If there are dynamically-generated parts of a website that are displayed frequently but change relatively infrequently, it may be worth precomputing the HTML output rather than needing to generate it for each request
      * Nygard gives the example of an online retailer with a category list that did change with some regularity but which needed to be constructed for many requests in the meantime
  * **“Tune the garbage collector”**
      * The job of the garbage collector is to look through created objects and decide which can be destroyed to free up memory
      * To do this efficiently, it categorizes objects on each pass based on how long they’ve been around (based on the premise that if something’s been around a while it’s likely to stay around a while longer)
      * The size of these categories can be tweaked to make garbage collection more efficient, but it needs to be retweaked with some regularity as the way the application is used changes

[*Release It!*]: http://www.amazon.com/Release-It-Production-Ready-Pragmatic-Programmers/dp/0978739213
