---
layout: post
title:  "Establishing application context"
date:   2014-11-23
---

It’s often the case that an application needs to interface with external services in order to function. This is the case for the “time off” application I’m currently working on. One of the requirements is that when you add a day off to the application, it gets added as an all-day event to a shared Google calendar.

Despite the fact that Google Calendar integration is core to what this application does, I still wanted to create a boundary between the application and the Google Calendar API. A look into why this is a good idea can be found in [a previous post].

There are lots of different ways to insulate an application from the extra services it needs. By the time I was ready to add Google Calendar integration, I had already implemented [the repository pattern][] to keep storage details from spreading throughout my application, so I decided to broaden it a little so its `Repository` class (which I renamed to `Service`) could be used to get both object repositories and other service interfaces.

The `Service` object is a singleton that wraps a hash of available services. Objects throughout the application can ask for the `Service.for(:whatever)` in order to obtain either an instance of an object (in the case of a repository) or a reference to a class or module.

{% highlight ruby linenos %}
class Service
  def self.register(type, service)
    services[type] = service
  end

  def self.services
    @services ||= {}
  end

  def self.for(type)
    services[type]
  end
end
{% endhighlight %}

Application context is established by individually `register`ing the services appropriate for the environment. So, for example, I can register an in-memory implementation of a `UserRepository` for tests:

{% highlight ruby %}
Service.register(:user_repository, MemoryRepository::UserRepository.new)
{% endhighlight %}

And an ActiveRecord implementation in production:

{% highlight ruby %}
Service.register(:user_repository, ARRepository::UserRepository.new)
{% endhighlight %}

I can similarly register either a test or real (i.e., Google) interface for a calendar service.

I like this approach because it achieves the goal of not coupling the application to the services it uses while remaining very simple to use. There is some indirection added—it would be somewhat simpler to just see calls being made directly to `GoogleCalendar` rather than `Service.for(:calendar)`—but not much and it buys you a whole lot.

Something I’m keeping in the back of my mind as the application develops is the possibility of creating objects that know exactly which services are appropriate for a given context (e.g., the in-memory repositories and the mock calendar service should be used for testing and development). This would add a little more complexity in the form of additional objects, but setting up the application for a given context would become simpler. If there comes a point where it’s necessary to register more than just a few services, this will probably be a worthwhile tradeoff.

[the repository pattern]: http://blog.8thlight.com/mike-ebert/2013/03/23/the-repository-pattern.html
[a previous post]: http://bspatafora.com/blog/wrap-external-dependencies/