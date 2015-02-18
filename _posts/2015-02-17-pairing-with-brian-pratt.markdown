---
layout: post
title:  "Pairing with Brian Pratt"
date:   2015-02-17
---

I spent today pairing with Brian Pratt. Like yesterday, we started the day under the pressure of a deadline. There was a demo scheduled for the next day, and there were a number of things the team needed to prepare for it. At standup, though, it was agreed that it would be best to push the demo back a couple days. That way, the team could ensure a smooth, well-rehearsed demo rather than risking unexpected hiccups.

The focus on upcoming deadlines during both of my first two parings, along with the persistent awareness of a launch deadline in the last project that I worked on, really underlined for me the importance of giving good estimates, as well as being able to manage time well individually and as a team.

Over the course of the day, Brian and I rounded out a couple of components involved in getting data from an API endpoint, then using another to update some related data. The first and point was provided by a third-party, while the second was owned by the company itself. At one point, I got a good SRP reminder. We’d started adding HTTP details to an object whose primary responsibility was really tying together other components to effect policy. So we ended up extracting those details into a lower-level object with which the the first object collaborated to do its thing.

I also learned to be a good API client. There are cases where the policy-level object doesn’t end up needing to update anything at all. The way we had originally written it, the object would have made a call to the end point regardless. It was natural to Brian to rewrite things so that it only made a call when it was actually going to do some work as a result. This way, it wouldn’t needlessly expend resources that other clients might need in order to do actual work. This had never crossed my mind, but makes total sense.