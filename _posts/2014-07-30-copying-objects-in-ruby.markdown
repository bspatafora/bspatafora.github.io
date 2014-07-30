---
layout: post
title:  "Copying objects in Ruby"
date:   2014-07-30
---

Say, hypothetically, we’re building a Tic-tac-toe app with an unbeatable AI in 
Ruby. The way we’re making this AI unbeatable is by implementing the 
[Minimax][] algorithm. The condensed version of this algorithm (as it applies 
to an object-oriented implementation of Tic-tac-toe) is that, given a `Board` 
object representing the state of play, it generates (and scores) new, 
hypothetical `Board` objects for each remaining possible state of play. Among 
other things, this means we’ll need to create copies of the “real” `Board` 
object.

Here’s the `Board` class we’re working with:

{% highlight ruby linenos %}

    class Board
      attr_reader :spaces

      def initialize
        @spaces = [nil, nil, nil, nil, nil, nil, nil, nil, nil]
      end
    end
{% endhighlight %}

To create a copy of an instance of this class, we might naively try to simply 
create a new variable and set it to the real board:

{% highlight ruby linenos %}

    real_board = Board.new
    copied_board = real_board
{% endhighlight %}

But `copied_board` will simply point to the original object, as evidenced by 
the matching object IDs:

{% highlight ruby linenos %}

    > real_board
    => #<Board:0x000001012e6628 @spaces=[nil, nil, nil, nil, nil, nil, nil, nil, nil]>
    > copied_board
    => #<Board:0x000001012e6628 @spaces=[nil, nil, nil, nil, nil, nil, nil, nil, nil]>
{% endhighlight %}

This means that any changes we make to `copied_board` will also change 
`real_board`, which of course is not the behavior we’re looking for.

After a quick search and maybe a glance at the docs, perhaps we decide that 
the [`clone`][] method is just what we’re looking for. Certainly sounds right, 
doesn’t it? (Ruby also provides objects with a `dup` method, but for our 
purposes `dup` and `clone` behave the same.) 

{% highlight ruby linenos %}

    real_board = Board.new
    copied_board = real_board.clone
{% endhighlight %}

We’ve got a good feeling about this. We head back to the REPL to check it out.

{% highlight ruby linenos %}

    > real_board
    => #<Board:0x000001012e6628 @spaces=[nil, nil, nil, nil, nil, nil, nil, nil, nil]>
    > cloned_board = real_board.clone
    => #<Board:0x00000101270b30 @spaces=[nil, nil, nil, nil, nil, nil, nil, nil, nil]>
{% endhighlight %}

Different object IDs! We did it! Elated, we continue work on our AI using 
`clone` whenever we need to generate a new hypothetical board to work with.

But our joy soon turns to ashes in our collective mouths. The algorithm isn’t 
working. Somehow, changes we make to our cloned `Board`s are affecting the 
original as well.

We know we’re working with different `Board` objects, so how could this be? 
Hungry for answers, we fire up the REPL once again, fortuitously deciding to 
try the following:

{% highlight ruby linenos %}

    > real_board.spaces.object_id
    => 2157392620
    > cloned_board.spaces.object_id
    => 2157392620
{% endhighlight %}

Oh. Em. Geez. It would appear that even though `cloned_board` refers to a 
different object than `real_board`, it still refers to the same object (in 
this case, an `Array`) held by `real_board`’s `@spaces` instance variable!

This sort of behavior is sometimes referred to as “shallow copy.” This means 
that while the original object itself is actually copied, any objects it 
references (e.g., `real_board`’s `@spaces` array) are simply rereferenced by 
the clones.

What we need is “deep copy”: the recreation at a separate location in memory 
of not only the original object, but also any and all objects “contained 
within” that object.

If you’re doing so much copying that every clock cycle matters, you probably 
want to override your class’s `clone` method, but if you’re working at this 
scale you’re also probably not reading this post, so I’m going to suggest a 
simpler, if somewhat less performant, approach: Using Ruby’s marshaling 
library, [`Marshal`][].

{% highlight ruby linenos %}

    real_board = Board.new
    marshaled_board = Marshal.load(Marshal.dump(real_board))
{% endhighlight %}

`Marshal.dump` serializes our entire complex `Board` object into a string. In 
so doing, it erases any trace of the original object IDs. `Marshal.load`, in 
turn, reverses the process, constructing a new object from the data dumped 
from the original.

{% highlight ruby linenos %}

    > real_board
    => #<Board:0x000001012e6628 @spaces=[nil, nil, nil, nil, nil, nil, nil, nil, nil]>
    > marshaled_board
    => #<Board:0x0000010112b8b0 @spaces=[nil, nil, nil, nil, nil, nil, nil, nil, nil]>
    > real_board.spaces.object_id
    => 2157392620
    > marshaled_board.spaces.object_id
    => 2156485680
{% endhighlight %}

So, if you ever find yourself in a situation where you need to deep copy some 
objects in Ruby (and non-optimal performance isn’t an issue), `Marshal.dump` 
and `Marshal.load` are your guys.

[Minimax]: http://en.wikipedia.org/wiki/Minimax
[`clone`]: http://ruby-doc.org/core-2.1.2/Object.html#method-i-clone
[`Marshal`]: http://www.ruby-doc.org/core-2.1.2/Marshal.html
