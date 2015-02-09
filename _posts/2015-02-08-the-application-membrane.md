---
layout: post
title:  "The application membrane"
date:   2015-02-08
---

Quick refresher from Bio 101. Living things are or are made up of cells. Each cell, while working in concert with others, contains its own machinery for performing functions like replication and protein manufacture. What separates each cell from its neighbors and allows it to act as an independent unit is its membrane. Our cells are wholly enclosed by their membranes, and yet those membranes must also allow them to interface with their external environment.

This same tension—the need to collaborate, while still clearly defining the boundary between self and other—is also present in software development. It’s a rare application that has no need to communicate with some entity outside its walls. Online stores communicate with credit card processors, mobile games let users to log in with their Facebook identities, and just about everything uses some sort of data store.

## Boundaries

Given this need to connect, it sometimes feels as though the right approach would be to allow the boundaries between your application and the outside world to dissolve. Does it really make sense to erect artificial-feeling barriers between your application and the web framework it depends on to interact with your users? The answer, I’d argue, is only as much sense as it makes for cells to have membranes. There are good reasons for both cells and software systems to develop boundaries with the outside world, and the reasons are similar.

There was a time in the development of the cell when the boundary between it and its environment was nebulous. But, after 3 billion years of evolving, cells of all descriptions are united by the fact that they carefully regulate the stuff allowed through their gates. I’d hazard a guess that the primary reason for this is the existence of threats in the external environment. Without an effective boundary between it and the outside world, a cell is vulnerable to things like viruses which, if let in, change the cell, shaping it to serve the virus’s ends.

The same threat exists for applications. The obvious connection here is with the malicious bits of software we’ve metaphorically termed computer viruses, but this threat is present even in services an application willingly chooses to interface with in order to do its job. If care is not taken to maintain separation between an application and the external services it uses, those services will influence the development of the application in ways that make it more rigid and more fragile.

Consider what happens when an application is designed to use a particular database, and no effort is made to insulate its domain logic from the details of how that database is controlled. If the application grows to a decent size, database queries will permeate the codebase. The database will have spread the details of its workings throughout the system, and the application’s code will likely show signs of its joint development alongside the database.

## The costs of entanglement

Two serious problems result from this. First, the application has blithely entangled its fate with that of its database. If the details of how that database is accessed change for any reason, the application will be forced to change along with it. Second, the application has been given too much knowledge about the specific details of that particular database. The application knows exactly how to interact with the database it developed with, meaning it knows *only* how to interact with that database. The decision to switch to a different storage mechanism will therefore be a costly one, despite the fact the new service fills the same basic need as the old one.

Both issues can be avoided by enveloping your application in a membrane—that is, by minimizing its exposure external agents as much as possible. But what should this membrane look like? How should it function? Obviously your application needs to know *something* about the services it depends on. Here again, it pays off to take a cue from nature.

## Building a wall

When a cell needs to consume a complex molecule from beyond its boundaries, it engages in a process called endocytosis. What this means is that it first wraps the molecule in a layer of its own lipids before bringing it inside.

This process has a close analogue in software development. When working with external services, it’s wise to wrap those dependencies in something you own. One strategy for doing this is the façade pattern. Here, the parts of your application need to interact with the external service don’t do so directly. Instead, they interact with an internal object that been designed to pass messages it receives from other parts of the application along to the external service in the way it knows to be appropriate.

While wrapping an external service in something you own does add a layer of indirection to your application, what it buys you is typically more than enough to justify the bit of added complexity. Now, when that service changes, the damage it can impose on the application is isolated to the confines of its wrapper.

A second way of achieving this sort of insulation from external services is by depending on abstractions rather than concrete details. If you needed to use an external service in a statically-typed language like Java, you might define an interface to which that service could adhere. The application could then depend only on that interface, rather than the specific details of any particular service.

In this way the application becomes capable of working with another service that does that same sort of thing rather than be bound to one particular implementation of that idea. An application that depends on *what* it needs to do will be much more flexible than an application that also depends on *how* those things get done.

Applications, like cells, are vulnerable to changes in their environment. Just as cells have developed different strategies for insulating themselves from the outside world, there are plenty of different strategies a system can employ to minimize unnecessary entanglement with the things that surround it.