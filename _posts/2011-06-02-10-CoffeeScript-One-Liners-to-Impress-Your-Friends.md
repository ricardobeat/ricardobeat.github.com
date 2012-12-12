---
layout: post
title: 10 CoffeeScript One Liners to Impress Your Friends
---

{{ page.title }}
================

You may have read ["10 Scala One Liners to Impress Your Friends"](http://mkaz.com/solog/10-scala-one-liners-to-impress-your-friends) at [Marcus Kazmierczak's blog](http://solog.co) recently featured on [HN](http://news.ycombinator.com). Although I don't know Scala (or Java), it all looks quite nice, so I decided to impress my friends too - folks go from Java to Scala, we go from Javascript to CoffeeScript. Assume [node.js](http://nodejs.org) as the environment for all examples.

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

Javascript (and CoffeeScript by extension) also has native [map](http://en.wikipedia.org/wiki/Map_%28higher-order_function%29) and  [reduce](http://en.wikipedia.org/wiki/Fold_%28higher-order_function%29) functions:

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
[1..4].map (i) -> console.log "Happy Birthday " + (if i is 3 then "dear Robert" else "to You")
{% endhighlight %}

But it get's better. This reads almost like pseudo-code:

{% highlight coffeescript %}
console.log "Happy Birthday #{if i is 3 then "dear Robert" else "to You"}" for i in [1..4]
{% endhighlight %}

6. Filter list of numbers
-------------------------

Filter a list of numbers into two categories. The literate way:

{% highlight coffeescript %}
(if score > 60 then (passed or passed = []) else (failed or failed = [])).push score for score in [49, 58, 76, 82, 88, 90]
{% endhighlight %}

(thanks [@giacecco](http://twitter.com/giacecco) for shortening this)

And the functional way:

{% highlight coffeescript %}
[passed, failed] = [49, 58, 76, 82, 88, 90].reduce ((p,c,i) -> p[+(c < 60)].push c; p), [[],[]]
{% endhighlight %}

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

**update (june/05)**: [@dionyziz](http://twitter.com/dionyziz) sent me this compact version:

{% highlight coffeescript %}
primes = []
primes.push i for i in [2..100] when not (j for j in primes when i % j == 0).length
{% endhighlight %}

which we can then use for a real one-liner like the original:

{% highlight coffeescript %}
(n) -> (p.push i for i in [2..n] when not (j for j in (p or p=[]) when i%j == 0)[0]) and n in p
{% endhighlight %}

or the somewhat more efficient

{% highlight coffeescript %}
(n) -> (p.push i for i in [2..n] when !(p or p=[]).some((j) -> i%j is 0)) and n in p
{% endhighlight %}

11. Bonus
---------

Most readable fizzbuzz version you'll ever see:

{% highlight coffeescript %}
"#{if i%3 is 0 then 'fizz' else ''}#{if i%5 is 0 then 'buzz' else ''}" or i for i in [1..100]
{% endhighlight %}

**edit:** even simpler, but trickier, with [a little hint by satyr](https://github.com/jashkenas/coffee-script/issues/1406#issuecomment-1293309):

{% highlight coffeescript %}
['fizz' unless i%3] + ['buzz' unless i%5] or i for i in [1..100]
{% endhighlight %}

When you use the `+` operator on an Array, it converts it to a string. `[].toString()` is the same as `[].join(',')`, which gives an empty string in case the array value is `undefined` or `null`. This also works in Javascript (`[undefined] + "b" === "b"`).

Conclusions
-----------

Modern languages are amazingly expressive. I'm also surprised that some of the syntax in these map so closely to Scala, given they're oceans apart.

You can [learn more about CoffeeScript here](http://jashkenas.github.com/coffee-script/), see a few more CoffeeScript snippets on [rosettacode](http://rosettacode.org/wiki/Category:CoffeeScript), and follow me on Twitter [@ricardobeat](http://twitter.com/ricardobeat).