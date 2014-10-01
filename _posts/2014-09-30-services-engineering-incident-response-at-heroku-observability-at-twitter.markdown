---
layout: post
title:  "Services Engineering: Incident Response at Heroku/Observability at Twitter"
date:   2014-09-30
---

## [Incident Response at Heroku][]
  * In order to make its service as reliable as possible, Heroku has developed a formal framework for responding when things go wrong.
  * Its system is based in part on the [Incident Command System][], which was originally created to coordinate the putting out of literal fires.
  * Key attributes:
      * **Centralized information**: This seems to be the most important aspect of Heroku’s incident response framework. A response to a problem with a complex of services as numerous and individually complex as Heroku’s will almost certainly involve a team of people in different locations and with different (and possibly conflicting) information on the state of the system at the time of the incident. To get a coherent, effective response out of an initially fragmented team, it’s critical to get everyone on the same page. Heroku does this with a central chat room, high-bandwidth video calls, and, perhaps most importantly, situation reports that are regularly sent out by a single person. These reports communicate the assessment of the problem and details of the response as they emerge.
      * **Centralized management**: One person (the “incident commander,” or “IC”) is put in charge of coordinating the response, which includes disseminating information and getting the right people involved in the response. Limiting this role to a single person prevents the response from being fragmented by conflicting orders or competing goals. While others are presumably welcome to suggest (or even insist on) a certain path, they must do so to the IC, and that person then decides which path to take and makes sure that the relevant responders are active and that everyone is clear on their role. The incident commander is accountable for either the quality of the response.
      * **Communication to customers**: While the highest priority is restoring service, it sounds like Heroku makes it a priority to tell their customers what they think the problem is and to assure them that they are working to fix it. Presumably they emphasize this because it prevents their customers from experiencing the additional frustration of feeling left in the dark about what’s going on/when it will be over. Heroku’s admin section makes it easy to get this information out to customers in multiple ways (e.g., their status page, text messages).

## [Observability at Twitter][]
  * In order to diagnose problems arising from the complex of services that make up Twitter, they need a system for analyzing not only the health and performance of each component, but also how each component is affected by the health and performance of others.
  * The system saves historical data for after-the-fact analysis or searching for trends. Since there’s so much data moving through the system at all times, “high resolution” data is removed after a certain amount of time, leaving lower resolution information for longer term archiving.
      * Twitter’s observability team has emphasized the consistency of stored data, as well as a single query language, to enable aggregating information from various sources for analysis.
  * It also makes metrics available as often as once per second, which is useful for getting a look inside a particular component when the system as a whole is failing.
  * The same query language used to access stored data can be used to set notification and alert conditions, which seems crazy useful.

[Incident Response at Heroku]: https://blog.heroku.com/archives/2014/5/9/incident-response-at-heroku
[Incident Command System]: http://en.wikipedia.org/wiki/Incident_Command_System
[Observability at Twitter]: https://blog.twitter.com/2013/observability-at-twitter