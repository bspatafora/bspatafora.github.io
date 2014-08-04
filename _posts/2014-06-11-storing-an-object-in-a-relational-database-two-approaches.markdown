---
layout: post
title:  "Storing an object in a relational database: Two approaches"
date:   2014-06-11
---

I’m adding a “game history” feature to my Ruby Tic-tac-toe game. The idea is that a user should be able to see a list of all previous games, including the move history of the game, the size of the board played on, and which player won.

## Deciding how to persist

The data need to persist beyond the termination of any one game process. There are a number of ways to achieve this kind of persistence, including writing to a file and storing to a database. I chose to store to a database. More specifically, I decided to use a relational database, in which data are stored in tables, much like the data in a spreadsheet. (Even more specifically, I used the popular relational database PostgreSQL, using the [pg gem][] to interface with it.)

A relational database may not have been the best tool for the job. There is another class of databases out there called object databases, and while I’ve never used an object database, I understand their advantage to be a one-to-one mapping between how an object’s data is stored in an application and how it is stored in the database. I went with a relational database in part because they seem to be the most commonly-used class of database, and partly because I already had Postgres set up on my computer.

## Deciding how to store

### Serializing to a string

Because I was using a relational database, I needed to decide on a way to translate the history data from how it was stored in my Ruby application to how a relational database stores its data. Storing the board size and winner was easy enough—I just created a “games” table with an integer column for each game’s board size and a character column for each game’s winner. Deciding how to store the move history was more complex.

In my Ruby application, I store a move as an array consisting of a token and a space on the board. A move might look like this: `["X", 2]`. A game’s move history, then, consists of an array of these arrays. There is no way (at least that I’m aware of) to store a multidimensional array containing data of multiple types (i.e., integers and characters) in a Postgres field.

My first approach was to serialize each game’s move history array into a string, something that SQL databases have no problem storing.

{% highlight ruby linenos %}
class PGWrapper
  def record_game(history)
    moves_string = parse_for_database(history.moves)

    connection.exec("INSERT INTO games (board_size, moves, winner) VALUES (
      #{history.board_size},
      '#{moves_string}',
      '#{history.winner}')")
  end

  def parse_for_database(moves)
    moves_string = ""

    moves.each do |move|
      moves_string << move.first.to_s
      moves_string << move.last.to_s
    end

    moves_string
  end
end
{% endhighlight %}

### A more relational approach

This worked, and it would have been trivial to write a `parse_from_database` method to rebuild the data into a multidimensional Ruby array. But it felt a little dirty, and when I asked my mentors about it, they both seemed to indicate a strong preference for doing it another way. Namely, by creating a separate “moves” table and associating each move with a particular game.

{% highlight ruby linenos %}
class PGWrapper
  def record_board_size_and_winner(history, connection)
    connection.exec("INSERT INTO games (board_size, winner) VALUES (
      #{history.board_size},
      '#{history.winner}')")
  end

  def record_moves(history, connection)
    game_id = get_game_id(connection)

    history.moves.each_with_index do |move, index|
      move_number = index + 1
      connection.exec("INSERT INTO moves (game, number, token, space) VALUES (
        #{game_id},
        #{move_number},
        '#{move.first}',
        #{move.last})")
    end
  end
end
{% endhighlight %}

I don’t know very much about the intricacies of transferring application data to and from databases, so I can’t speak with much confidence about the advantages and disadvantages these two approaches have relative to one another. But one major benefit to the second approach became clear to me after implementing it.

Whereas the moves history data in the first approach are essentially unusable until brought back into the application and deserialized, the fact that they are contained in separate columns of the “moves” table in the second approach means that they can be independently accessed and manipulated while still in the database. And while I don’t currently have a need to access or manipulate these data outside the context of a given move’s game, it seems like keeping data in as usable a form as possible is generally a good idea.

[pg gem]: http://deveiate.org/code/pg/
