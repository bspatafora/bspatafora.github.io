---
layout: post
title:  "Talk: Rescuing Healthcare.gov"
date:   2014-10-12
---

Paul Smith was part of the team brought on to fix healthcare.gov after its disastrous launch. I attended a recent event where he talked about his experiences and observations during the process.

When the website launched a year ago, it quickly became obvious that there were severe problems with how the underlying system had been designed. The magnitude of the failure—only 6 people successfully enrolled on the first day—was evidence enough of this fact. What wasn’t immediately clear is what exactly had gone wrong.

## Underlying issues

According to Smith, bad communication was the ultimate source of many of the system’s problems. He recounts a scene in the “war room” that had been established during the rescue that illustrates his point. The war room was a conference room meant to enable the many groups (primarily contractors) involved in the system to communicate with one another with the speed that only in-person conversation permits. Not long after everyone had gathered together in the room for the first time, a member of one of the contractors involved in the system’s development observed that it was the first time they’d met or even spoken with members of many of the other groups present. This despite the fact that these groups had spent the last couple years working on the same project.

A related issue was the high-level design process, which was delegated top-down from policy-makers to implementers, and seemed defined by a largely one-way communication structure running in the same direction. Rather than working iteratively with the development teams, allowing the perceived requirements and implementation details to gradually shape one another, the clients (here, the Department of Health and Human Services and the Centers for Medicare and Medicaid Services) simply told the contractors what needed to be done and expected that it would be.

## Technical failure

Of course, these issues weren’t directly responsible for the failure of the resulting system at launch. These problems, combined with poor development practices and technical decisions  on the part of the involved contractors, gave rise to the specific details of the system that failed and to the general fragility of the system that allowed these failures to spread.

Smith cites one of the problems his team identified and fixed to give a sense of the state of the system when it first launched. Once the team got some monitoring set up, they saw that the biggest bottleneck seemed to be occurring around the database, though the database itself wasn’t being accessed much at all. Instead, they determined that the actual issue was that connections between the application and the database were being needlessly kept alive, and that these connections were accumulating and devouring system resources in the process. After a member of the team turned off the keep-alive setting, average response times dropped from 8 seconds to 2 (still terrible, but clearly an enormous improvement).

## Lessons learned

This episode, along with the others Smith shared, illustrated a few things about the system’s problems and the approach he and his team took in solving them. One was the lack of skill on the part of at least some of the contractors involved in building the system. Smith mentioned that a lot of the components used were enterprise software not really suited for the task at hand, and that many of them were still in their “out of box” configurations.

Another, which Smith didn’t focus on but which felt very familiar to me thanks to the 8th Light Services Engineering group, is that the system clearly had not been designed to anticipate failure. If it had, there would have been enough load testing done that this issue (and many others) would very likely not have made it to production. But even if it had, the system as a whole would have fared much better if those building it had given due weight to [designing a stable system][].

Finally, it provided a glimpse at how Smith and his team approached the problem. They immediately got a monitoring system in place so they could accurately and precisely diagnose what was going wrong with the system. They prioritized their work, making sure to always work on the issue causing the biggest performance or reliability hits (i.e., the bottleneck). And, as Smith emphasized as an overarching aspect of their approach, he and his team made sure to convey the attitude that the problems they were facing were, in fact, solvable.

[designing a stable system]: http://bspatafora.com/blog/designing-stable-systems/