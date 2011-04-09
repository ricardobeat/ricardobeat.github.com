---
layout: post
title: Frist post
---

{{ page.title }}
================

<div class="date"><time datetime="{{ page.date | date_to_xmlschema }}" pubdate>{{ page.date | date_to_string }}</time></div>

> Here's this post **lead**.

Test post. Here's a `variable`. And some *emphasized* and **bold** text.

Some code:

{% highlight coffeescript %}
app.configure ->
	app.set 'root', __dirname
	app.set 'view engine', 'html'
	app.register '.html', jqtpl

	app.use express.methodOverride()
	app.use express.bodyParser()
	app.use express.cookieParser()
	app.use express.session
{% endhighlight %}
