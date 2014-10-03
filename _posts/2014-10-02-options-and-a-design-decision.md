---
layout: post
title:  "OPTIONS and a design decision"
date:   2014-10-02
---

In HTTP, sending an [OPTIONS][] request to some route should get you a response with an `Allow` header. This values of this header specify which HTTP methods (GET, POST, PUT, etc.) that route will respond to (in some useful, non-404/405 way). I needed to implement this functionality for one of the routes served by my Java server.

To get the corresponding OPTIONS acceptance test to pass, all you really need to do is craft a nice, bespoke `Allow` header for the one route it scrutinizes, then solder it firmly into place in the route’s handler. But my mentors made sure to specify that while not all of my routes need to respond to OPTIONS requests, there should be some way to determine the HTTP methods a route’s handler handles from within the application itself. (I’m going to start referring to these methods as HTTP verbs to avoid confusion with the non-HTTP sort.)

Still, the idea of writing a simple, internally-hardcoded `allowedHTTPVerbs` method for each route handler did cross my mind. This would certainly be the next-simplest way to do things. The problem with this approach is that it isn’t DRY. Not only would each `Handler` have functionality relating to each HTTP verb it supported, it would also have the name of each allowed verb manually entered into its `allowedHTTPVerbs` method. This fragmentation could easily lead to the two getting out of sync with one another, meaning that an OPTIONS call to a certain route would might incorrectly specify which verbs it allows.

I instead invested a fair bit of time (given my still-unfamiliarity with Java) looking into some way to keep the list of allowed verbs (on the one hand) and those actually implemented (on the other) automatically in sync with one another.

The solution I ended up at uses [reflection][] to examine the methods defined on a `Handler`, then compares those against a list of possible HTTP verbs. This strategy requires that a `Handler` define a separate method for each HTTP verb it allows (e.g., a handler for a route that permits GET requests must have a corresponding `get()` method), which didn’t seem too onerous a stipulation, given that it also happens to make it clear what a `Handler` does at a glance, and aligns with the clean-code best practice of decomposing your methods.

Here’s what it looks like:

{% highlight java linenos %}
public class HTTP {
    public static String allowedMethods(Class klass) {
        StringJoiner allowedMethods = new StringJoiner(", ");
        for (java.lang.reflect.Method method : klass.getDeclaredMethods()) {
            String methodName = method.getName().toUpperCase();
            if (Method.METHODS.contains(methodName)) {
                allowedMethods.add(methodName);
            }
        }
        return allowedMethods.toString();
    }
}
{% endhighlight %}

To get the list of methods defined on a `Handler`, I use `Class`’s `getDeclaredMethods` method. One drawback to this method is that it doesn’t grab inherited methods, only those defined within the passed class itself. Some of my `Handler`s leave the majority of their work—including defining the HTTP verb methods—to their superclass, so this strategy does require redefining those methods in the barebones `Handler`s themselves. Duplication is avoided by a simple call to the superclass’s copy:

{% highlight java %}
public class File1 extends FileHandler implements Handler {
    // ...
    protected void get() { super.get(); }
}
{% endhighlight %}

My first implementation actually used `Class`’s `getMethods` method, which gets inherited methods as well as those defined my the class itself. The reason I made the switch is that `getMethods` doesn’t get private methods.

The two methods presented me with a tradeoff. I ultimately went with `getDeclaredMethods` because I was more comfortable with the “useless” redefinitions (though they do act as documentation) in the barebones `Handler`s than in breaking encapsulation by making all of a `Handler`’s HTTP verb methods public when there were no actual clients for these methods.

Even more interesting than the choice I went with, however, was being presented with the choice in the first place. Making the decision took time, and I don’t know if it was the right or best one, but it was a useful exercise to have to make it.

[OPTIONS]: http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html
[reflection]: http://en.wikipedia.org/wiki/Reflection_(computer_programming)