---
layout: post
title: Localization on node.js + express + jquery templates
---

{{ page.title }}
================

<div class="date"><time datetime="{{ page.date | date_to_xmlschema }}" pubdate>{{ page.date | date_to_string }}</time></div>


I just published `jqtpl-express-i18n`, a module for translations using jquery templates and express. It's quite simple but took me a little time to figure it out.

My first idea was to add a getter called .i18n to `String.prototype`. That's possible using __defineGetter__:

{% highlight coffeescript %}
String.prototype.__defineGetter__ 'i18n', () ->
	return strings['pt']?[this] || this
{% endhighlight %}

It would just check for the existence of a translation and return it. It worked like a charm, using this in the templates:

{% highlight html %}
<p>${"Look ma I'm being translated".i18n}
{% endhighlight %}

*Magic.*

The problem was, see that `'pt'` string up there? It's supposed to be the `req.session.lang` property, which is set on a user basis. But it turned out to be unreachable from inside the getter. Even adding `i18n` as function to String.prototype didn't work (`${"string".translate()}`). I have no idea in what scope that thing is executed.

I ended up extending jQuery templates after following [the only example I could find](https://gist.github.com/726057), and it turned out to be much cleaner.

Well, it seems to work, and you can get it from npm or [github](https://github.com/ricardobeat/jqtpl-express-i18n).