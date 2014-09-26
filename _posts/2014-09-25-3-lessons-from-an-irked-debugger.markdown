---
layout: post
title:  "3 lessons from an irked debugger"
date:   2014-09-25
---

I ran into two exasperating bugs in the last 24 hours. Their frustratingness was primarily due to their opaqueness, though the simplicity of their solutions (once found) also factored in.

The details aren’t so important, but here’s a rough rundown.

Yesterday, I’d added some functionality (serving images) to my Java server and both a unit test and one of the acceptance tests started throwing exceptions. At first glance, neither error seemed to have any relationship to what I had added (or to one another for that matter). The unit test exception was telling me a connection had been refused, and the acceptance test exception  insisted the end of a file had been reached (the horror).

I knew that these exceptions were being raised for reasons that made sense, but from my perspective, they might as well have been random. This is because the failures resulting in the exceptions being raised were happening at some too-great level of remove from the things I was myself working on. **Lesson one from the last day or so: fail fast**. The source of this problem turned out to be a null pointer. Part of what made it hard to diagnose was the fact that the “connection refused” error was happening later and in a different place than the null pointer. If I’d designed the system to blow up as soon as the null was encountered, the source of the problem would have been more readily apparent. (A better solution in this case might be making it impossible for null to show up in this context, which is what I ultimately did, but this detail doesn’t change the broader principle.)

**Second lesson: Step back.** I’d been banging my head against the wall for a few hours, getting nowhere and feeling pretty lost. It was only once I decided to cut my losses for the moment and switch to working on something else that I figured out what the issue was. The unfortunate detail here is that what I had been missing was a part of the test result (containing the actual null pointer exception message, with a link to the line it occurred on) that I didn’t know existed due to my unfamiliarity with the IDE I’m using. But this detail remained hidden in plain sight until I finally decided to stop staring uselessly and let my brain switch to something else for a while. (Though rule 2 1/2 might be to make sure you know how to use the tools you’re using.)

The other bug I encountered was similarly opaque, and similarly simple to solve once I knew what to do. In this case, the solution was a rubber duck session with another apprentice (thanks, Andrew). So my third takeaway is to **ask for help when you need it**. As the economists say, there are gains from trade. What feels like a foggy, insurmountable challenge to you might just be 30 seconds of someone else’s time.