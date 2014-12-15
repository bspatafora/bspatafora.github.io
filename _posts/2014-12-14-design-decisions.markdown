---
layout: post
title:  "Design decisions"
date:   2014-12-14
---

In the past week, I’ve experienced two very different approaches to making design decisions.

On Wednesday, I attended a Ruby hack night where people were pairing up to build up a basic banking application. I spent most of the night working with a person with a programming background, but little to no experience with test-driven development or the design awareness it fosters.

Throughout the session, there was a notable contrast between his wanting to just move and start implementing and keep implementing, and my overly meticulous approach. I kept pausing to ask questions about the design choices we seemed to be making (or ignoring). For example, I thought it was worth taking some time to consider whether an `Account` should know its owner, a `Person` should keep track of their accounts, or if some third entity might be needed to link the two. My pair seemed amused that I would spend the time on such a seemingly trivial matter.

The opposite extreme happened during my pairing with another apprentice throughout the week. While I think we struck a pretty good balance on the whole, there were times where we got sort of bogged down in discussion about the costs and benefits of a particular approach to solving a problem.

One discussion in particular was whether it would be better to leave a conditional in the code that was in effect checking the type of object being dealt with in order to decide what to do (something we both recognized was a design smell), or instead include in a domain class some presentational logic specific to an external service that we didn’t want domain entities knowing anything about.

If I had to choose between the two approaches, I’d pick the more methodical approach. Probably this has something to do with my personality, but it seems to me that much time can be saved in the long run by spending a little more time up front thinking about how you ought to do something. But I also recognize that there’s a fine line between thinking ahead and just spinning your wheels. In the case of the discussion between the other apprentice and me, there came a point where we realized we just needed to start writing code or we’d just continue to talk endlessly.

There wasn’t any great revelation I drew from these episodes, but I do feel like they provided me with some good information and experience. I get the sense I’m not alone in feeling that it can sometimes be very difficult to make design decisions. I can get paralyzed with indecision, seeing options but not being able to say with enough certainty which among them is the best. Other times I feel the urge to just attack a problem with little regard for the future implications.

I’m continually trying to get better at paying close attention when I’m feeling pain in the tests or the code I’m writing, and taking that as a cue to reevaluate the design and possibly re-implement it to alleviate the pain points. It seems to me this is a skill that to be honed over time.