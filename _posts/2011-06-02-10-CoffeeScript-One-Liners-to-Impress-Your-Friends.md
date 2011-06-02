---
layout: post
title: 10 CoffeeScript One Liners to Impress Your Friends
---

{{ page.title }}
================

You may have read ["10 Scala One Liners to Impress Your Friends"](http://solog.co/47/10-scala-one-liners-to-impress-your-friends/) at [Marcus Kazmierczak's blog](http://solog.co) recently featured on [HN](http://news.ycombinator.com). Although I don't know Scala (or Java), it all looks quite nice, so I decided to impress my friends too - folks go from Java to Scala, we go from Javascript to CoffeeScript. Assume [node.js](http://nodejs.org) as the environment for all examples.

1. Multiply each item in a list by 2
------------------------------------

Marcus starts by showing off the `map` function. We can do exactly the same using a _range literal_ and an anonymous function:

{% highlight coffeescript %}
[1..10].map (i) -> i*2
{% endhighlight %}

but we can also write the more expressive form

{% highlight coffeescript %}
i * 2 for i in [1..10]
{% endhighlight %}

2. Sum a list of numbers
------------------------

Javascript (and CoffeeScript by turn) has the same native [reduce](http://en.wikipedia.org/wiki/Fold_%28higher-order_function%29) function:

{% highlight coffeescript %}
[1..1000].reduce (t, s) -> t + s
{% endhighlight %}

(`reduce` == `reduceLeft`, `reduceRight` is also available)

3. Verify if word exists in string
-------------------------------

Too easy since we have the `some` method. It returns true if any of the elements in the array satisfies the function:

{% highlight coffeescript %}
wordList = ["coffeescript", "eko", "play framework", "and stuff", "falsy"]
tweet = "This is an example tweet talking about javascript and stuff."

wordList.some (word) -> ~tweet.indexOf word
{% endhighlight %}

This will return the matched words instead:

{% highlight coffeescript %}
wordList.filter (word) -> ~tweet.indexOf word
{% endhighlight %}

`~` is not a special operator in CoffeeScript, just a dirty trick. It is the [bitwise NOT](https://developer.mozilla.org/en/JavaScript/Reference/Operators/Bitwise_Operators) operator, which inverts the bits of it's operand. In practice it equates to `-x-1`. Here it works on the basis that we want to check for an index greater than `-1`, and `-(-1)-1 == 0` evaluates to false.

4. Read in a File
-----------------

Users of client-side javascript frameworks will be familiar with this idea:

{% highlight coffeescript %}
fs.readFile 'data.txt', (err, data) -> fileText = data
{% endhighlight %}

You could also use the synchronous version:

{% highlight coffeescript %}
fileText = fs.readFileSync('data.txt').toString()
{% endhighlight %}

In node.js land this is **only acceptable for application start-up** routines. You should use the async version in your code.

5. Happy Birthday
-----------------

First, a 1 to 1 mapping to the Scala version with a bit of string interpolation thrown in the mix:

{% highlight coffeescript %}
[1..4].map (i) -> console.log "Happy Birthday #{if i is 3 then "dear Robert" else "to You"}"
{% endhighlight %}

But we can do better. This is **not** pseudo-code:

{% highlight coffeescript %}
console.log "Happy Birthday #{if i is 3 then "dear Robert" else "to You"}" for i in [1..4]
{% endhighlight %}

6. Filter list of numbers
-------------------------

Filter a list of numbers into two categories. This is close enough:

{% highlight coffeescript %}
passed = []
failed = []
(if score > 60 then passed else failed).push score for score in [49, 58, 76, 82, 88, 90]
{% endhighlight %}

(could also use `filter` but then it wouldn't be a one-liner...)

7. Fetch and Parse a XML web service
------------------------------------

XML what? Haven't heard of it. Let's fetch a JSON instead, using the [request](http://github.com/mikeal/request) library:

{% highlight coffeescript %}
request.get { uri:'path/to/api.json', json: true }, (err, r, body) -> results = body
{% endhighlight %}

8. Find minimum (or maximum) in a List
--------------------------------------

The `apply` function comes handy here. It allows you to call a function passing an array as the list of arguments: `Math.max` and `Math.min` both receive a variable number of arguments, i.e. `Math.max 30, 10, 20` returns `30`. Let's put it to work with an array:

{% highlight coffeescript %}
Math.max.apply @, [14, 35, -7, 46, 98] # 98
Math.min.apply @, [14, 35, -7, 46, 98] # -7
{% endhighlight %}

9. Paralell Processing
----------------------

Not there yet. You can create [child processes](http://nodejs.org/docs/v0.4.8/api/child_processes.html) on your own and communicate with them, or use the [WebWorkers API implementation](https://github.com/pgriess/node-webworker). Skipping over.

10. Sieve of Eratosthenes
-------------------------

Couldn't get this down to one line. Ideas?

{% highlight coffeescript %}
sieve = (num) ->
    numbers = [2..num]
    while ((pos = numbers[0]) * pos) <= num
        delete numbers[i] for n, i in numbers by pos
        numbers.shift()
    numbers.indexOf(num) > -1
{% endhighlight %}


11. Bonus
---------

Shortest still readable fizzbuzz version you'll see:

{% highlight coffeescript %}
"#{('fizz' unless i%3) ? ''}#{('buzz' unless i%5) ? ''}" or i for i in [1..100]
{% endhighlight %}

You can golf your coffee without being cryptic.

Conclusions
-----------

Modern languages rule. I'm surprised that some of the syntax in these map so closely to Scala, given they're oceans apart.

You can [learn more about CoffeeScript here](http://jashkenas.github.com/coffee-script/), see a few more CoffeeScript snippets on [rosettacode](http://rosettacode.org/wiki/Category:CoffeeScript), and find me on Twitter [@ricardobeat](http://twitter.com/ricardobeat).