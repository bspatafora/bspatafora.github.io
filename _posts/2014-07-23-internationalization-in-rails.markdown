---
layout: post
title:  "Internationalization in Rails"
date:   2014-07-23
---

Actually, localization as well as internationalization.

The difference between them:

* Internationalization, or I18n, means preparing an app for localization. It’s 
  the process of “putting in the hooks” so different translations/formats can 
  easily be substituted for one another as needed.
* Localization (L10n), refers to the actual “hooking in” of different 
  translations/formats so an app can be used by users who speak a different 
  language [or are pirates][].

In Rails, you define your different locale files in `config/locales`. Rails 
will automatically load any files in this directory via the included I18n gem. 
It understands both Ruby and YAML files, though YAML is the more conventional 
choice.

Here’s the `en.yml` file from a very simple app I recently 
internationalized/localized:

    en:
      title: 'Prime factors generator'
      number_solicitation: 'Enter number:'
      generate_button: 'Generate prime factors'
      generate_more_link: 'Generate more prime factors'
      invalid_input_alert: '“%{input}” is not valid input'
      number_separator: ', '

In my app, this file constitutes the localization half of the process. Every 
user-facing part of the app that might be expected to vary based on someone’s 
language/location is abstracted into a key that can then be given a different 
value in each locale file.

The benefit of this is that all the things that you might need to change in 
order to prepare an app for a different locale now live in one place. Instead 
of having to search through your entire app to root out the bits that need to 
be redefined for the new locale, you can just copy an existing locale file and 
update the keys with whatever values are appropriate.

Note that the I18n gem allows you to do more than simply provide different 
translations. I’ve also defined the punctuation to be used for separating 
numbers, for instance, and the `input` interpolation in `invalid_input_alert` 
accounts for the possibility (or likelihood) that the position of the value 
in the string may change depending on the language.

Of course, the above localization isn’t worth much if the app isn’t 
internationalized to make use of it. Whereas a locale file can be defined in 
just one place, internationalization happens throughout an app.

Here’s an example of a flash alert that has been internationalized to accept 
the locale-specific data provided in whatever locale file is in use:

    def input_was_invalid(input)
      flash.now[:alert] = t(:invalid_input_alert, input: input)
      render :form
    end

Rails provides a `t` helper method to aid in internationalization. Specifying  
a symbol corresponding to a key in the active locale file lets Rails know 
which string to display for the flash message. The second argument provides
the variable Rails should use for the interpolation specified for the 
`invalid_input_alert` key in the locale file.

If you [internationalize your Rails app][] in a manner similar to what is 
demonstrated in the example above, localizing it for a new group of users 
becomes as straightforward as defining a new locale file with the correct 
translations and formats.

[or are pirates]: https://github.com/rory/pirate-l10n
[internationalize your Rails app]: http://guides.rubyonrails.org/i18n.html
