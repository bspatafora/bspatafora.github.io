---
layout: post
title:  "Towards a dumber command-line I/O"
date:   2014-06-10
---

One of the more major revelations of my first weeks at 8th Light was how important it is to separate out the core logic of an application from the parts that allow someone (or something) to interface with that core logic. The consequence of not doing so is that it will likely take significantly more time and effort to rework things so that the core logic can be accessed via some method other than the original.

In the case of my Tic-tac-toe game, I originally wrote things so that a user could interface with the core game logic via the command line. Soon enough, though, I’ll be building a web interface for my game. The ease and speed with which I’m able to do this will largely depend on how well I’ve established the boundaries between the core logic and my command-line I/O interface.

While I have no doubt there are plenty of ways remaining for me to improve the modularity of these components, I recently restructured things in a way I think will facilitate this process. At the very least, the changes made a few of my classes and modules easier to test, which usually indicates a move in the right direction.

Here’s my command-line I/O module [before the changes][].

And here it is post-lobotomy:

{% highlight ruby linenos %}
module CommandLine
  module IO
    def self.solicit_input
      gets.chomp
    end

    def self.display(message)
      puts message
    end

    def self.display_red(message)
      puts red(message)
    end

    def self.red(message)
      colorize(message, 31)
    end

    def self.blue(message)
      colorize(message, 34)
    end

    def self.colorize(message, color_code)
      "\e[#{color_code}m#{message}\e[0m"
    end
  end
end
{% endhighlight %}

The fact that the module is now small enough that I can embed it in this post rather than linking to it is, by itself, a good sign.

Of course, I didn’t just remove the additional behavior. Instead, I extracted it into a class that I believe has more potential for reuse than this command-line I/O module (even if it doesn’t turn out to be usable in the context of a web application).

By paring down the module tasked with interfacing with the command line to just those behaviors strictly necessary for doing so, I better defined the boundaries between the core logic and I/O components of my game. And because it’s impossible to anticipate exactly how a given application might be reused in the future, I would argue that this increase in modularity is a good in and of itself.

[before the changes]: https://github.com/bspatafora/tic_tac_toe/blob/65cc6df8424f76fc804617cd7d4408cf45c782ce/lib/command_line/io.rb
