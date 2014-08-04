---
layout: post
title:  "Command objects are gerunds"
date:   2014-07-09
---

A gerund—as I often have to remind myself—is a verbal noun. That is, a gerund is a verb being used as a noun. In English, they always end in -ing, though not every word that ends in -ing is a gerund. An example: The verb “to overeat” becomes the gerund “overeating,” as in the sentence, “I enjoy overeating.”

The Command Pattern is a [behavioral design pattern][]. This is its essence:

{% highlight ruby linenos %}
class SomeCommand
  def execute
    # ...
  end
end
{% endhighlight %}

The idea is that you can make lots of command objects from this class (and other classes that adhere to this “has an execute method” interface), then pass them around your system to entities that know about the interface (i.e., know they can call `execute` on whatever object they are passed).

Command objects are like gerunds in that they take the coding equivalent of a verb (methods) and transform them into the coding equivalent of a noun (objects).

This transformation allows for some interesting possibilities. For example, you could dynamically build up a list of commands to be executed together at some later time, a structure that might form the basis for the creation and execution of user-defined macros. Another use with a similar structure is building up and executing a database transaction, where failure on the part of any individual command would prevent the transaction as a whole from being committed.

The Command Pattern can also be purposed to implement undo functionality. Each command object must remember the details of what it did when its execute method was called, and have an undo method to able to reverse whatever it did.

The [Wikipedia article on the Command Pattern][] contains a simple example of the pattern showing its typical structure. Here’s my version of the example, rewritten in Ruby and a little modified:

{% highlight ruby linenos %}
# Invoker
class Invoker
  def initialize
    @history = []
  end

  def record_and_execute(command)
    @history << command  # Optional
    command.execute
  end
end

# Receiver
class Light
  def turn_on
    puts 'The light is on'
  end

  def turn_off
    puts 'The light is off'
  end
end

# A command
class TurnOnCommand
  def initialize(light)
    @light = light
  end

  def execute
    @light.turn_on
  end
end

# Another command
class TurnOffCommand
  def initialize(light)
    @light = light
  end

  def execute
    @light.turn_off
  end
end

# Client
class Client
  def initialize
    @invoker = Invoker.new
    @light = Light.new
  end

  def party
    50.times do
      @invoker.record_and_execute(TurnOnCommand.new(@light))
      @invoker.record_and_execute(TurnOffCommand.new(@light))
    end
  end
end
{% endhighlight %}

The example contains four sorts of entities, each with their own responsibility. The **receiver** does the actual work (in this case, it is a Light object that can be turned on and off). The **command objects** wrap method calls to the receiver. While client entities can already *send* a `turn_on` or `turn_off` message to a Lamp object, these commands are objects representing *the sending* of such a message. This may seem like a subtle distinction, but it's the key to how the pattern works.

The **client** makes decisions about which commands should be created and executed and when these things should occur, and the **invoker** is the entity that actually calls `execute` on the command objects. The roles of client and invoker are sometimes represented in the same object, but since they [change for different reasons][] each responsibility should exist in its own entity.

The common thread running through this example and the use cases for the pattern described above is the decoupling of the actual execution of a command from the entity that decided it should be executed. [As Uncle Bob puts it][], the Command Pattern decouples “what gets done from who does it” and “what gets done from when it gets done.” The key to this decoupling is the reification of something abstract and transitory (a method) into a stable entity (the object).

[behavioral design pattern]: http://en.wikipedia.org/wiki/Behavioral_pattern
[As Uncle Bob puts it]: http://cleancoders.com/episode/clean-code-episode-25/show
[Wikipedia article on the Command Pattern]: http://en.wikipedia.org/wiki/Command_pattern
[change for different reasons]: http://en.wikipedia.org/wiki/Single_responsibility_principle
