---
layout: post
title:  "Ruby Tic-tac-toe: Unifying the UI ports (before)"
date:   2014-09-22
---

When I started building my [Ruby Tic-tac-toe][] implementation back in May, it was going to be text-based, playable only via the command line. That’s actually only partly true. I knew it was pretty likely that my mentors would at some point want me to reuse the reusable parts (e.g., the abstraction that wrapped the board data structure, the AI behavior) with a different UI; namely, a web browser.

Nonetheless, my original implementation was not as easily adapted to other UI contexts as it could be. This was for two reasons. First, though I had gone through a similar process in porting my Mastermind implementation to the browser, I still didn’t have an intuitive sense for exactly what about the system would be different in the new UI context and what could remain the same. This is the individual reason specific to what I knew at the time. The more general reason that my game couldn’t just be dropped into, say, a Rails app without going back in to create a port for it to interface with is YAGNI: You aren’t gonna need it. Though I was pretty sure I’d be adapting the system for the browser, I wasn’t totally sure. If I’d devoted extra time to thinking about how to design the system to be immediately adaptable to both contexts and it turned out that making a browser version was not a requirement, then that extra time would have been a waste.

Fast forward to today. Right now, I have one Ruby Tic-tac-toe that is playable via the command line and one Ruby Tic-tac-toe that is playable via web browser. The good news is that the two applications share most of their functionality. They both use the same `Board` objects, `Rules` methods, and AI implementations (among other things). The reality is that they can’t share all their behavior. For example, the command line version `put`s a text representation of the game board to the terminal it’s being run in, whereas the web version’s board is an HTML table. Because some of the details differ, some parts of the implementations will necessarily differ as well. The bad news, however, is that there are still parts of the system that unnecessarily differ. Consider `Runner`, a class used only in the command-line implementation of the game, and `Adapter`, whose only client is the Rails app:

### CommandLine::Runner

{% highlight ruby linenos %}
require 'tic_tac_toes/core/game_state'

module TicTacToes
  module CommandLine
    class Runner
      def initialize(io, menu, history)
        @io = io
        @menu = menu
        @history = history
      end

      def run
        board, players = @menu.get_board, @menu.get_players
        game_state = TicTacToes::Core::GameState.new(board, players, @history)
        game_state.start_game

        take_turn(game_state) until game_state.game_over?
        end_game(game_state)
      end

      private

      def take_turn(game_state)
        @io.draw_board(game_state.board)
        @io.thinking_notification if game_state.current_player.needs_to_think

        move = game_state.current_player.place_and_return_move(game_state)
        game_state.turn_over(move)
      end

      def end_game(game_state)
        @io.draw_board(game_state.board)

        winner = game_state.determine_winner
        game_state.end_game(winner)

        @io.game_over_notification(winner)
      end
    end
  end
end
{% endhighlight %}

### UI::Adapter

{% highlight ruby linenos %}
module TicTacToes
  module UI
    module Adapter
      def self.start_game(order, game_state, listener)
        user_is_first = order == 'first'

        user_is_first ? listener.moves_were_made(game_state) : make_move(game_state, nil, listener)
      end

      def self.make_move(game_state, move, listener)
        computer_first = move.nil?

        if computer_first
          game_state.turn_over(nil)
          computer_move(game_state, listener)
          return game_over(game_state, listener) if game_state.game_over?
        else
          human_move(game_state, move, listener)
          return game_over(game_state, listener) if game_state.game_over?
          computer_move(game_state, listener)
          return game_over(game_state, listener) if game_state.game_over?
        end
        listener.moves_were_made(game_state)
      end

      private

      def self.computer_move(game_state, listener)
        computer_player = game_state.computer_player
        computer_move = computer_player.place_and_return_move(game_state)
        game_state.turn_over(computer_move)
      end

      def self.human_move(game_state, move, listener)
        human_player = game_state.current_player
        game_state.place_move(move.to_i)
        game_state.turn_over([human_player.token, move.to_i])
      end

      def self.game_over(game_state, listener)
        winning_player = game_state.determine_winner

        record_game_history(game_state, winning_player)
        if winning_player
          listener.game_ended_in_winner(game_state, winning_player.token)
        else
          listener.game_ended_in_draw(game_state)
        end
      end

      def self.record_game_history(game_state, winning_player)
        game_state.start_game
        game_state.end_game(winning_player)
      end
    end
  end
end
{% endhighlight %}

There’s a lot going on in these two entities, but I think it’s clear that they share some responsibilities. Both of them are coordinators, wiring together discrete pieces of functionality from throughout the system to define higher-level behavior (in this case, playing through a single game of Tic-tac-toe). They both describe things like how to step through a turn and what to do when the game has ended.

But the way in which they do these things differs. Again, some of the difference is legitimate. In addition to being defined by their different UI’s, the two implementations have different requirements. In the browser version, for instance, the user can pick whether they make the first or second move. In the command-line version, the user always goes first. The command-line version also has functionality that the browser version does not; namely, it allows the user to select the tokens that will be used to represent them and their opponent on the board.

These differences, however, don’t negate the fact that the entities have, in the abstract, a shared responsibility. To use the terminology of [Ports-and-Adapters Architecture][], each entity represents a UI “port,” allowing some UI adapter (e.g., a command-line or web interface) to work with the system’s core functionality. One change I’d like to make is to rename `UI::Adapter` to `UI::Port` to align better with Ports-and-Adapters nomenclature.

But the big, architecture-level change I’m most interested in is reworking things so that `UI::Port` can serve both UI adapters. I expect this will require changing not only `UI::Port` itself, but also its clients. In particular, it looks like the command-line implementation needs to be structured more like the web adapter (specifically, a Rails controller), which uses the [Observer Pattern][] to interface with the port, passing itself as a listener when it calls into the entity (via `make_move`, for instance) and having one of its methods called back (`moves_were_made`, or `game_ended_in_winner` or `game_ended_in_draw` if the move ended the game).

[Ruby Tic-tac-toe]: https://github.com/bspatafora/tic_tac_toes
[Ports-and-Adapters Architecture]: http://bspatafora.com/blog/ports-and-adapters-architecture-aka-hexagonal-architecture/
[Observer Pattern]: http://en.wikipedia.org/wiki/Observer_pattern