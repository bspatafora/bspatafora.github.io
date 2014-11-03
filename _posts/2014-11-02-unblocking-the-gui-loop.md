---
layout: post
title:  "Unblocking the GUI loop"
date:   2014-11-02
---

{% highlight swift linenos %}
@IBAction func buttonTouch(sender: UIButton) {
    status.text = "Thinking"
    port.makeMove(move: sender.tag)
}
{% endhighlight %}

The above handler is linked to the buttons I use to represent the 9 spaces of the Tic-tac-toe board and is triggered when any one of them is tapped. It contains two lines of code representing the things I wanted to happen when a user makes a move. First, I wanted to update the `status` label to indicate that the AI player is thinking. Second, I wanted a `makeMove` message with the user’s move to be sent to an instance of the core application class, `UIPort`, with the expectation that it call the client class back according to the new game state.

What happened instead was that `makeMove` executed and called back with the updated game state before the status label was set to “Thinking.” Given that I was resetting this to an empty message once the UI had been updated with the new board (or having announce the outcome if the game had ended), the “Thinking” message ended up not appearing at all.

This seemed bizarre. `makeMove` did block while the AI player made its move in response to the user’s, but there didn’t seem to be any reason it should be able to block execution of a line that came before it. After asking around, it turned out I wasn’t the only one confused by this. Luckily, Brian Pratt around to show me the light.

The reason the blocking code was able to have a seemingly retroactive affect on my attempt to update the status label has to do with the way most GUIs are implemented. When line 2 was executed, what happened immediately was that the `text` property of my “status” `UILabel` got set to “Thinking.” What didn’t happen immediately was this change being reflected in the part of the UI corresponding to that object.

The change, like all other GUI changes, wasn’t reflected in the GUI until the next time it got redrawn. Most GUIs are based around an event loop used to manage how things like mouse clicks get handled and when the GUI gets refreshed. Here’s a simple, abstract example of how the event loop in the QT UI framework works:

{% highlight C++ linenos %}
while (is_active)
{
    while (!event_queue_is_empty)
        dispatch_next_event();

    wait_for_more_events();
}
{% endhighlight %}

The events queued up for the loop to execute can include redraw events, like the one I wanted to happen to my `UILabel`. The reason the redraw event didn’t occur immediately is that the loop was blocked processing the previous event; namely, the touch event being handled by my `buttonTouch` method.

As I said before, the call to `makeMove` resulted in the execution of some blocking code. Because of this, the `buttonTouch` handler that called it ended up blocking execution of the GUI loop in return, preventing any redraws (or any other GUI events) from being processed.

The solution involved replacing the blocking HTTP request I was using to obtain the AI move with an asynchronous request. As a result, `makeMove` could return before the move was obtained, so `buttonTouch` could return as well, unblocking the GUI loop and permitting the label redraw to occur at the appropriate time.