---
layout: post
title:  "The Single Responsibility Principle"
date:   2014-07-03
---


First, a mostly-there but not-quite-right formulation:

> “A class should have only one responsibility.”

Why so? What’s wrong with a class having two or five or twelve responsibilities? What problems is this principle trying to solve? It seems to me the SRP buys you three things: expressiveness, increased mobility, and reduced fragility/rigidity.

## 1. Expressiveness

A system that conforms to the single responsibility principle will, in general, be more expressive than one that does not. This is evident when you consider whether you’re likely to come up with a name that simultaneously communicates each of your SRP-violating class’s five responsibilities. As you add responsibilities to a class, its name must become vaguer or longer, and either way the system as a whole becomes that much less comprehensible. It’s easier to decipher a system if you can look at each class in isolation and quickly grasp its purpose. If a class is doing many things, it will take longer to understand.

## 2. Increased mobility

The increase in mobility comes from the possibility (or likelihood) that someone will want to reuse only the code representing one of an SRP-violating class’s multiple responsibilities. For example, a class that contains both domain logic and command-line-specific I/O logic couples code that is reusable across I/O contexts (i.e., the domain logic) with code that is not. Separating these responsibilities into two classes would make it easier to reuse the domain logic in another I/O context, such as a web interface.

## 3. Reduced fragility/rigidity

Perhaps the most important benefit that results from adhering to the SRP is the reduction in a system’s fragility, and therefore in its rigidity. A fragile system is one in which changes to one thing tend to break unrelated things. If a new requirement necessitates changing just one responsibility, but the code representing that responsibility lives in a class containing other responsibilities, your changes will be more likely to affect those responsibilities than if they were encapsulated in their own classes, with their information properly hidden from the rest of the system.

## Decomposing an SRP-violator

{% highlight ruby linenos %}
# SRP-violating Board class

class Board
  # ...
 
  def spaces
    @spaces
  end
 
  def place(token, space)
    @spaces[space] = token
  end
 
  def draw
    puts @spaces
  end
end
{% endhighlight %}

{% highlight ruby linenos %}
# New Board class with only one responsibility

class Board
  # ...
 
  def spaces
    @spaces
  end 
 
  def place(token, space)
    @spaces[space] = token
  end 
end
{% endhighlight %}

{% highlight ruby linenos %}
# IO responsibility extracted into IO-specific class

class CommandLine
  # ...
 
  def draw(board)
    puts board.spaces
  end
end
{% endhighlight %}

## Defining responsibility

Our original formulation raises the question of what responsibility means in this context. [PPP][] mentions two possibilities.

The first is responsibility as cohesion. This is a fairly abstract definition that says a class has one responsibility if all of its methods do the same sort of thing. So I/O methods go with I/O methods, database stuff goes with database stuff, domain logic of one kind lives with domain logic of that same kind. It’s separation of responsibilities based on “sameness” mapping.

The alternative is responsibility as reason to change. It’s not a totally different definition from cohesion, because oftentimes things will change together because they do the same sort of thing. But the two sometimes diverge, and focusing on “reason to change” makes it more likely that the way you choose to separate your responsibilities will fulfill one of the central purposes of this (and every other) SOLID principle: to build systems that readily adapt to changing requirements.

[PPP]: http://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445
