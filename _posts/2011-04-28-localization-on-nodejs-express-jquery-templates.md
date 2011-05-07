---
layout: post
title: Localization on node.js + express + jquery templates
---

{{ page.title }}
================

I just published `jqtpl-express-i18n`, a module for translations using [jquery templates](https://github.com/kof/node-jqtpl) and [express](http://expressjs.com). It takes a string from the .html template and looks for a suitable translation according to the received `Accept-Language` header.

My first idea was to add a getter called .i18n to `String.prototype` using __defineGetter__:

{% highlight coffeescript %}
String.prototype.__defineGetter__ 'i18n', () ->
    return strings['pt']?[this] || this
{% endhighlight %}

And this in the templates:

{% highlight html %}
<p>${"Look ma I'm being translated".i18n}</p>
{% endhighlight %}

*Magic.*

This didn't work though, because you can't access the request's scope from the getter. I ended up extending jQuery templates after following [this example](https://gist.github.com/726057), and it turned out to be much cleaner. Now my templates look like this:

{% highlight html %}
<ul>
	<li>{[e "Something"}}</li>
	<li>{[e "Another something"}}</li>
</ul>
<input type="submit" value="{[e 'submit'}}" />
{% endhighlight %}

(ignore the bracket, [markdown](http://daringfireball.net/projects/markdown/) can't handle the double braces)

I wish I could just use `{["text here"}}` - but I'd have to break into node-jqtpl's house and mess with it's privates, that wouldn't be nice. Right?

I'm currently rewriting it to use .json files and update strings automagically, hope to get v0.2 up soon. You can get it from npm or [github](https://github.com/ricardobeat/jqtpl-express-i18n). Questions or issues go to [https://github.com/ricardobeat/jqtpl-express-i18n](https://github.com/ricardobeat/jqtpl-express-i18n).