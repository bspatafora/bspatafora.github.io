---
layout: post
title:  "Understanding responsibility"
date:   2014-10-15
---

If forced to pick, most people would call the [Single Responsibility Principle][] the most important of the SOLID principles. It could be that that’s why it’s the first one (though I have a sneaking suspicion it might have something to do with forming an attractive acronym). It’s commonly understood that in order to build flexible systems you need to keep their components modular, and in order to keep components from getting entangled with other parts of the system you need to keep them focused on just one responsibility.

Another reason the SRP is popular is that it’s arguably the easiest SOLID principle to understand. But its simplicity is deceptive, as I’ve learned in my time at 8th Light. When I first started applying the SRP, I focused (and still do, though to a lesser extent) on conceptual affinity—how well the things in a class “fit” together. Like with like.

While I’d defend this as a consideration (if done right, it can make a system easier to understand), I’m now aware that it must firmly take the back seat to a focus on preventing objects from growing their responsibilities through knowledge of (i.e., dependency on) other objects.

To reflect a little on how my understanding has evolved so far, I pulled code samples involved in checking victory conditions from the three Tic-tac-toe games I’ve worked on during my apprenticeship.

The first Tic-tac-toe I attempted was in Ruby. This `Game` class, from such an early commit that it’s only partially written, clearly violates single responsibility.

{% highlight ruby linenos %}
class Game
  def initialize
    @board = Array.new(9)
    @row_size = Math.sqrt(@board.size)
    @tokens = [:X, :O]
  end

  def place(move)
    # ...
  end

  def diagonal_victory?(token)
    back_diagonal, front_diagonal = true, true
    generate_row_slices.each_with_index do |row, index|
      back_diagonal = false if row[index] != token
      front_diagonal = false if row[@row_size - (index + 1)] != token
    end
    back_diagonal || front_diagonal
  end

  def horizontal_victory?(token)
    linear_victory?(generate_row_slices, token)
  end

  def vertical_victory?(token)
    transposed_rows = generate_row_slices.transpose
    linear_victory?(transposed_rows, token)
  end

  def generate_row_slices
    @board.each_slice(@row_size).to_a
  end

  def linear_victory?(rows, token)
    rows.any? { |row| row.all? { |space| space == token  } }
  end
end
{% endhighlight %}

The victory condition rules are embedded in the same class as the details of the board. This is a problem because these things change for different reasons, and at different rates.

On the one hand, the way the game is won is very unlikely to change, given that our system is a Tic-tac-toe application. It’s as close as you get to pure domain logic. The details of the board, on the other hand, may very well change. First, it’s possible (or if you’re an 8th Light apprentice, near certain) that requirements may change to specify that it must be possible to play on different-sized boards.

Second, even if the size of the board doesn’t change, the implementation details of the data structure used to represent it might. A board might be represented as a flat array (as above), a two-dimensional array (as in the next example), or as any number of other data structures. It should be possible to change this underlying data structure without affecting the rest of the system.

This class, at the very least, needs to have a `Board` and a `Rules` class extracted from it. Otherwise, changes made to one of its responsibilities (like the implementation details of the board) will be likely to break functionality in one of its other responsibilities (the victory condition methods).

My Clojure implementation doesn’t contain as blatant an SRP violation as the above (again, early!) commit of my Ruby version. But it nevertheless has an issue.

{% highlight clojure linenos %}
(ns clojure_tic_tac_toe.core.rules
  (:require [clojure_tic_tac_toe.core.board :refer :all]))

(defn winning-token [board]
  (let [winning-row (filterv #(every? #{(first %)} %) (board-full-slices board))]
    (when winning-row
      (get-in winning-row [0 0]))))

(defn is-draw [board]
  (and (not-any? #{" "} (flatten board))
       (not (winning-token board))))

(defn is-game-over [board]
  (if (or (is-draw board) (winning-token board))
    true
    false))
{% endhighlight %}

Things are looking better here in the scope-limited `Rules` namespace. Rather than the victory condition logic being all tangled up in the implementation details of a board, `winning-token` allows the `Board` namespace to mask them. It depends on the return value of `board-full-slices` rather than on the board itself. If the board data structure needed to change, these changes could be accounted for in `board-full-slices`, a function in the `board` namespace.

Unfortunately, the `rules` namespace takes on too much knowledge of `board` in its `is-draw` function. Not only does it depend on the structure of `board` directly on line 10, it also has its own knowledge of how a `board` represents empty spaces (" "). This knowledge exposes `rules` to changes to `board`, making it more dependent on it than it needs to be to do its job.

My just-written Swift `VictoryConditions` class addresses the above issues. It knows only about `Board`’s public interface, using just its `fullLines()` and `isFull()` methods to accomplish what it needs.

{% highlight swift linenos %}
class VictoryConditions {
    func isGameOver(#board: Board) -> Bool {
        if isWinner(board) || isDraw(board) {
            return true
        }
        return false
    }

    func winningToken(#board: Board) -> String? {
        for line in board.fullLines()! {
            if filledWithSameToken(line) {
                return line[0]
            }
        }
        return nil
    }

    private func isWinner(board: Board) -> Bool {
        return winningToken(board: board) != nil
    }

    private func isDraw(board: Board) -> Bool {
        return board.isFull()
    }

    private func filledWithSameToken(line: [String]) -> Bool {
        let firstToken = line[0]
        for space in line {
            if space != firstToken {
                return false
            }
        }
        return true
    }
}
{% endhighlight %}

In contrast to the above attempts, this `VictoryConditions` class does only one thing, and is as dumb as it can be while still being able to do that thing right. In the context of object-oriented programming, stupidity is safety. My victory conditions logic is now protected from the vagaries of `Board` by virtue of its ignorance.

[Single Responsibility Principle]: http://en.wikipedia.org/wiki/Single_responsibility_principle