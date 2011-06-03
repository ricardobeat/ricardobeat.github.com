---
layout: post
title: What should I listen to?
---

{{ page.title }}
================

> Do you frequently open Grooveshark/Pandora/iTunes/etc and just stare at it while trying to decide what to listen next?

I do it all the time. I probably visit my own [last.fm profile](http://last.fm/user/superbife) a dozen times a week, just so I can take a look at my library and pick a familiar name to put into the search field (yeah, I dumped Last.FM radio for Grooveshark - I like to hear whole albums).

Enter [What should I listen to?](http://listen.to.ricardo.cc/), _the dumbest web service ever_. It takes a last.fm username and a click, and gives back lots of love in the form of good music recommendations (actually these recommendations come from yourself, but you gotta give it some credit...)

I had a lot of fun building it:

1. runs on node.js, courtesy of [nodester](http://nodester.com)
2. IDs are saved in localStorage, and your library's size in your cookies (we need to know the range to shoot at), so no database.
3. It has no images *per se*, the noisy background is generated via <del>Javascript</del> CoffeeScript and the rest is CSS.

It's not mobile-ready, though that should be easy to accomplish. Source code is [on github](http://github.com/ricardobeat/what-should-i-listen-to).

I hope it's useful for a few people out there. If you have any suggestions you can find me [on Twitter](http://twitter.com/ricardobeat).
