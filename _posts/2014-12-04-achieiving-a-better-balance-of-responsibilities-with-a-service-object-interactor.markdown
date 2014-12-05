---
layout: post
title:  "Achieving a better balance of responsibilities with a service object/interactor"
date:   2014-12-04
---

This week, I was forced to confront a design decision I’d previously deferred. I’m building an app for tracking employee time off in Rails. When a user adds a day off, it’s added to both the app’s database and to a shared Google calendar.

Before the redesign, I’d allowed some domain logic to live in a Rails controller. It usually isn’t a good idea to couple your domain logic to a detail like Rails because it exposes your system to changes to that detail, makes it more difficult to reuse parts of your system in other contexts (e.g., with another web framework like Sinatra), and can frustrate testing. In this case, I felt it was acceptable to leave the logic in the controller temporarily, since I knew I’d shortly have more information on what other logic would be needed, and expected to be able to use that information to break the domain logic into better abstractions.

This was the `DaysOffController` before the restructuring:

{% highlight ruby linenos %}
class DaysOffController < ApplicationController
  def create
    day_off = RepositoryObject::DayOff.new(
      email: session[:email],
      date: params[:date],
      category: params[:category])
    day_off.url = Service.for(:calendar).add_event(day_off)
    Service.for(:day_off_repository).save(day_off)
    redirect_to home_path
  end

  # ...
end
{% endhighlight %}

Even at this point, it didn’t have much knowledge about how days off get added to the repository or the calendar. But it did know that the creation of days off involves these two kinds of services, and this isn’t something a web framework should concern itself with.

I’d also set aside another problem. In order to add events to a Google calendar under a user’s name, you need an access token for that user. This token expires pretty quickly, and to get a new one, you need to use a separate token—the user’s refresh token. Nevertheless, Google doesn’t want you refreshing a user’s access token each time you want to change some of their data, so it’s necessary to save any new access token (along with information on when it expires) at the point you obtain it from Google.

This means that some part of the system will need to know something about both the Google Calendar API and also the application’s data repository.

Here too, I’d allowed for a temporary coupling while waiting for more information.

In production, `Service.for(:calendar)` (on line 7 above) will return an instance of the following class:

{% highlight ruby linenos %}
module CalendarService
  class Google
    def add_event(day_off)
      http = Net::HTTP.new(uri.host, uri.port)
      http.use_ssl = true
      request = Net::HTTP::Post.new(uri)
      request.add_field('Content-Type', 'application/json')
      request.add_field('Authorization', "Bearer #{Token.for(day_off.email)}")
      request.body = body(day_off)
      event_link(http.request(request))
    end

    # ...
  end
end
{% endhighlight %}

The `Token` class, referenced on line 8, appears below. It was this class—primarily responsible for interfacing with a Google API—that temporarily contained knowledge of both calendar service and repository service details:

{% highlight ruby linenos %}
module CalendarService
  class Token
    def self.for(email)
      user = Service.for(:user_repository).find_by_email(email)
      if token_is_still_valid(user.token_expiration)
        user.token
      else
        response = Net::HTTP.post_form(uri, body(user.refresh_token))
        data = JSON.parse(response.body)
        user.token = data['access_token']
        user.token_expiration = expires_at(data['expires_in'].to_i)
        Service.for(:user_repository).save(user)
        data['access_token']
      end
    end

    # ...
  end
end
{% endhighlight %}

## The Day Off Interactor

The solution came in the form of a domain object with the responsibility of wiring together other domain objects to fulfill a use case—in this case, creating a day off:

{% highlight ruby linenos %}
module Interactor
  class DayOff
    def self.create(args)
      day_off = RepositoryObject::DayOff.new(
        email: args[:email],
        date: args[:date],
        category: args[:category])
      event = calendar_service.create(day_off, token_for(args[:email]))
      day_off.event_id = event.event_id
      day_off.url = event.url
      day_off_repository.save(day_off)
    end

    private

    def self.token_for(email)
      user = user_repository.find_by_email(email)
      update_token(user)
    end

    def self.update_token(user)
      return user.token if token_still_valid(user.token_expiration)
      token_data = token_service.refresh(user.refresh_token)
      user.token = token_data.token
      user.token_expiration = token_data.expiration
      user_repository.save(user)
      token_data.token
    end

    def self.token_still_valid(token_expiration)
      Time.at(token_expiration.to_i) > Time.now
    end

    def self.day_off_repository
      Service.for(:day_off_repository)
    end

    def self.user_repository
      Service.for(:user_repository)
    end

    def self.calendar_service
      Service.for(:calendar)
    end

    def self.token_service
      Service.for(:token)
    end
  end
end
{% endhighlight %}

With this object handling the knowledge of *what* things need to happen in order to update a token, the token class could content itself only knowing how to do the part of this process it should be concerned with—obtaining the new token data via Google’s API:

{% highlight ruby linenos %}
module TokenService
  class Google
    def self.refresh(refresh_token)
      response = Net::HTTP.post_form(uri, body(refresh_token))
      data = JSON.parse(response.body)
      OpenStruct.new(
        token: data['access_token'],
        token_expiration: expires_at(data['expires_in']))
    end

    # ...
  end
end
{% endhighlight %}

And the `DaysOffController` now knows only that the creation of a day off means that the core application has to deal with the creation of a day off, and that it should pass along the input it has received from the user for the purposes of this mysterious process:

{% highlight ruby linenos %}
class DaysOffController < ApplicationController
  def create
    Interactor::DayOff.create(
      email: session[:email],
      date: params[:date],
      category: params[:category])
    redirect_to days_off_path
  end

  # ...
end
{% endhighlight %}

It may become necessary to further break down the day off interactor as its complexity and the system’s complexity increase. But at this juncture, I’m happy with how it has simplified the responsibilities of other parts of the system without knowing too much itself.