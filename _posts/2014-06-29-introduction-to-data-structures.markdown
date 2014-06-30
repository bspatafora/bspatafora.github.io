---
layout: post
title:  "Introduction to data structures"
date:   2014-06-29
---


This post, like [Introduction to Big O notation][], is based heavily on 
Mark Grant’s lessons.

### Some unnecessary (and sketchy) background

Computers store data using 1s and 0s. This well-known fact isn’t strictly 
needed to gain an understanding of what data structures are, but since 
data structures are constructed out of these stored bits and aren’t even 
that far removed from them, since most data structures are pretty low 
level, it seemed worth remarking on.

After writing that computers store data using 1s and 0s, I wondered why 
computers store data using 1s and 0s. While there may be more factors 
involved, it seems like the dominant reason has to do with the physical 
mechanism most computers use to store data.

While the specifics of how this is achieved vary, most computers store 
data using electricity. As it turns out, there are lots of external 
factors that can influence the transmission of electricity in a system, 
such as the quality of the components used to transmit the current. 
Because the net result of these factors can cause significant 
fluctuations of the expected transmission within a system, it’s more 
reliable to depend only on whether electricity is present (1) or absent 
(0). While it’s possible to create a system that distinguishes a third 
state (electric current at half of capacity), or a system that 
distinguishes even more granularly than that, these systems would 
become less and less reliable due to inevitable signal interference 
changing their state.

### Bigger than bits

With 1s and 0s, and [a system for counting with those digits][], you 
can start storing numbers. For example, to store the number 24, you 
could write the following series to memory: 11000.

But how does the computer know to treat all five of those bits as 
a unit? What keeps it from reading only the first bit and deciding 
the number we wanted to store was 1?

The answer is that each computer system decides on a number of bits to 
treat as an “atomic” (i.e., indivisible and most basic) chunk of 
data. I don’t know what the most commonly used number of bits is in practice, 
but it simplifies things to just pretend it’s 8 (i.e., one byte).

### Linking chunks with other chunks

So now we can count up to 11111111 (255). But what if we want to count higher?

In addition to grouping bits into “atomic” chunks, the computer can also decide 
to group chunks together into other chunks. The most basic way of doing this is 
to decide that chunks that are physically next to each other in memory should go 
together. So, for example, the computer could decide to treat the first and 
second physical chunks of memory as one unit. These chunks might together add 
up to 1111111111111111 (65,535). So clearly we can keep right on counting.

Moreover, at this point we can in theory store more than numbers. We just need 
a system for converting binary to whatever else, just like we converted binary to 
decimal above. For example, 01100001 represents the character “a” in the ASCII 
character-encoding system. But that same chunk of memory could just as easily 
be a part of an image or a movie.

### More structure

Another thing we could encode using chunks of memory is a list of numbers.

The most straightforward way of doing this would simply be to treat some chunks 
of memory that are physically sequential as the values of the list. This works 
pretty well, because once you’ve defined the boundaries of the list (e.g., it 
starts at memory chunk 50 and goes to memory chunk 80), accessing the value 
at a particular position in it (e.g, position 15 in the list) is just a matter 
of adding its position to the starting point of the list (50 + 15 = 65, so read 
the value at location 65).

But if you have a really long list and want to add something to the middle of 
it, this scheme suddenly doesn’t look so appealing. It’s also problematic because 
each time the list fills up, adding to it means designating a whole new swath of 
memory for the list, and then copying each of the existing values to their locations 
in the new list before finally adding the new value.

This problem can be solved with one of the most basic data structures: the linked 
list. Instead of treating individual chunks of memory as values of the list and 
depending on their ordering to determine the structure of the list, each item of 
a linked list consists of two chunks of memory—one containing of the value, and 
the other representing the memory address of the next item in the list. While 
this means storing a given list will now take up twice the memory, using this 
data structure can result in much faster writing to the list under certain 
circumstances, such as the one described above.

While this is a simple example, the basic idea holds true for more complex 
data structures: A data structure is a way of structuring data that uses some 
memory addresses to store the actual data you want to store and other memory 
addresses to store the memory addresses of other data in the data structure.

[Introduction to Big O notation]: http://bspatafora.com/blog/introduction-to-big-o-notation/
[a system for counting with those digits]: http://en.wikipedia.org/wiki/Binary_number#Counting_in_binary
