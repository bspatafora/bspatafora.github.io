---
layout: post
title:  "When in doubt, check for nil"
date:   2015-01-29
---

Since joining 8th Light, I’ve heard from many about the dangers of passing nil around a system. The first few times I heard it, it was just sort of a spooky thing that sounded like a important advice, but only because of the gravity with which it was expressed. At that time, I didn’t have a real sense of why it was a problem.

Working on my own projects, I had a few encounters with unexpected nils, and learned how frustrating they could be. One of the tricky things about nil is that it can show up in unexpected places, having crept through numerous parts of the system undetected. You then have to take time to trace it back to its origin, and fix the code at the source so that it never returns nil.

But I don’t think I really understood how dangerous nil can be until starting on client work. With my own projects, even the more complicated ones, I had a pretty good sense of how every part of the system worked. Being the only developer in the project, I could also make a real effort to avoid passing nil around the system, though of course there were plenty of cases that slipped by me. Real projects, however, can be orders of magnitude more complicated than the pet projects of a single developer. They’re also written by a variety of people, and can depend on third party code and data of questionable integrity. All of this leads to more dark corners for nil to hide in, and makes vigilance that much more important.

I’m sorry to say that I missed an occasion for nil-checking in a recent pull request, and had to have it pointed out to me by the team lead. Here’s the code in question:

{% highlight ruby linenos %}
class Track < ActiveRecord::Base
  has_attached_file :digital,
    :path => "path/to/file/based/on/#{track_id}",
    :url =>  "url/with/#{track_name}"

  def playable?
    File.file?(digital.path)
  end
end
{% endhighlight %}

I’d added the `playable?` method on line 6 to the existing `Track` model in order to discriminate between track objects with associated audio files and those that only exist to provide metadata like the name and artist. As the team lead pointed out in a comment, if either `digital` or `path` return nil, `file?` will blow up.

(After making the fix, I remembered the interpolated strings used to define the digital file path and url, on lines 3 and 4. I do think seeing this (which wasn’t directly visible from the pull request) made me more comfortable with not providing for the possibility of unexpected nils, but given my unfamiliarity with the library handling this part of the code, it still made sense to err on the side of caution.)

To fix my mistake, I first wrote a failing test exercising the possibility of nils:

{% highlight ruby linenos %}
it "returns false when the track has no attached file" do
  track1 = Spree::Track.create!(digital: track1_mp3)
  track2 = Spree::Track.create!(digital: track1_mp3)
  allow(track1).to receive(:digital) { nil }
  allow(track2).to receive(:digital) { OpenStruct.new(path: nil) }

  expect(track1.playable?).to be false
  expect(track2.playable?).to be false
end
{% endhighlight %}

Then added a line of code to protect from the exceptions that could occur if either `digital` or `path` were to return nil for any reason:

{% highlight ruby linenos %}
def playable?
  return false if digital.nil? || digital.path.nil?
  File.file?(digital.path)
end
{% endhighlight %}