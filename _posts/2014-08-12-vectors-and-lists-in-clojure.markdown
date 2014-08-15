---
layout: post
title:  "Vectors and lists in Clojure"
date:   2014-08-12
---

Clojure gives you a lot of collection-type data structures to work with. There’s lists, vectors, hash maps, array maps, and sets, and I’m pretty sure I’m missing a few. Actually, for all I know, I could be missing a hundred.

As a newcomer to the language, I find it difficult to know how to decide which structure to use for any given job. It’d be overwhelming to try to really understand all of them all at once, but I thought it would be useful to compare two of the more straightforward ones: the vector and the list.

Clojure’s vector is like a simple [contiguous array][]. The elements of such an array are held in adjacent memory locations.

In contract, a Clojure list is similar to a [singly-linked list][]. In a linked-list, the elements are no longer (necessarily) stored contiguously in memory. Instead, each element in a singly-linked list stores two pieces of data: the actual value of the element and the address of the location of the next element of the list.

As for when to use each, it doesn’t much matter unless you’re working with very large datasets. If you are, though, you should take the following characteristics into account in deciding which to use:

  * Vectors can be indexed very quickly. No matter the size, you’ll be able to access an element at any given index in O(1) time (or possibly O(log32n) time, but either way, very quickly).
  * It takes longer (O(n)) to index a list.
  * It is much easier to insert or remove elements from within a list than a vector.
  * It’s easier to add to the end of a vector than the beginning.
  * It’s easier to add to the beginning of a list than the end.

Note that when talking about immutable data structures like Clojure’s lists and vectors, terms like “insert,” “remove,” and “add” don’t mean exactly what they suggest, since the operations involve the creation of a new collection rather than the modification of the original. The above still holds, however.

One additional note: Because these two data structures differ in performance characteristics, built-in Clojure functions that operate on them sometimes behave differently depending on the type of collection passed. For example, calling `conj` on a vector will add the new data to the *end* of the vector, whereas calling it on a list will add to the *beginning* of the list. While the different treatment of functionally-similar collections seems odd in isolation, it makes sense in the context of the low-level data structures used to implement them.

[contiguous array]: http://en.wikipedia.org/wiki/Array_data_structure
[singly-linked list]: http://en.wikipedia.org/wiki/Linked_list
