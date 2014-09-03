---
layout: post
title:  "Web servers versus application servers"
date:   2014-09-02
---

A **web server** serves HTTP responses in response to HTTP requests.

HTTP requests consist of these things:

  * A “request line,” indicating it is a request, and specifying the following:
      * The method name (e.g., POST)
      * The path of the requested resource (e.g., /directory/file.type)
      * The version of HTTP used (e.g., HTTP/1.0)
  * Zero or more header lines which, if present, provide additional information like the server (e.g., indicating the response came from an Nginx web server)
  * A body which, if present, contains non-meta data being sent from the client to the server (e.g., form data with a list of the user’s aspirations)

HTTP responses consist of these things:

  * A status line, indicating it is a response, and specifying the following:
      * The version of HTTP used (e.g., HTTP/1.0)
      * A status code (e.g., 404)
      * A “reason phrase” describing the status code (e.g., Not Found)
  * Zero or more header lines which, if present, provide additional information like the user agent (e.g., indicating the request came from a Safari browser)
  * A body which, if present, contains the requested resource (e.g., an HTML webpage of a store catalog)

An **application server** serves application content, which it may or may not do via HTTP. It is a means of exposing business logic to client applications. In the context of a web application, the client application is typically a web server.

If a physical server’s web server and application server are distinct, requests are received by the web server. The web server then either sends a response if the request is for static content (e.g., an image), or passes the request along to the application server if responding to the request involves generating content dynamically. In the second case, the web server will return the response provided by the application server.

Strictly speaking, a web server is limited to the HTTP protocol, and an application server is not.

These days, however, the line between web servers and application servers is pretty blurred. Oftentimes a server application will take on functions of both the web server and the application server.