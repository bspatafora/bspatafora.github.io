---
layout: post
title:  "The Open–Closed Principle"
date:   2014-07-05
---

> “Entities should be open for extension, but closed for modification.”

How do you add functionality to an entity without changing its code? To answer this, we need to identify the sort of entity we’re talking about closing to modification. Considering an example will help, and [PPP][] provides a good one.

Imagine we are tasked with the critical mission of designing a system whose job it is to draw shapes. For now, let’s say the only shapes we’re interested in drawing are circles and squares.

Since we’re using an object-oriented language (we’re using an object-oriented language), we’ll probably want to create a `Circle` class and a `Square` class with which to represent our circles and our squares. Here they are in all their current glory:

{% highlight ruby %}
class Circle
  attr_accessor :radius
end
{% endhighlight %}

{% highlight ruby %}
class Square
  attr_accessor :side
end
{% endhighlight %}

Great. Now, all we need is to tell them how to draw themselves:

{% highlight ruby %}
class Circle
  attr_accessor :radius
  
  def draw_circle
    # ...
  end
end
{% endhighlight %}

{% highlight ruby %}
class Square
  attr_accessor :side

  def draw_square
    # ...
  end 
end
{% endhighlight %}

Assuming the left-out implementation details are correct, these classes will do what we need them to, and we could print a list of shapes like so:

{% highlight ruby %}
draw_all(shapes)
  shapes.each do |shape|
    case shape
    when Circle
      shape.draw_circle
    when Square
      shape.draw_square
    end
  end
end
{% endhighlight %}

But while this will work, consider what happens when we want to add a feature to our system in the form of a `Triangle` class. Not only do we need to write the code for the `Triangle` class, but we also have to go into the above method and add another when clause to specify that triangles should be draws with their `draw_triangle` method.

We can rework our system to conform to the open–closed principle by having shapes adhere to a common interface, in the form of a `draw` method:

{% highlight ruby %}
class Circle
  attr_accessor :radius

  def draw
    # ...
  end
end
{% endhighlight %}

{% highlight ruby %}
class Square
  attr_accessor :side

  def draw
    # ...
  end
end
{% endhighlight %}

{% highlight ruby %}
draw_all(shapes)
  shapes.each do |shape|
    shape.draw
  end
end
{% endhighlight %}

Now, we can add our `Triangle` class, and as long as it abides by the interface we have created (i.e., it has a `draw` method), no changes will be required within our `draw_all` method in order for it to be able to draw triangles. In this example, `draw_all` is the client of our shapes, which in this context might be termed service entities. In both of our implementations, we need to add code in the form of a new service entity in order to add functionality. The difference the OCP makes is that it obviates the need to change code within the client interface in order to accommodate the new functionality.

While such a change may not seem necessary in a system as simple as this one, adhering to the OCP can lend a more complex system much needed flexibility.  Specifically, it reduces the fragility and rigidity of the system by enabling you to add new functionality without changing existing code. In addition, the OCP often helps keep your code DRY, as the sort of conditional logic that appears in the original `draw_all` method (which might otherwise end up getting copied in various places throughout a system) is no longer necessary.

[PPP]: http://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445
