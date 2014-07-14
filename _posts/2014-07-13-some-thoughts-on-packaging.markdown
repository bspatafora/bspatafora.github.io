---
layout: post
title:  "Some thoughts on packaging"
date:   2014-07-13
---

Some thoughts on packaging, based on Uncle Bob’s *[Agile Software Development: 
Principles, Patterns, and Practices][]*.

### How to group classes into packages (Uncle Bob calls this “cohesion”)
* Consider how a package will be reused by others—it should not be so 
  small that you can’t be bothered to maintain it (e.g., notify users of 
  changes, support older versions for at least a little while), nor so 
  heterogeneous that many users will only want some of its classes.
* If something depends on one of a package’s classes, it should depend on 
  all of that package’s classes. If classes aren’t tightly bound to each 
  other, they shouldn’t be in the same package.
* A package should only have one reason to change. This is the SRP applied at 
  a higher level.

### How to handle coupling among packages (“stability”)
* Large teams can run into the issue of changes to some parts of the 
  system by some individuals breaking changes to other parts made by other 
  individuals. This can be addressed by partitioning code into packages 
  that can be worked on independently of one another.
* But partitioning development code into packages only addresses this 
  problem so long as the dependency structure among the packages is 
  managed. In particular, there should be no cycles in which two packages 
  each depend on the other, because these dependency cycles can make it 
  very difficult to keep packages isolated (largely defeating the purpose 
  of partitioning into packages in the first place).

### Instable packages should depend on stable packages
* The most “instable” (able to be changed and likely to have to change) 
  packages are depended on by no other packages, so there aren’t reasons 
  for them not to change, and depend on many packages, meaning they have many 
  possible reasons to change (as changes to any of their dependencies may 
  require that they themselves change).
* The most “stable” (unable to be changed and unlikely to have to change) 
  packages are depended on by many packages, so they shouldn’t change lest 
  many other things need to change, and depend on no other packages, 
  meaning there aren’t external reasons for them to change.
* Uncle Bob’s package instability equation: I = Ce / Ca + Ce
    * Ce is the number of classes inside the package that depend on 
      classes outside the package.
    * Ca is the number of classes outside the package that depend on 
      classes inside the package.
    * I = 0 is maximally stable; I = 1 is maximally instable.
* Instable packages should depend on stable packages; the high-level 
  design of a system should be contained in stable packages, and other 
  things should be more flexible and depend on the stability of the 
  high-level design.
* Packages should be as abstract as they are stable.
    * If a package is stable, it is depended on by many packages, meaning 
      changes to its code could necessitate many changes to code in other 
      packages; this can be addressed with the use of abstraction.

[Agile Software Development: Principles, Patterns, and Practices]: http://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445
