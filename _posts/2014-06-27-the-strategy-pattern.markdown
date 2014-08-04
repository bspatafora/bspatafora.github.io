---
layout: post
title:  "The strategy pattern"
date:   2014-06-27
---

## The conditions, in general, under which you may want to use the strategy pattern

  1. There’s some algorithm you need to run
  2. Some parts of the algorithm should always run in the exact same way
  3. But part of it needs to vary depending on the context in which the algorithm is run

## The general structure of the strategy pattern

  * There’s a “context” class
    * It contains the parts of the algorithm that will always run in the exact same way
    * Context objects are instantiated with a strategy object, to which the context object will delegate the running of the part of the algorithm that varies
    
  * There are multiple “strategy” entities
    * Each one contains its own way of running the part of the algorithm that varies, and will be called on by the context object to run its “strategy” at some point during the running of the part of the algorithm contained in the context object

## The specific conditions under which I realized I wanted to use the strategy pattern in my Ruby Tic-tac-toe

  1. Players need to decide on their moves, and place them onto the board
  2. All players place their moves on the board and can only place valid moves on the board
  3. But different types of players decide on their moves in different ways (e.g., a HardAI uses a minimax algorithm, whereas a Human uses a prefrontal cortex)

## The specific structure of my use of the strategy pattern

  * My `Player` class is the context class
    * It contains the parts of the algorithm that place the move on the board if it is valid
    * It gets instantiated with a `move_strategy` to which it delegates the decision of which move to make
  * My `HardAI`, `MediumAI`, and `EasyAI` modules, and my `Human` class are the strategy entities
    * Each one contains a move method that returns a move based on its own move-deciding strategy

## My code

{% highlight ruby linenos %}
module TicTacToes
  class Player
 
    # Attribute readers and initialize method here
 
    def place_and_return_move(board, players)
      loop do
        space = @move_strategy.move(board, players)
        break [@token, space] if board.place(self, space)
        @io.invalid_move_error
      end
    end
  end
end
{% endhighlight %}

{% highlight ruby linenos %}
module TicTacToes
  module MoveStrategies
    module EasyAI  # One of the four strategy entities
      def self.move(board, players)
        board.open_spaces.sample
      end
    end
  end
end
{% endhighlight %}
