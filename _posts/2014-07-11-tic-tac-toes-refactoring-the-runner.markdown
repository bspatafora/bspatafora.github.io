---
layout: post
title:  "Tic-tac-toes: Refactoring the runner"
date:   2014-07-11
---

My Ruby implementation of Tic-tac-toe, Tic-tac-toes, currently runs on the 
command-line. Here’s the class that’s responsible for the policy-level wiring 
together of the system into a playable game:

{% highlight ruby linenos %}
module CommandLine
  class Runner
    def initialize(io, menu, rules)
      @io = io
      @menu = menu
      @rules = rules
    end

    def run
      board, players = @menu.get_board, @menu.get_players

      take_turn(board, players) until @rules.game_over?(board, players)
      end_game(board, players)
    end

    def take_turn(board, players)
      @io.draw_board(board)
      @io.thinking_notification if players.first.needs_to_think

      players.first.move(board, players)
      players.rotate!
    end

    def end_game(board, players)
      @io.draw_board(board)

      winner = @rules.determine_winner(board, players)
      @io.game_over_notification(winner)
    end
  end
end
{% endhighlight %}

The problem is that this class has more than one responsibility. In addition 
to the responsibility described above, it also keeps track of the state of the 
game. Specifically, it’s the class that knows which `board` object is the one 
being used in a given game, which `player` objects are playing, and which 
`player` is next up.

There are two main reasons to extract one of these responsibilities. First, 
the class as it stands now has more than one reason to change. A change to 
the way the command-line version of the game runs has a higher chance of 
affecting the game-state-management aspect of the system as long as both 
responsibilities live in the same class.

Second, the coupling of a core-logic responsibility (managing the game state) 
to a detail (how the game is played when IO is mediated through the command-
line) means that the system is less portable than it could be. If I move the 
game to a different IO context (e.g., by building a web application out of 
it), I won’t be able to reuse this class, even though some of its code (i.e., 
the core-logic responsibility of managing game state) should be reusable 
regardless of IO context.

To solve this, I extracted a `GameState` class that lives in my TicTacToes 
module, which is where I keep the game’s core logic:

{% highlight ruby linenos %}
module TicTacToes
  class GameState
    attr_reader :board, :players

    def initialize(board, players)
      @board = board
      @players = players
    end

    def current_player
      @players.first
    end

    def turn_over(move)
      @players.rotate!
    end
  end
end
{% endhighlight %}

The decomposed command-line runner is now free of the core-logic 
responsibility of keeping track of game state:

{% highlight ruby linenos %}
module CommandLine
  class Runner
    def initialize(io, menu, game_state_factory, rules)
      @io = io
      @menu = menu
      @game_state_factory = game_state_factory
      @rules = rules
    end

    def run
      board, players = @menu.get_board, @menu.get_players
      game_state = @game_state_factory.generate_game_state(board, players)

      take_turn(game_state) until @rules.game_over?(game_state)
      end_game(game_state)
    end

    private

    def take_turn(game_state)
      @io.draw_board(game_state.board)
      @io.thinking_notification if game_state.current_player.needs_to_think

      move = game_state.current_player.move(game_state)
      game_state.turn_over(move)
    end

    def end_game(game_state)
      @io.draw_board(game_state.board)

      winner = @rules.determine_winner(game_state)
      game_state.game_over(winner)

      @io.game_over_notification(winner)
    end
  end
end
{% endhighlight %}
