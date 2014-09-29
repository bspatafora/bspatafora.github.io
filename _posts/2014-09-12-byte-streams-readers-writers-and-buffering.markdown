---
layout: post
title:  "Byte streams, readers/writers, and buffering"
date:   2014-09-28
---

I’ve been using these guys quite a bit in building my Java server, and I found working with them very confusing when I first started, so I thought I’d spend some time getting to know them a little better.

  * Byte streams
      * Allow you to read/write binary data from/to a source/destination, such as a file or socket
      * Descendants of `InputStream` and `OutputStream`
      * Examples include `ByteArrayInputStream`/`ByteArrayOutputStream` and `FileInputStream`/`FileOutputStream`
      * Each time you read or write with a byte stream, an 8-bit byte is read from the source or written to the destination
  * `BufferedInputStream` and `BufferedOutputStream`
      * Descendants of `InputStream` and `OutputStream` (via `FilterInputStream` and `FilterOutputStream`, whose derived classes decorate plain `InputStream`s and `OutputStream`s, augmenting them with additional functionality)
      * In the case of `BufferedInputStream` and `BufferedOutputStream`, the additional functionality is, as their names suggest, buffering the input/output
      * Instead of having to `read` from or `write` to the underlying byte stream (which in turn interfaces with the source or destination, which might be a file or socket) with each call to one of those methods (which can be an expensive operation), the `BufferedInputStream` or `BufferedOutputStream` fills its buffer (8KB by default) after the first call to `read` or `write`, allowing subsequent calls to be read/written from memory (until the buffer is emptied)
  * Readers and writers
      * Allow you to read/write characters from/to a source/destination
      * Readers/writers like `FileReader`/`FileWriter` wrap byte streams, and also automatically convert bytes to characters in the default or specified character encoding
  * `BufferedReader` and `BufferedWriter`
      * Like `BufferedInputStream` and `BufferedOutputStream`, but wrap readers and writers instead of byte streams, buffering them to reduce the cost of read/write operations