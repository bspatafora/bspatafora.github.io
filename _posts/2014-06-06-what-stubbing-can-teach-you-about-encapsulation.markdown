---
layout: post
title:  "What stubbing can teach you about encapsulation"
date:   2014-06-06
---

First, me stubbing the system under test:

{% highlight ruby linenos %}
class Menu
  def get_board
    TicTacToe::Board.new(row_size: get_row_size)
  end

  def get_row_size
    loop do
      row_size = @io.solicit_row_size
      break row_size if TicTacToe::Rules.row_size_valid?(row_size)
      @io.invalid_row_size_error
    end
  end
end
{% endhighlight %}

{% highlight ruby linenos %}
describe TicTacToe::Menu do
  describe '#get_board' do
    it "returns a board with the correct row size when given valid row size input" do
      row_size = 3
      allow(menu).to receive(:get_row_size) { row_size }  # Stubbing the system under test

      board = menu.get_board
      expect(board.row_size).to equal(row_size)
    end
  end
end
{% endhighlight %}

Bad.

In this case, the system under test is a class. To keep things simple, I’m going to pretend classes are the only systems that are ever under test and refer to any such class as a CUT (for “class under test”).

Where I *should* have stubbed was at the boundary between the CUT and the command-line IO module it asks for user input, represented on line 8 of my code. Instead, I stubbed within the boundaries of the CUT.

My original reasoning went something like this: “I know I need to stub something, because `get_board` calls `get_row_size`, and `get_row_size` calls something that gets user input, and I’d like to keep RSpec from waiting forever for input that never comes. I’m testing `get_board`, and all `get_board` knows is that it calls something called `get_row_size`, so `get_row_size` is what I will stub.” Seemed pretty straightforward.

I came to this conclusion with the best of intentions. I knew encapsulation was a good thing because of the adaptability it can afford a system, and so I was trying to apply it everywhere I could. In the same way a class should be ignorant of the inner workings of any other class, I figured, a method should only know that any method it calls provides the sort of result it desires, not how the outside method obtained that result. And so I wrote a test that reflected this understanding.

The problem is that I was trying to enforce encapsulation in the wrong place.  The level at which encapsulation should occur is not among the methods within a class, but at higher levels, such as among the classes within a system. It’s the boundaries between classes that matter, because classes (and other class-like entities, such as modules) are meant to be the “atom” of an object-oriented system. Of course, this isn’t entirely accurate, as classes are clearly (much) further divisible. But they are often meant to be thought of as indivisible, because ignoring the fact that they have things going on inside of them encourages putting together a system in a more modular way.
