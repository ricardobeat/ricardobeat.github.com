---
layout: post
title: React Tutorial rewritten in plain javascript
---

{{ page.title }}
================

Last week Facebook & Instagram launched the [React]() framework and an accompanying [tutorial](http://facebook.github.io/react/docs/tutorial.html).

Developer Vlad Yazhbin decided to [rewrite that using AngularJS](https://medium.com/make-your-own-apps/e71bcedc36b). The end result is pretty neat, but if you're like me you will not actually appreciate the HTML speaking for itself and doing all the hard work. So let's see what that looks like in plain javascript. Here is the template:

{% highlight html %}
{% raw %}
<div class="comment-box">
    <h1>Comments</h1>
    <div class="comment-list">
        <div class="comment">
            <h2 class="comment-author">
                {{author}}
            </h2>
            <div class="comment-text">{{text}}</div>
        </div>
    </div>
    <form class="comment-form" method="POST" action=".">
        <input type="text" name="author" id="author" required />
        <input type="text" name="text" id="text" required />
        <button type="submit">Add comment</button>
    </form>
</div>
{% endraw %}
{% endhighlight %}

And this is the code rewritten in *VanillaJS*. No dependencies, no DOM libraries, no template engines (kinda).  It renders the comments, markdown (using [marked](https://github.com/chjj/marked)), and handles new comments:

{% highlight javascript %}

function CommentBox (root, data) {
    this.root = document.querySelector(root)
    this.list = this.root.querySelector('.comment-list')
    this.form = this.root.querySelector('.comment-form')
    this.data = data || []
    this.bindEvents()
}

CommentBox.template = document.querySelector('.comment').innerHTML

function nanostache (str, data) {
    return str.replace(/\{\{(\w+)\}\}/g, function(a, m){ return data[m.trim()] || '' })
}

CommentBox.prototype = {
    add: function (comment) {
        this.data.push(comment)
        return this
    },
    render: function () {
        this.list.innerHTML = this.data.map(function(comment){
            comment.text = marked(comment.text)
            return nanostache(CommentBox.template, comment)
        }).join('\n')
    },
    bindEvents: function () {
        var self = this, form = this.form
        form.addEventListener('submit', function(e){
            e.preventDefault()
            self.add({
                author: form.author.value
              , text: form.text.value
            }).render()
        }, false)
    }
}
{% endhighlight %}

(In a real project you'd probably want to create separate `Comment` and `CommentList` models/views, use a real template engine or DOM manipulation, and put the templates in a &lt;script&gt; tag, but this works.)

It's nearly the same number of lines as the Angular example, despite some extra work you'd probably delegate to DOM, events and templating libraries.

## Automatic updates

The Angular demo finishes with using a `post` to save the comment on the server, and a 5s polling interval for live updates, as does the original one. You can do that, but this is the future, let's use websockets!

First create a connection:

{% highlight javascript %}
var ws = new WebSocket('ws://' + location.hostname)
{% endhighlight %}

Then send data to the server when adding a new comment:

{% highlight javascript %}
add: function (comment) {
    this.data.push(comment)
    ws.send('newcomment::' + JSON.stringify(comment))
}
{% endhighlight %}

And change the initialization code to fetch data from the server (it should send existing comments on connection), and listen for new items:

{% highlight javascript %}
var comments = new CommentBox('.comment-box')

ws.onmessage(function(e){
    // or use socket.io/sockjs
    comments.add(JSON.parse(e.data))
})
{% endhighlight %}

There you go. A real-time, *reactive* comment form with real-time updates *without any library whatsoever*.

## Conclusions

What does it mean?

I don't really know. Maybe the point is, there is no point in comparing frameworks with small, synthetic examples.

Code like this, using plain javascript, can get hairy pretty fast. It will usually give birth to encapsulated common idioms and functionality, and will end up becoming it's own little framework. Sure, it's easier to start with because there is no learning curve, but if in the end it will need a considerable amount of knowledge to play with, how is it different from using a framework like Angular/Ember/React/Knockout from the start?

My opinion: when in doubt, use [Backbone](http://backbonejs.org). It's small, focused and not much different from whatever flavor of MV* you were going to cook up by yourself, with the advantage of a huge community of developers, time-tested code and active development.