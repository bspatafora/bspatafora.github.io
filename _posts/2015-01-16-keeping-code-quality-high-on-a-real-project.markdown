---
layout: post
title:  "Keeping code quality high on a real project"
date:   2015-01-16
---

I joined a client project team a few weeks ago. It’s my first time doing client work, and also the the first time I’ve worked on a (non-trivial) project that I didn’t start myself. Though I joined the project after spending months developing my knowledge of and experience in the coding techniques that 8th Light espouses, I feel like I’ve been treading on relatively new territory in having to work with code I didn’t write.

## Staying humble

Sometimes, I stumble across code that I’m very tempted to change. When I first started working on the project, I paired with another apprentice who had been on the project for some time. As we walked through the project, there were a good number of things that jumped out at me as probably problematic. One thing that sticks in my mind is that some of the views contain a substantial amount of logic, enough that they seemed to be taking on some of the work of their controllers. It seemed to me that it would be ideal to reestablish the separation of responsibilities between the project’s views and controllers.

Instead, I set aside my concerns and stayed focused on the story we had been tasked with completing. It felt wrong to do this, especially after so many months of working on personal projects that I could spend time actively rethinking and refactoring, but it seemed like the right decision. I’d been advised by one of my mentors to tread lightly when joining a new project. Without the context you build up from working with a codebase for days and weeks and months, there’s a good chance your evaluations are based on only a partial understanding of the full picture. In this case in particular, I wasn’t aware that the choice of CMS had restricted the use of our own controllers. In this context, the too-smart views made more sense.

## Changing what you can, knowing what you (alone) can’t

Of course, that doesn’t mean there isn’t a better solution. However, as I’ve learned from listening to the customer and talking with team members and others, there simply isn’t enough time to do every refactoring the team would like. Given this reality, I’ve tried to focus on doing what I can do to keep the quality of the code high.

The kinds changes I’ve made to improve the project have been small scale. I imagine this is often the way clean-up and maintenance has to be done on projects of non-trivial size. It’s a lot more straightforward to clean up the methods in the class you’re working with or to add a few tests here and there than it is to implement changes that have far-reaching effects on the system.

One thing I’ll be looking out for as I work on more projects is how bigger changes actually happen. Architectural or other large changes must sometimes be necessary in order to prevent the system from rotting as requirements change in many or major ways. It seems like this kind of effort would require coordination among team members, maybe initiated by a meeting to propose and discuss a change important enough to require this kind of effort, and sustained as attention shifts to more imminent needs and the composition of the team changes.
