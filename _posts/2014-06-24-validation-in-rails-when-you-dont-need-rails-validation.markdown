---
layout: post
title:  "Validation in Rails when you don’t need Rails Validation"
date:   2014-06-24
---

I was recently tasked with adding some validation to my prime factors Rails app to ensure user input is an integer greater than 1. Given the simplicity of this validation rule, the challenge wasn’t what the code should look like so much as where it should live and how it should be “wired into” the rest of the system.

Since I’m still very new to Rails, I started by doing a search for “input validation in rails.” Most of the results talked about “Active Record validations,” so that’s what I looked into first. [Active Record][], as it turns out, is the [object-relational mapping][] that Rails uses to facilitate the transmission of data between an application and a database. Wrapping these interactions is helpful because most databases understand and store data [in a very different way][] than a Ruby application. One of the things Active Record does to make it easier to use a database with your application is provide validation mechanisms to keep bad data out of your database.

A useful feature to be sure, but since my app doesn’t use a database, it seemed like using Active Record validations would be overkill (if it would even be possible). But in the process of reading up on Active Record and the validation framework it provides, I learned that its not-depedent-on-using-a-database features are available via Active Model. So, I could in theory have done something like this:

{% highlight ruby linenos %}
class NumberInputValidator < ActiveModel::Validator
  def validate(number_input)
    unless integer_greater_than_one?(number_input.value)
      number_input.errors[:value] << 'Input is invalid'
    end
  end
 
  private
 
  def integer_greater_than_1?(number_input)
    # Implementation goes here
  end
end
{% endhighlight %}
 
{% highlight ruby linenos %}
class NumberInput
  include ActiveModel::Validations
  attr_accessor :value
  
  validates_with NumberInputValidator
  
  def initialize(value)
    @value = value
  end
end
{% endhighlight %}

But this too seemed like overkill. Creating an entire class for some simple integer validation is overarchitecting or nothing is. (Edit: One of my mentors, Eric Meyer, clued me in to the existence of the Rails `validates_numericality_of` helper method, which would have removed the need to write my own validator. Still, this approach feels unnecessarily heavy.)

It took a conversation with fellow apprentice Mike Knepper to come to a solution that better fit the scale of the problem:

{% highlight ruby linenos %}
class PrimeFactorerValidator
  def initialize(input)
    @input = input
  end
 
  ERROR_MESSAGE = 'Input is invalid'
 
  def error_message_if_invalid
    return nil if no_input?(@input)
 
    number = Integer(@input)
    return ERROR_MESSAGE if number < 2
    rescue ArgumentError
      ERROR_MESSAGE
  end
 
  private
 
  def no_input?(input)
    @input.nil? || @input.empty?
  end
end
{% endhighlight %}

While it may not be much shorter, the above code is simpler (and not just because I left the actual validation details out of the Rails-y code). I’m not inheriting from or including any other entities as in the Active Model code, which means that though I lose the benefit of such Rails niceties as being able to ask whether a `NumberInput` object is `valid?`, I gain the benefit of a “what you see is what you get” class.

Here’s the new controller and view, with validation wired in:

{% highlight ruby linenos %}
class GenerateController < ApplicationController
  def index
    input = params[:number]
    @prime_factorer_validator = PrimeFactorerValidator.new(input)
    if @prime_factorer_validator.error_message_if_invalid
      @prime_factorer_presenter = PrimeFactorerPresenter.new(nil, PrimeFactorer)
    else
      @prime_factorer_presenter = PrimeFactorerPresenter.new(input, PrimeFactorer)
    end
  end
end
{% endhighlight %}

{% highlight erb linenos %}
<h1>Generate prime factors</h1>

<%# User input form here %>

<p>
  <%= @prime_factorer_validator.error_message_if_invalid %>
  <%= @prime_factorer_presenter.prime_factors %>
</p>
{% endhighlight %}

[Active Record]: http://guides.rubyonrails.org/active_record_basics.html
[object-relational mapping]: http://en.wikipedia.org/wiki/Object-relational_mapping
[in a very different way]: http://bspatafora.com/blog/storing-an-object-in-a-relational-database-two-approaches/
