---
layout: post
title:  "Ruby requires"
date:   2014-07-16
---

I’m posting this on the off chance it can someday save someone time and 
frustration.

This week, I’ve been working on implementing a browser version of my 
[Ruby Tic-tac-toe game][] via the Ruby on Rails web framework. The original 
implementation is played via the command-line. There’s a big difference 
between how things work on the command-line and how they work over the 
internet, the primary reason being that a process running on your computer via 
the command-line has state and the HTTP protocol does not. So to do this, I’ve 
had to rething a lot of how the game works.

But there’s also a whole bunch of core/domain/Tic-tac-toe-specific logic that 
can work exactly the same regardless of the context in which the game is being 
played. For example, my Tic-tac-toe game has a `Board` class whose job it is 
to keep track of the 9 (or 16, or 25, or however many there are in the board 
you chose to play on) spaces, exposing methods like `place`, which allows a 
player to place their token in a particular space (assuming that space is on 
the board and not already occupied by a token). It doesn’t matter whether 
someone is playing via the command-line or over the internet; the board is 
going to keep functioning the same way.

So, because I didn’t want to [repeat myself][], I packaged the command-line 
version up as a gem so I could simply require it in the Rails app that is to 
be its second home. (A better approach would have been to extract the parts of 
the application that can be used by both versions of the game, package those 
as a gem, then require that gem in the packages responsible for making the 
game playable via command-line/HTTP. But my mentors had me do things this way 
because it’s hard to know a priori what parts of the application will actually 
turn out to be reusable.)

Unfortunately, requiring the gem from Rails turned out not to be as simple as 
I’d hoped.

At first, I tried referring to one of my gem’s modules from the Rails app 
without using a `require` statement. This I thought was reasonable because (a) 
my understanding was that Rails automatically made any gems you listed in your 
Gemfile (and `bundle install`ed) available throughout the application (or at 
least in controllers, which is where I was trying to reference the module), 
and (b) I’d done it before.

This didn’t work. Instead, Rails gave me an “Undefined constant” error 
message indicating it had no idea what I meant by `TicTacToes::Web`. I was 
surprised that this didn’t work, and confused by the fact that Rails was 
telling me not that it didn’t know what `TicTacToes` was, but that it didn’t 
know what the `Web` module of `TicTacToes` was.

In hindsight, I feel like this should have been enough of a clue as to what 
was going on, but at the time I had no idea. I generated and tested out a lot 
of guesses, and tried every search I could think of to turn up information 
from others who’d experienced the same thing, but none of my guesses was right 
and it seemed like no one else had had the problem (an illusion that can arise 
when you’re not only not able to solve something, but also not totally sure 
exactly what it is you’re trying to solve).

The solution came by way of mentor. Turns out that while Rails was indeed 
making my gem automatically available to the controller, the gem itself was 
not automatically making any of its modules available. So though it was not 
necessary to `require 'TicTacToes'`, I did need to `require 'ui/adapter'` 
(`ui` being a subdirectory of the TicTacToes `lib` directory, and `adapter` 
being a file within that directory).

The reason for this has to do with how RubyGems works. From [the RubyGems 
website][]: “When packaging your gem files, you need to be careful of what is 
in your `lib` directory. Every gem you have installed gets its `lib` directory 
appended onto your `$LOAD_PATH`. This means any file on the top level of the 
`lib` directory could get required.”

To ensure all of their files are available to a given system’s Ruby path 
(including those living in subdirectories `lib`), most gems include a single 
file on the top level of the `lib` directory. This file is conventionally 
given the same name as the gem itself, and it `require`s all of the gem’s 
other files. Because I didn’t have such a file (and have been encouraged not 
to use them), I had to manually require any file I wanted to use.

[Ruby Tic-tac-toe game]: https://github.com/bspatafora/tic_tac_toes
[repeat myself]: http://en.wikipedia.org/wiki/Don't_repeat_yourself
[the RubyGems website]: http://guides.rubygems.org/patterns/
