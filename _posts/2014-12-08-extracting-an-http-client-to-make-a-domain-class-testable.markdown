---
layout: post
title:  "Extracting an HTTP client to make a domain class testable"
date:   2014-12-08
---

The time off app I’m working on makes HTTP requests to the Google Calendar API. To handle this, I created a `CalendarService::Google` class.

To create an event on the applications’s Google calendar, `CalendarService::Google` takes a `DayOff` (which includes things like the email of the user who created it, the date, and what category the day off falls into) and an OAuth access token. It then uses that information to form a request in the manner required by the API (and parse the response the API returns), then send that request off and accept the response.

{% highlight ruby linenos %}
module CalendarService
  class Google
    def self.create(day_off, token)
      http = Net::HTTP.new(base_uri.host, base_uri.port)
      http.use_ssl = true
      headers = { 'Content-Type' => 'application/json', 'Authorization' => "Bearer #{token}" }
      response = http.post(base_url, event_body(day_off), headers)
      OpenStruct.new(
        event_id: parse_event_id(response),
        url: parse_event_url(response))
    end

    # ...
  end
end
{% endhighlight %}

I’d previously created a similar class in the Tic-tac-toe game I wrote in Swift, the primary difference being that the API being hit by that class was one I’d created to return the best next move when presented with a board.

In both cases, I coupled (a) the domain logic of how to prepare the request and what to expect from the response to (b) the side effect of actually sending a request and getting a response with an HTTP library.

Today, another apprentice and I paired for half the day, and spent most of the time extracting an HTTP client class from `CalendarService::Google`:

{% highlight ruby linenos %}
module HTTPClient
  class NetHTTPWrapper
    def create(host, port)
      @client = Net::HTTP.new(host, port)
    end

    def use_ssl=(flag)
      @client.use_ssl = flag
    end

    def post(url, body, headers)
      @client.post(url, body, headers)
    end

    def delete(url, headers)
      @client.delete(url, headers)
    end
  end
end
{% endhighlight %}

As a result, the Google Calendar service no longer has a concrete dependency on a particular HTTP client:

{% highlight ruby linenos %}
module CalendarService
  class Google
    def self.create(day_off, token)
      http = Service.for(:http_client)
      http.create(base_uri.host, base_uri.port)
      http.use_ssl = true
      headers = { 'Content-Type' => 'application/json', 'Authorization' => "Bearer #{token}" }
      response = http.post(base_url, event_body(day_off), headers)
      OpenStruct.new(
        event_id: parse_event_id(response),
        url: parse_event_url(response))
    end

    # ...
  end
end
{% endhighlight %}

The primary (and major) benefit of this change is that it’s now possible to test the way in which the class forms its requests and how it parses the responses it expects will be returned based on Google’s calendar interface. Before, any attempts to do so would have required actually communicating with Google’s servers, and would (among other problems) have resulted in a very slow test suite. Now, all that’s required to test the class is registering a mock HTTP client as the `Service.for(:http_client)`.

While I’d been uncomfortable about these coupled classes when I wrote them, it took till now to actually make the above change. I think the primary reason for this was that the two responsibilities I listed above (knowing how to build the request/what to do with the response on the one hand and actually sending the request/accepting the response on the other) were coupled in my head. It took time and more experience (and maybe a little prompting from Brian Pratt) working with API calls for me to develop a feel for the distinction. The main lesson I draw from this going forward is to listen when my tests (or lack thereof) are making me uncomfortable, even if it’s not immediately clear why.