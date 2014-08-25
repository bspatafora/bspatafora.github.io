---
layout: post
title:  "Some thoughts on professionalism in coding"
date:   2014-08-24
---

Based on the “Coding” chapter in [The Clean Coder: A Code of Conduct for Professional Programmers][].

  * When coding, you need to balance a number of concerns at once. Sometimes they compete, but all are important.
      1. First and foremost, the code needs to work. This is the most obvious requirement, but it isn’t the only thing to consider.
      2. The code you write should solve the client’s problem. This might seem obvious, but sometimes a client will provide requirements that you realize will not actually achieve the goal the client has in mind. When you realize this, you either need to code to the spirit of the requirement rather than the letter (if the discrepancy is obvious and you are confident you know what the client was actually interested in achieving) or, more likely, contact the client to make them aware of the matter and ask whether they would like to modify the requirement.
      3. Your code must fit well into the existing system. While you might be able to get that feature implemented by sort of duck taping it into the system, this is unprofessional. Any changes should be made in such a way as to preserve or increase the system’s flexibility.
      4. Your code needs to be readable by other programmers. You aren’t the only one working on the system, and it’s probably not even your system, so any code you write should account for this fact. It should be logically structured and expressively written to ensure that other developers are able to work with it without incurring unnecessary mental overhead.
  * Don’t write code when you’re in no condition to write code. Uncle Bob calls this “3 am code.” If you’re up against a deadline, it sometimes seems like you should just keep working, regardless of how long you’ve been working or what mental state you’re in. But this is false thrift. Even though you’re putting characters on a screen, the code you write is a lot more likely to create a costly mess when you’re exhausted or otherwise unable to focus and think.
      * Relatedly, be aware that it’s sometimes necessary to step away and turn your mind to other things, particularly when you’ve been stuck on a problem for some time. Brains can do amazing things when given a little time to work things through on their own.
  * In general, avoid flow. According to Uncle Bob, it’s not really the hyper-productive state it’s purported to be. As with “3 am code,” code you write in flow carries an increased risk of creating future problems because it brings with it a sort of myopia that reduces awareness of the “big picture” (that is, how the code you’re writing fits into and interacts with the rest of the system).
  * Pair.
      * Pairing makes interruptions less intrusive because one of you can maintain context and bring the other back up to speed when they’ve dealt with the other matter.
      * When you’re experiencing “writer’s block,” finding someone to pair with is often the best thing you can do to get going again.
      * A professional gives and receives help, and pairing is one of the most effective ways for developers to help and learn from one another.
  * Taking in creative input from a variety of sources will increase your creative output.
      * Uncle Bob likes science fiction.
  * Use test-driven development. Debugging wastes a ton of time, and you can avoid a lot of it if you go through the red-green-refactor cycle and have tests that can cue you into the source of any problems that arise in the system.
  * “There is no way to rush.” Trying will just result in messes that slow you down later. As a professional, you need to stick to your disciplines. They exist for a reason, and become all the more important in a crisis.
  * Overtime is draining, so “you should *not* agree to work overtime unless (1) you can personally afford it, (2) it is short term, two weeks or less, and (3) *your boss has a fall-back plan* in case the overtime effort fails.”
  * Professionals are “honor bound” to help others, and must be able to accept help as well.

[The Clean Coder: A Code of Conduct for Professional Programmers]: http://www.amazon.com/The-Clean-Coder-Professional-Programmers/dp/0137081073