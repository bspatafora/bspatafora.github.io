---
layout: post
title:  "My introduction to multithreading"
date:   2014-09-08
---

The first time I needed to manually create a separate thread to work in happened the other day, and it happened (as I finally realized thanks to fellow apprentice Dave Torre) because I told something to block a thread, then tried to do the thing that would have unblocked that thread on that same blocked thread.

I’m writing a server in Java. So far, it exists to create an instance of Java’s `ServerSocket` class, have it listen on some port for a “remote” socket to connect, then, when this happens, write an HTTP response to the client socket.

At this point, the response it sends needs to indicate a status code of 200 and contain the string “Hello, world!” in its body. To verify this, of course, I wrote a couple tests.

In order to exercise the actual server code (creating the server socket, writing to the connected socket’s output stream, etc.), each test needed not only an instance of my server, but also a client socket. These client sockets would connect to my server and (hopefully) receive the response. The information in their output streams could then be tested against the strings I expected to appear in the response.

### The original, broken test

{% highlight java linenos %}
@Test
public void testServerGetRoot() throws Exception {
    Server server = new Server();
    server.start(9000);
    Socket testSocket = new Socket("localhost", 9000);
    BufferedReader in = new BufferedReader(new InputStreamReader(testSocket.getInputStream()));
    String response = in.readLine();
    assertTrue("Response contains a status code of 200", response.contains("200"));
}
{% endhighlight %}

As I alluded to above, this test just hung. By calling `start` on my server, it started listening for a connection, blocking the thread. I tried to give it that connection on the line below it, but that line never got evaluated because the thread was blocked.

### Working test

{% highlight java linenos %}
@Test
public void testServerGetRoot() throws Exception {
    Thread serverThread = new Thread(new Server(9000));
    serverThread.start();
    Socket testSocket = new Socket("localhost", 9000);
    BufferedReader in = new BufferedReader(new InputStreamReader(testSocket.getInputStream()));
    String response = in.readLine();
    assertTrue("Response contains a status code of 200", response.contains("200"));
}
{% endhighlight %}

So, in a move that seems obvious in retrospect but hadn’t even crossed my mind until asking Dave, I gave the server its own thread to run in. As a result, the thread the test was running in remained free to evaluate the line creating the client socket and connecting it to the server, allowing the test to run to completion.

In order to do this, I modified my `Server` class to implement Java’s `Runnable` interface. This allowed me to pass an instance of the class into a newly created thread. Sending it the `start` message in turn executes the `Server` instance’s `run` method in the thread. The modified `Server` class appears below.

{% highlight java linenos %}
public class Server implements Runnable {
    private int port;

    public Server(int serverPort) {
        port = serverPort;
    }

    public void run() {
        try (
            ServerSocket serverSocket = new ServerSocket(port);
            Socket clientSocket = serverSocket.accept();
            PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true)
        ) {
            out.write(statusLine());
            out.write(headers());
            out.write(body());
        }
        catch (IOException e) {
            System.err.println("Caught IOException: " + e.getMessage());
        }
    }

    // ...
{% endhighlight %}