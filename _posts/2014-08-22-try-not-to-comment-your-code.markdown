---
layout: post
title:  "Try not to comment your code"
date:   2014-08-22
---

*A comment I posted in response to a question someone asked a about the best practices for commenting code:*

I'd say write as few comments as you can. Expressive code is preferable to comments whenever possible.

Instead of doing this:

    i = 0  # Number of apples

Do this:

    apple_count = 0

This example is totally contrived and obvious, but there are lots of opportunities to write "self-commenting" code if you write/refactor with an eye for readability, and a surprising number of them are similarly simple.

It's often possible to refactor complex code to the point where adding explanatory comments becomes unnecessary. One technique for doing this that I find myself using all the time is variable extraction. This means replacing chunks of complex code with variables named to describe what those chunks are doing.

Here's some unrefactored Ruby code I wrote for a Tic-tac-toe game a few months ago:

    def valid?(space)
      spaces(space).nil? && 0..(@size - 1).include?(space)
    end

While this certainly isn't indecipherable, there's enough happening on this line that it requires the reader to stop and think about how the code maps to the Tic-tac-toe rules it embodies. So I refactored it into the following:

    def valid?(space)
      space_empty = spaces(space).nil?
      board_range = 0..(@size - 1)
      on_the_board = board_range.include?(space)

      space_empty && on_the_board
    end

While the changes resulted in more code, the last line reveals the method's purpose at a glance. (The method's length could be re-reduced by extracting `space_empty` and `on_the_board` methods, but in my opinion that might be taking things too far.) 

This example is interesting because I can imagine someone arguing that it actually makes the case for comments. Here, adding a comment would likely have only added one line to the method's length rather than the three or four I added with my refactoring.

I'd still say go with the refactor rather than the comment. One of the best arguments I've heard for writing more expressive code rather than adding explanatory comments is this: Code doesn't lie, but comments can.

That comment you're reading might misrepresent the details of how the code it describes works in its attempt to summarize it, or it could refer to code that has since changed. With expressive code, there's no need to maintain two separate records of what the code does—the code itself is the documentation.
