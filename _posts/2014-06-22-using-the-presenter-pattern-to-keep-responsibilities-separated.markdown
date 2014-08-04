---
layout: post
title:  "Using the presenter pattern to keep responsibilities separated"
date:   2014-06-22
---

A common web application trope is getting some data and displaying it on a web page. One source of data might result from processed user input, as when my prime factors Rails app takes a user-inputted number and displays its prime factors beneath the input form.

There are many ways one might wire a web app to do this, some better than others.

## The “naive” approach

{% highlight erb linenos %}
<h1>Generate prime factors</h1>

<%# User input form here %>

<%=
  @number = @number.to_i
 
  primes = []
  candidate = 2
  while @number > 1
    while @number % candidate == 0
      primes << candidate
      @number /= candidate
    end
    candidate += 1
  end
 
  primes.join(', ')
%>
{% endhighlight %}

{% highlight ruby linenos %}
class GenerateController < ApplicationController
  def index
    @number = params[:number]
  end
end
{% endhighlight %}

I don’t think there are many people trying to cram this much logic into a Rails view, but I’ve heard there’s a lot of PHP out there that does this sort of thing. The problem with the above code is that it has two very different responsibilities—core logic and presentation logic—and therefore more than one reason to change.

The [Single Responsibility Principle][] advises that a given entity (class, module, method, etc.) should have only one reason to change. Otherwise, maintaining the system becomes more difficult, because changing code related to one of an entity's multiple responsibilities (e.g., core logic) will be more likely to require changing code related to others of that entity’s responsibilities (e.g., presentation logic).

## Towards separate responsibilities

{% highlight erb linenos %}
<h1>Generate prime factors</h1>

<%# User input form here %>

<%= @prime_factorer.generate(@number) %>
{% endhighlight %}

{% highlight ruby linenos %}
module PrimeFactorer
  def self.generate(number)
    number = number.to_i
 
    primes = []
    candidate = 2
    while number > 1
      while number % candidate == 0
        primes << candidate
        number /= candidate
      end
      candidate += 1
    end
 
    primes.join(', ')
  end
end
{% endhighlight %}

{% highlight ruby linenos %}
class GenerateController < ApplicationController
  def index
    @prime_factorer = PrimeFactorer
  end
end
{% endhighlight %}

This is an improvement on the last example, because the code representing the core logic of generating a number’s prime factors has been extracted into its own entity. Unfortunately, the new `PrimeFactorer` module still contains multiple responsibilities. While it’s mostly concerned with the process of generating prime factors, it also includes presentation logic; namely, converting the input from a string to an integer and converting the `primes` array into a string.

## Implementing the presenter pattern

{% highlight erb linenos %}
<h1>Generate prime factors</h1>

<%# User input form here %>

<%= @prime_factorer_presenter.prime_factors %>
{% endhighlight %}

{% highlight ruby linenos %}
module PrimeFactorer
  def self.generate(number)
    primes = []
    candidate = 2
    while number > 1
      while number % candidate == 0
        primes << candidate
        number /= candidate
      end
      candidate += 1
    end
 
    primes
  end
end
{% endhighlight %}

{% highlight ruby linenos %}
class PrimeFactorerPresenter
 
  def initialize(number, prime_factorer)
    @number = number
    @prime_factorer = prime_factorer
  end
 
  def prime_factors
    unless @number.nil?
      @number = @number.to_i
      prime_factors_array = @prime_factorer.generate(@number)
      prime_factors_array.join(', ')
    end
  end
end
{% endhighlight %}

{% highlight ruby linenos %}
class GenerateController < ApplicationController
  def index
    @prime_factorer_presenter = PrimeFactorerPresenter.new(params[:number], PrimeFactorer)
  end
end
{% endhighlight %}

This approach further decomposes things down to a `PrimeFactorer` entity to handle core logic, a `PrimeFactorerPresenter` entity to handle the presentation logic, and a view entity concerned not with how the data should be displayed (e.g., with or without surrounding brackets, separated by commas or semicolons, etc.) but simply where on the page the data should appear. In doing so, it ensures that each entity is concerned with a single responsibility, thereby making the system as a whole more modular and adaptable.

[Single Responsibility Principle]: http://en.wikipedia.org/wiki/Single_responsibility_principle
