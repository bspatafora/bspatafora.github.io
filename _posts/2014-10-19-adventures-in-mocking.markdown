---
layout: post
title:  "Adventures in mocking"
date:   2014-10-19
---

Last week I rolled my own mock object for the first time. Before that, I’d either found ways to test without mocks or used a dynamic mocking framework, like [the one included in RSpec][].

With a mocking framework, it’s fast and easy to create mock objects. In RSpec, it might look something like this:

{% highlight ruby linenos %}
it "tells its adapter the winning token" do
  game = Game.new(
                  # ...
                 )
  mock_ui_adapter = double()
  ui_port = UIPort.new(game, mock_ui_adapter)

  allow(mock_ui_adapter).to receive(:game_was_updated)
  ui_port.move_was_made(0)
  expect(ui_port).to have_received(:game_was_updated).with(game.spaces)
end
{% endhighlight %}

Here, Rspec Mocks is doing the work of creating a mock object for you. On line 5, the mock is created, and on line 8 it has a method added to its interface. The subsequent `expect` statement works because the the framework does more than the above.

In order to be able to test whether the mock object received a `game_was_updated` message, and what was passed if and when that message was received, the mock must keep track of this information. While I’m not sure exactly how RSpec Mocks implements this, it probably bears some resemblance to how I did things below (in Swift).

Because Swift is statically-typed, the first step was to define an interface (or what Swift calls a “protocol”) that both the mock and real objects could implement:

{% highlight swift linenos %}
protocol UIAdapterProtocol {
    func boardWasUpdated(#spaces: [String])

    // ...
}
{% endhighlight %}

Then, I created a `MockUIAdapter` that implements that interface:

{% highlight swift linenos %}
class MockUIAdapter: UIAdapterProtocol {
    var receivedUpdatedSpaces: [String]?

    func boardWasUpdated(#spaces: [String]) {
        receivedUpdatedSpaces = spaces
    }

    // ...
}
{% endhighlight %}

Instead of containing the intended behavior of real `UIAdapter` objects, it’s designed so that client objects can call the methods that make up its interface and tests can verify that those clients have interacted with the mock as expected.

{% highlight swift linenos %}
it("sends its adapter updated spaces when game continues") {
    let game = Game(
                    // ...
                    )
    let adapter = MockUIAdapter()
    let port = UIPort(game: game, adapter: adapter)

    port.moveWasMade(move: 0)
    expect(adapter.receivedUpdatedSpaces!).to(.Equal(game.getSpaces()))
}
{% endhighlight %}

Here, the test is able to create a client object (`port`) that sends a message to the mock with some data, then subsequently ask the mock about the interaction. Specifically, the test asks to see the updated spaces it should have received when `port` sent it `boardWasUpdated`, which also implicitly verifies that the mock received the object in the first place.

The reason I made my own mock object was that there isn’t a good mocking framework for Swift yet. I was pushed to it by necessity. But I think I’ll be doing it more often going forward. I was glad to get more insight into how mocking works, and was pleasantly surprised to find out it’s very easy to do yourself.

[the one included in RSpec]: https://github.com/rspec/rspec-mocks