---
layout: post
title:  "Design discussions"
date:   2015-02-08
---

This past Friday, I asked a Craftsman if I could pair with him on the personal project he was working on during Waza. He said yes, so we spent about two hours working on it together.

I felt a little bad by the end, because in those couple hours we only wrote one test and the code to make it pass. There are a couple reasons that we moved so slowly. First foremost was that this was my first time looking at the project. My partner was good enough to take the time to walk me through the existing parts of the system so I had a sense of the big picture, and not just whatever subsection we decided to work on. I felt very appreciative (and a little guilty) that he was willing to take this time, which he could have instead used to do actual work on his project. Rather than feeling too guilty about it, however, I plan to continue working on the project with him. Next time I’ll be able to jump right in.

There was another reason that we got so little “real” work done that afternoon. While it didn’t take very long long to identify the feature we wanted to work on, we spent a substantial amount of time talking about where to start, how to design things, and what techniques to use along the way.

The project is a fundraising charity, and so it tracks donations with a domain object. When a user donates, a certain number of “thoughts,” posted by other users and originally hidden from view, should be “unlocked” and made visible. This is the feature we had decided to work on. We took some time to discuss how this might be implemented, and decided to start by making it possible (in some way) for the system to know which donation to associate with an unlocked thought, information needed in the case that there are donations that haven’t been “used” present when a new thought comes in.

We had the idea of adding a field to the donation object to know how much of the payment had already been used to unlock thoughts, but after discussing it decided that a new object was warranted. The existing donation object already had a well-defined responsibility: it stores data relating to the payment, made through an external payment-processing service. We realized that if the external service were to change, it would be best if as few parts of the system as possible knew about the change. Adding a related but separable responsibility would only increase the chances of such a switch being more complicated than necessary.

I don’t know what the right amount of time to spend discussing such things is. On the one hand, you could use that time actually writing tests and code to add the new feature. But on the other hand, it seems like these discussions can pay dividends. As I’ve gotten more experience working on existing projects, I’ve become more and more aware of the effects bad design choices can have, especially when made early in the life of the project and not changed when changing them would still be relatively cheap to do. So I’m hoping the extra time thinking things through on Friday will pay off in the form of a simpler and more maintainable system down the line.