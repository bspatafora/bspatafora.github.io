---
layout: post
title:  "Validation in Rails when you don’t need Rails Validation"
date:   2014-06-24
---

I was recently tasked with adding some validation to my prime factors Rails 
app to ensure user input is an integer greater than 1. Given the simplicity of 
this validation rule, the challenge wasn’t what the code should look like so 
much as where it should live and how it should be “wired into” the rest of the 
system.

Since I’m still very new to Rails, I started by doing a search for “input 
validation in rails.” Most of the results talked about “Active Record 
validations,” so that’s what I looked into first. [Active Record][], as it 
turns out, is the [object-relational mapping][] that Rails uses to facilitate 
the transmission of data between an application and a database. Wrapping these 
interactions is helpful because most databases understand and store data [in a 
very different way][] than a Ruby application. One of the things Active Record 
does to make it easier to use a database with your application is provide 
validation mechanisms to keep bad data out of your database.

A useful feature to be sure, but since my app doesn’t use a database, it 
seemed like using Active Record validations would be overkill (if it would 
even be possible). But in the process of reading up on Active Record and the 
validation framework it provides, I learned that its 
not-depedent-on-using-a-database features are available via Active Model. So, 
I could in theory have done something like this:

{% gist bspatafora/00c08a99132804c385dd %}

But this too seemed like overkill. Creating an entire class for some simple 
integer validation is overarchitecting or nothing is. It took a conversation 
with fellow apprentice Mike Knepper to come to a solution that better fit the 
scale of the problem:

{% gist bspatafora/79978bc1d018690ed025 %}

While it may not be much shorter, the above code is simpler (and not just 
because I left the actual validation details out of the Rails-y code). I’m not 
inheriting from or including any other entities as in the Active Model code, 
which means that though I lose the benefit of such Rails niceties as being 
able to ask whether a `NumberInput` object is `valid?`, what you see is what 
you get.

Here’s the new controller and view, with validation wired in:

{% gist bspatafora/65aabbc4bc92fa772c7b %}

{% gist bspatafora/36c60dbe4817a90bb432 %}

[Active Record]: http://guides.rubyonrails.org/active_record_basics.html
[object-relational mapping]: http://en.wikipedia.org/wiki/Object-relational_mapping
[in a very different way]: http://bspatafora.com/blog/storing-an-object-in-a-relational-database-two-approaches/
