---
layout: post
title:  "Test behavior, not implementation"
date:   2014-05-08
---

I’m putting together a Tic-tac-toe game in Ruby (and test driving it with 
RSpec). The first iteration will allow the user to play against an AI, and 
will run via the command-line.

One thing the game needs to do is ask the user for a move. Or, more 
completely: it needs to ask the user for a move, then either place that move 
on the board (if it’s a valid one) or ask the user for a valid move if it’s 
not (and keep asking until the user decides to cooperate). The implementation 
details of these steps are contained in various parts of the game, but the 
general flow is contained in this function:

{% gist bspatafora/afd2105b5c1c731343b9 %}

It’s a pretty straightforward block of code, and most of the logic it 
represents was equally straightforward to test. But when it came time to write 
tests for cases where the method is given invalid input, I got stuck.

The way the method learns it has received invalid input (which it obtains via 
the `solicit_input` method) is by receiving an `InvalidMove` error message 
from its game object. In these cases, the code in the rescue clause is 
executed, which includes, on line 7, a [call to the method itself][]. My first 
instinct for testing that this occurs was to test it directly, which looked 
something like this:

{% gist bspatafora/057ca0ef03e548fb1753/b2431c582eb00165e0ca2ce669700e5e62b84a43 %}

My misguided intention was to [stub out][] `solicit_input` with an invalid 
move, call `send_move`, then check to see that `send_move` got called again 
once control flowed to the rescue clause.

The test differs from what I intended it to be in two ways. First, line 5 
doesn’t just check to see if `send_move` is called, as I first assumed. The 
[`receive`][] syntax actually results in RSpec stubbing out the method with a 
“test-double-like” method, meaning that the recursive call does not exist as 
far as the test is concerned.

The second divergence becomes more apparent if we eliminate the first, which 
we can do by modifying line 5 to read:

    expect(io).to receive(:send_move).and_call_original

The addition of [`and_call_original`][] calls, as the name suggests, the 
original method after the “test-double-like” version is received. Setting 
aside for the moment the fact that this causes the method to recurse 
uncontrollably, this modified test still doesn’t fulfill my original, 
misguided intention, because the expectation that `send_move` be called 
(which I intended to test that `send_move` itself calls `send_move`) is 
actually satisfied by the initial call to `send_move`, made explicitly in the 
line above the expectation.

Even so, the test will not pass. Instead, it results in the following error:

    Failure/Error: Unable to find matching line from backtrace
         SystemStackError:
           stack level too deep

As mentioned above, this is because the modified test causes the method to 
enter an infinite loop, continually calling itself (until the Ruby interpreter 
cuts it off after a few thousand iterations) with the same bad input. I 
floundered and searched for a while for some way to avoid this problem while 
still testing the recursive call directly, then asked some more experienced 
people what they would do in this situation. The solution, as the title and 
these people suggested, was to test the method's result, rather than its 
(current) implementation.

I ended up with something like this:

{% gist bspatafora/057ca0ef03e548fb1753 %}

In contrast with my previous attempts, this test sets an expectation about the 
object that `send_move` sends moves to, rather than anything in its own class. 
The use of [`and_return`][] with an invalid argument followed by a valid 
argument ensures that the method recurses only once, and the `expect` 
statement checks to see that it recurses *at least* once, because otherwise 
the board’s 0 index would not have a value.

I had initially wanted to avoid this kind of test, because it seems to me that 
it tests both `send_move` *and* my game class’s `place` method (which places a 
move on the board structure if it is valid). But it seems that, in order to 
test behavior and not implementation, it's sometimes reasonable to cross class 
boundaries.

[call to the method itself]: http://en.wikipedia.org/wiki/Recursion
[stub out]: https://github.com/rspec/rspec-mocks#method-stubs
[`receive`]: https://github.com/rspec/rspec-mocks#test-specific-extension
[`and_call_original`]: https://github.com/rspec/rspec-mocks#delegating-to-the-original-implementation
[`and_return`]: https://github.com/rspec/rspec-mocks#consecutive-return-values
