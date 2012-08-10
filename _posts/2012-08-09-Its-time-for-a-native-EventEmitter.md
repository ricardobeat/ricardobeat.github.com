---
layout: post
title: It's time for a native EventEmitter
---

{{ page.title }}
================

Every developer working with javascript has used events hundreds of times. More like thousands. Or millions. Events are the base for UI interaction on the web, and with the popularity of Node.JS and evented frameworks for many languages before it, are now a staple in the backend too. If you have a written a decently-sized web site or app in the past years, chances are you've used a custom event emitter or the observer pattern in your code many times.

Event-driven architectures are, in my opinion, the most natural way to deal with async code. They free you from cross-referencing objects and methods all over, allowing real dependency-free modules that don't need to know about other parts of the application, like they should. As soon as your code starts to grow and you have to handle UI events, XHR requests, dynamic asset loading, you either
<pre style="font-family:sans-serif; font-size:14px; margin-top:-1em;">
            enter
                callback
                     hell,
                         make an entangled chain of method references, or do the sane thing: resort to events.
</pre>

The proof is in the massive number of event emitter implementations; Backbone has its own, as does Spine, jQuery (it can handle object events too) and most other client frameworks/libraries:

(links point to code)

* [AngularJS](https://github.com/angular/angular.js/blob/master/src/ng/rootScope.js)
* [SpineJS](https://github.com/maccman/spine/blob/master/src/spine.coffee)
* [Backbone](https://github.com/documentcloud/backbone/blob/master/backbone.js#L61)
* [jQuery](https://github.com/jquery/jquery/blob/master/src/event.js)
* [BatmanJS](https://github.com/Shopify/batman/blob/master/src/event_emitter/event_emitter.coffee)
* [Zepto](https://github.com/madrobby/zepto/blob/master/src/event.js)
* [ScaleApp](https://github.com/flosse/scaleApp/blob/master/src/Mediator.coffee)
* [Stapes](https://github.com/hay/stapes/blob/master/stapes.js#L186)
* [Events-js](https://github.com/kbjr/Events.js/blob/master/events.js#L850)
* [Bean/Ender's Jeesh](https://github.com/fat/bean/blob/master/src/bean.js#L378)

And of course, a million stand-alone implementations:

* [EventEmitter](https://github.com/Wolfy87/EventEmitter/blob/master/src/EventEmitter.js)
* [EventEmitter2](https://github.com/hij1nx/EventEmitter2/blob/master/lib/eventemitter2.js)
* [MinPubSub](https://github.com/daniellmb/MinPubSub/blob/master/minpubsub.src.js)
* [ShotGun](https://github.com/jgnewman/shotgun/blob/master/shotgun.js)
* [pubsub.js](https://github.com/federico-lox/pubsub.js/blob/master/src/pubsub.js)
* [observer](https://github.com/azer/observer/blob/master/lib/observer.js)
* [snackJS](https://github.com/rpflorence/snack/blob/master/src/event.js)
* [jvent](https://github.com/pazguille/jvent/blob/master/jvent.js)
* [callbacks.js](https://github.com/dperrymorrow/callbacks.js/blob/master/callbacks.coffee)
* [SignalJS](https://github.com/millermedeiros/js-signals/blob/master/dev/src/Signal.js)
* [asEvented](https://github.com/mkuklis/asEvented/blob/master/asevented.js)
* [ekho.js](https://bitbucket.org/killdream/ekho/src/ebfc23d7ed02/src/ekho.js)
* [microevent.js](https://github.com/jeromeetienne/microevent.js/blob/master/microevent.js)
* [radio.js](https://github.com/uxder/Radio/blob/master/radio.js)

Each of these has a slightly different opinion on how things should work, but EventEmitter2 is arguably the canonical implementation - it's the most complete, battle-hardened and the base for node.js's EventEmitter.

In the end, they all implement a simple three-method API: `addListener`, `removeListener` and `trigger`. In fact, most of them are functionally equivalent and just differ on method naming: *bind|add|listen|on*, *unbind|remove|off* and *trigger|fire|dispatch|emit*.

Going on a tangent, I have to say I'm not very fond of the *on/off* naming convention. "on" was used as a preposition in the original DOM events like `onclick`, not as an adverb (being "on/off"). That's annoying.

Anyway, let's end this mess and have `EventEmitter` as a native object. What do you think?

<iframe src="http://thatsjustlikeyouropinion.info/on/nativeEventEmitter/Agreed/You're stupid" frameborder="0" width="190" height="30" style="vertical-align:bottom;"></iframe>