---
layout: post
title:  "Wrap external dependencies"
date:   2014-11-05
---

I went around today and spoke with three Craftsmen—Jeremy Neander, Ben Voss, and Sandro Padin—about how they approach integrating external services into applications they’re building. I started by asking them whether they take any special precautions when they need to depend on an external service. All of them said that they did. There was some variation, however, in their responses to the question of *why* it is they do so.

## Insulating from change

One reason all of them touched on in one way or another was that by wrapping external dependencies in code you control, you protect your application from changes that might be made to those services.

The primary impetus behind this argument seems to be avoiding unnecessary maintenance. If you have hard dependencies on some service scattered throughout your codebase and then that service changes, it’s going to be much more work to accommodate that change than if your system is only exposed to that service at one single point where it’s wrapped, at the boundary of the application.

While in some simpler code bases the amount of work might be about the same in these two scenarios, in most real projects the combination of multiple external dependencies and use of those services in many places in a large codebase means finding and fixing every place the service is used could be a nightmare.

## Single responsibility

A related argument was that you should wrap external dependencies in order to avoid Single Responsibility Principle violations.

Imagine that you depend on a service that exposes its functionality via a JSON API. If you’re using this service unwrapped throughout your project, each use will involve not only logic related to what you’re doing with the data, but also the parsing of that data into some format usable by your application. This is a case of coupled responsibilities—methods or objects that contain both may change for different reasons. Your domain logic shouldn’t be at risk of breaking because you need to update the way you’re parsing JSON.

## Swapability

A third explanation I received was that by exposing only the abstract idea of the service you need rather than the details of how the particular service you’re using is implemented, you make it easier to swap out one service for another that fulfills the same need.

The underlying motivation for this argument is the same as the first argument: reducing the cost of change. If, for (Jeremy’s) example, you’re using one authentication service and need to switch to another, or want to add multiple ways for your users to log in, your job will be much easier if your application depends not on the quirks of the service you started off with but rather the abstract notion of authenticating a user.

## Reducing complexity by shrinking interfaces

One argument I thought was particularly interesting was that wrapping external dependencies can actually decrease the complexity of your system.

Typically the argument you hear against doing so is that it adds a level of indirection and therefore complexity to your application. But as Sandro pointed out, oftentimes when you’re wrapping an external service you’ll shrink its interface to just those methods that you actually need. In addition, it’s sometimes possible to reduce number of parameters required by a method you’re wrapping. By simplifying the interfaces of the services you use, you reduce the number of choices you’re presented with when you or others are developing the application.

## Reducing complexity by working at the right level of abstraction

A similar argument that was hinted at was that it’s simpler to develop when you’re working at a single level of abstraction. Wrapping external dependencies often involves parsing data into the format expected by your application, and so removes this low-level detail from the high-level domain logic at your application’s core.

## Testability

Finally, Ben and Sandro pointed out that wrapping external dependencies makes it easier to test the rest of your system. If you need to make calls to your database in order to run your test suite, the amount of time this adds will likely lead you to run the suite less often, making TDD difficult or seemingly impossible. The benefit here of course is that by making the rest your system easier to test, you gain all of the benefits, expounded on elsewhere, of test-driven development.

The argument against wrapping external dependencies seems to be that doing so can make it less immediately apparent what services you’re using. While this is a valid concern, and may be overriding in very simple applications that will never grow in complexity, I think in most cases the arguments above more than justify the added complexity.