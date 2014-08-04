---
layout: post
title:  "Recursion and the stack"
date:   2014-07-31
---

Here’s some recursion:

{% highlight ruby linenos %}
def factorial(number)
  if number.zero?
    return 1
  else
    return number * factorial(number - 1)
  end
end
{% endhighlight %}

I used to find this really mystifying, and I feel like I would have gotten it quicker if I’d had some conception of [the call stack][], so I’m going to try to explain why this works by reference to how the following call gets evaluated:

{% highlight ruby %}
factorial(5)
{% endhighlight %}

A good hint that understanding the stack might help in making sense of recursion comes in the form of the error message you get if you do recursion wrong. Specifically, if you forget to include a base case (a condition that ultimately ends the recursion) or if you put things together in such a way that the base case is never reached, you might see something like this:

    stack level too deep (SystemStackError)

This is the sort of error message that is either totally opaque or totally transparent depending on whether you have a little relevant background knowledge.

### The bit of relevant background knowledge

Sometimes functions call other functions. Our `factorial` function, for instance, makes a call to the multiply method of Ruby’s `Fixnum` (\*). In order for `factorial` to return a result, it needs (among other things) to wait for this second function to return *its* result. In addition, something needs to keep track of the fact that our particular invocation of `factorial` (where we passed 5 as the argument) should continue executing once its invocation of the multiply function has completed.

This “something” is the call stack. The call stack—usually just referred to as “the stack”—is a section of memory used to keep track of what has been called by whom and in what order.

As the name suggests, it works using a stack data structure. Stacks are “last in, first out,” or LIFO, meaning that whatever was “pushed” onto the stack most recently will be the first thing “popped” off of it. An analogy is commonly made to those spring-loaded tray dispensers you find in cafeterias, where the spring depresses and the entire stack of trays moves down as you add trays to the top of it.

When something makes a function call in Ruby, the interpreter adds information about this call (including who made it and any local variables passed as arguments) to the stack in the form of a “stack frame” (each of which would be a cafeteria tray in the tray dispenser analogy).

### Making the call

So, with that background, here’s the high-level overview of what happens when we make our call to `factorial`:

* We make the original call to `factorial` in the context `<main>` (i.e., outside any entities that we have defined), passing in 5 as the argument.
* The interpreter adds a frame to the stack with this information, which I’m going to represent like this: `{ caller: '<main>', arguments: [5] }`. The first invocation of `factorial` will be evaluated in this context.
* The interpreter runs through the body of `factorial` with the local variable `number` set to 5.
* Since `number` isn’t 0, the interpreter heads to line 5 and tries to evaluate the multiplication.
* While it already knows the value of the first argument (`number`) is 5, determining the value of the second argument requires first trying to get a return value for `factorial(number - 1)`.
* So the interpreter makes this call (`factorial(4)`) and adds a frame to the stack with the relevant information. The stack now looks something like this:
    * `{ caller: 'factorial', arguments: [4] }`
    * `{ caller: '<main>', arguments: [5] }`
* The interpreter runs through the body of `factorial` in the context of the current stack frame (i.e., the one most recently added).
* `number` isn’t 0 in this context either, so the interpreter ends up trying to evaluate the line 5 multiplication in this context.
* It can’t do this without getting a value for `factorial(number - 1)`, so this call is made as well, and another frame gets added to the stack:
    * `{ caller: 'factorial', arguments: [3] }`
    * `{ caller: 'factorial', arguments: [4] }`
    * `{ caller: '<main>', arguments: [5] }`
* The same sequence happens with the next two calls, resulting in a stack that looks something like this:
    * `{ caller: 'factorial', arguments: [0] }`
    * `{ caller: 'factorial', arguments: [1] }`
    * `{ caller: 'factorial', arguments: [2] }`
    * `{ caller: 'factorial', arguments: [3] }`
    * `{ caller: 'factorial', arguments: [4] }`
    * `{ caller: '<main>', arguments: [5] }`
* When the interpreter evaluates this last call, it can finally return a value rather than making a call/adding another frame to the stack. `number` is 0, so this invocation of `factorial` returns 1.
* It will then work back through the stack, popping off the last-added frame and evaluating `factorial` with that context and the value returned from its recursive call. This “unwinding” is more easily represented using [Polish notation][]:
    * `(* 1 (* factorial(1) (* factorial(2) (* factorial(3) (* factorial(4) (factorial(5)))))))`
    * `(* 1 (* factorial(2) (* factorial(3) (* factorial(4) (factorial(5))))))`
    * `(* 2 (* factorial(3) (* factorial(4) (factorial(5)))))`
    * `(* 6 (* factorial(4) (factorial(5))))`
    * `(* 24 (factorial(5)))`
    * `(120)`

The key to recursion is that when the interpreter can’t immediately evaluate an expression (as with `return number * factorial(number - 1)` when `number - 1` isn’t 0), it “punts” by adding a new frame to the stack. It does this as many times as it needs, then works its way back through the stack once a call returns a “usable” value (e.g., an integer when the context is multiplication).

It should now be clear what the error message above means. When a recursive function lacks a base case (or that base case is never reached), the interpreter is left to continually add frames to the stack. In theory, it would do this forever, as there isn’t anything in the function telling it when to stop, but in practice, it is stopped and the above error message displayed once it has created some artificially designated maximum number of frames.

[the call stack]: http://en.wikipedia.org/wiki/Call_stack
[Polish notation]: http://en.wikipedia.org/wiki/Polish_notation