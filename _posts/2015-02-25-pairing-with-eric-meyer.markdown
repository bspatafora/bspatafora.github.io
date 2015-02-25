---
layout: post
title:  "Pairing with Eric Meyer"
date:   2015-02-25
---

I paired with Eric Meyer the last two days. Users of the app he’s working on need to be able to view a list of people and update some of their data.

He’d already put a number of pieces of the functionality together, so we worked on putting the remaining pieces in place and making sure everything was wired together correctly. The language (CoffeeScript) and way of getting and updating data (via API calls to services maintained elsewhere in the company) were not the most familiar to me, but the general approach was. The two days were (with the exception of some minor spiking) completely test-driven and aimed at not only implementing the feature, but also on keeping the code readable and structuring it to limit dependencies to the minimum needed.

The thing that struck me most over the two days was how incremental Eric’s approach is. This apparent in his adherence to red-green-refactor, but was most obvious during the numerous refactors. Rather than try to execute a refactor in one fell swoop, he does it in multiple passes, changing only one thing in each pass, and trying to stay green at each step. While I try to follow this approach myself, it was really cool to see a craftsman using it, and to see the particular way he decided to break things down into discrete steps.