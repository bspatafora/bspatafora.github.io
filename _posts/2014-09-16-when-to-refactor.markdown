---
layout: post
title:  "When to refactor"
date:   2014-09-16
---

Consider refactoring when:

  1. You’ve written a new test and gotten it to pass. These sorts of refactorings are the third phase of the red-green-refactor cycle, and are usually minor because of the incremental nature of the cycle. If you’re doing things right, each test you write should result in just a small addition to the functionality of your code. Sometimes a small addition represents a tipping point and will lead to a large restructuring, but this is less common.
      * If you don’t consider refactoring at the end of every iteration of the red-green-refactor cycle, bad code may become entrenched in the system. Getting the system back to a clean state will take longer than the cumulative time of regular, red-green-refactor refactorings.
  2. You’re about to implement new functionality and you are certain that restructuring the system will (a) increase the system’s maintainability, and (b) make it easier to add the functionality.
      * Quick aside to define maintainability: Flexibility and expressiveness. How easy it is to change a system to accommodate additional functionality (helped by, e.g., SOLID code and high test coverage), and how easy it is to decide what needs to be changed and how to change it (helped by the system being easily comprehensible at every level, from the guts of individual functions to the web of dependencies among packages).
      * If the refactoring doesn’t make it easier to add the functionality, it should probably be done after adding the functionality. The maintainability of the system is a critical consideration, but the customer probably cares more about the immediate concern of implementing the new functionality, so in this case the new functionality should come first.

Consider delaying refactoring when:

  1. It’s better done after implementing a new feature (as described above).
  2. You think you’ll need a design pattern or abstraction, but aren’t certain. If you don’t end up needing it, the refactoring will either be a waste (if you end up getting rid of it) or needless complexity (if you don’t).
  3. When you *know* you’re approaching a design pattern or abstraction and you’re close enough to achieving it that any intermediate refactoring would be a waste.