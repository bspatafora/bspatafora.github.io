---
layout: post
title:  "Internationalize your way to cleaner code"
date:   2015-09-09
---

If your project is localized, it’s probably internationalized. If it isn’t, there’s a good chance it’s not.

Depending on your familiarity with those terms, the above may seem annoyingly opaque or blindingly obvious. If it’s opaque, I promise to clarify. If it seems obvious—that this is how things are and how they should be—I’d like to make a case for internationalizing regardless of whether you plan to localize.

Even if it never ends up helping someone who speaks another language, internationalization is worth it because it’s good for your code.

So many letters
============

Localization and internationalization are a lengthy couple of words, so I’m going to use the abbreviations L10n and I18n often (and neglect to consider whether it’s at all grammatical to do so).

They’re also commonly conflated, so let me take a moment to brighten a line.

L10n
------

L10n means taking an application made for one group of people and adapting it for another.

Usually the second group speaks a different language than the first, but language translation isn’t the only change made during L10n. It may also be necessary to alter the way numbers are separated (1,234,567.89 might become the internationally more common 1 234 567,89), or to reformat dates.

I18n
-----

I18n refers to the process of shaping code to make L10n less painful.

At its core, it involves rounding up all the things that might change when localizing (for example, the name of the button a user clicks to abort some action) and extracting them to one easily editable place. That way, when the time comes for L10n, supporting a second locale is as easy as swapping in a new translation file.

I18n is typically thought of as the thing you do to prepare for L10n, but it isn’t strictly necessary to I18n before you L10n. All apps are technically localizable—it’s just that some can be localized a lot more easily than others.

To see why you *should* I18n before you L10n, consider the alternative. Localizing a totally non-I18n app means creating a copy of the codebase and hunting through it—file by file, function by function—to find and replace every localizable thing. Possible, but painstaking.

I18n-ing a file converter
==================

Here’s a simple example of how you might go about internationalizing some existing code.

The class below contains the high-level steps for converting an audio file from one format to another. It has not been internationalized.

```ruby
class Converter
  def initialize(processor)
    @processor = processor
  end

  def convert(file)
    puts "Converting #{file.name} to #{@processor.target_format}..."
    converted_file = @processor.execute(file)

    puts "Conversion complete!"
    return converted_file
  end
end
```

I18n-ing this class comprises swapping out its string literals (“Conversion complete!”) for references that represent the message in the abstract (`conversion_complete`). Here’s one way of going about it:

```ruby
class Converter
  def initialize(processor, translator)
    @processor = processor
    @translator = translator
  end

  def convert(file)
    puts @translator.converting(file.name, @processor.target_format)
    converted_file = @processor.execute(file)

    puts @translator.conversion_complete
    return converted_file
  end
end
```

Now, instead of being bound to the strings that were soldered into it, the class will use the messages defined in the `translator`, whatever the language and however they are formatted. Localizing the code becomes as simple as creating a class that contains translations for a given locale and adheres to the `translator` interface:

```ruby
class EnUS
  def self.conversion_complete
    "Conversion complete!"
  end

  def self.converting(filename, target_format)
    "Converting #{filename} to #{target_format}..."
  end
end
```

```ruby
class EsMx
  def self.conversion_complete
    "¡Conversión completa!"
  end

  def self.converting(filename, target_format)
    "Convirtiendo #{filename} a #{target_format}..."
  end
end
```

The way I internationalized this code is a little unusual. In practice, it’s more common to put translations in language-agnostic data interchange files rather than defining them in code. Rails I18n, for example, uses YAML.

I did things unconventionally to keep things simple, and because my intent isn’t to discuss or advocate for a particular implementation of I18n, but to point out what I18n achieves in the abstract.

Good for code, not just users
======================

I18n makes L10n easier. If the project you’re working on will be used by people outside the locale in which it was developed, you should internationalize it—ideally as early as possible.

But it’s worth internationalizing even if you know you’ll never localize. I18n embodies some of the most important qualities of good software design, and applying it will leave you with a codebase that is more pliable when it needs to change.

The ideas it represents are simple but significant: use abstraction, depend on abstractions, and have a single reason to change.

Use abstraction
-------------------

To create an abstraction is to come up with an interface that exposes *what* the underlying thing can do in the abstract (e.g., store some data) while hiding the details of *how* it does that thing (by sending SQL statements to Postgres/in memory/with a robotic arm and chalk).

Consider the use of `@processor` above. `@processor` is an abstraction. All the messy details of how to convert an audio file from one format to another might have been embedded in `Converter`, but the use of abstraction means it doesn’t need to concern itself with them. Instead, all it needs to know is that a processor is something with an `execute` method that takes a file and returns a file.

Internationalizing `Converter` was in part the process of replacing detail with abstraction. The original version concerned itself with the details of the progress messages, whereas the I18n `Converter` is blissfully ignorant of them. Its translator’s `converting` method could return Mandarin, Spanish, English, or Konkani, and it would be equally happy with each. It doesn’t need to know whether its translator has the strings hardcoded into it or reads them from a file.

Depend on abstractions
----------------------------

One of the big ways to benefit from using abstraction is to depend on it instead of concretion. Doing so affords a system with greater flexibility, and makes it more resilient to change.

Even though `Converter` uses the abstraction `puts` rather than taking on the details of how exactly to get a string printed to the console, it doesn’t depend on that abstraction. Instead, it has a concrete dependency on `Kernel`, via its direct use of `puts` to output messages to the user. (While it looks like `puts` is being called on nothing, it’s actually a method on `Kernel`, and available pretty much everywhere because `Kernel` is included in `Object`.)

The result is a class that is less amenable to a certain kind of change. If we wanted to adapt `Converter` for a context other than the console, we’d need to make changes in multiple places (all the `puts` statements) instead of just one (the abstract I/O dependency we might have passed on instantiation instead).

Admittedly, this change wouldn’t be painful at all if simple little `Converter` comprised the whole system. But code is cheap, and systems grow. Much harder to make a change like this when you have to touch a dozen long files to do it. Breaking something becomes likelier, and it’s monotonous.

We could have rewritten `Converter` to abstract away the message details to `EnUS`, and then depended directly on that class on lines 8 and 11. But by relying only on the `translator` abstraction passed on instantiation, `Converter` can be passed and work happily with `EnUS`, `EsMx`, and anything else that implements the `translator` interface.

Single responsibility per capsule
-------------------------------------

By fostering abstraction and dependence on abstraction (instead of concretion), I18n moves a codebase towards the ideal of a single responsibility per capsule (function, class, etc).

The original `Converter` concerned itself with not only the high-level steps involved in converting an audio file (in this case, passing the file path to some processor, returning the result, and informing the user of the progress), but also the lower-level details of what exactly to say to the user at a given juncture. By extracting those details into a `translator` class, the class has a simpler and more obvious role.

In addition to the increased clarity this affords, limiting each capsule to one reason to change makes the system as a whole easier to change. When a reason to change, such as message localization, is shared by many parts of a system, they all have to change in order for the concern to be changed. Anytime you change the inner workings of a capsule, you chance breaking it or another capsule that depends on it.

But by limiting how many details any given capsule knows, and by limiting dependencies to abstractions, you can minimize a system’s vulnerability to change.
