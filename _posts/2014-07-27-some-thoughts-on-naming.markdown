---
layout: post
title:  "Some thoughts on naming"
date:   2014-07-27
---

Based on the chapter on naming in *[Clean Code][]*.

  * The big-picture takeaway: “Choosing good names takes time but saves more time than it takes.” I was very happy to hear this, because I sometimes get pretty focused on coming up with just the right name. Of course there’s a balance here—you obviously can’t spend half a day waiting for your muse to wake up—but spending a little extra time may save a whole bunch down the line.
  * Use intention-revealing names. Code is much easier to parse at a glance if the names tell a story about what is happening in a given block (without the reader having prior knowledge of the code).
  * Don’t use names that have special meaning in programming. Names should be based on the abstraction, not specific implementation details.
  * Don’t use “noise words” that don’t help in differentiation, like “info” or “variable.” Words like this serve no function other than to add clutter, resulting in code that takes longer to parse.
  * Use pronounceable names. We humans are good at language, so take advantage of this fact. Names that can be pronounced will be easier to remember than some string of characters with no immediately apparent meaning.
  * Only use single-characters for local variables, if at all. Not only are they essentially meaningless, but they are also, for obvious reasons, hard to search for.
  * Classes should be nouns, methods should be verbs.
      * The book suggests using method names like `set_<variable>` and `get_<variable>`, though it seems like this sort of convention is language-specific, as names like this seem to be discouraged in Ruby.
  * Use one word per concept (e.g., DeviceManager and ProtocolController should both be either managers or controllers). However, it may be even more important to ensure you don’t use the same word for different concepts.
  * Give names context by enclosing them in (well-named) classes, functions, or namespaces. You can also prefix them if it makes more sense. For example, `state` out of context is ambiguous; `address_state` is not.

[Clean Code]: http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882
