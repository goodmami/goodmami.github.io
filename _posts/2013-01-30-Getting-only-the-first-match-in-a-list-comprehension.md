---
layout: post
title: 'Python one-liner: Getting only the first match in a list comprehension'
redirect_from:
 - /2013/01/python-one-liner-getting-only-the-first-match-in-a-list-comprehension/
---

Using `next()` with a generator expression to simulate a `break`
statement in a for loop. Something like this:

{% highlight python %}
val = next((x for x in some_list if match(x)), default_val)
{% endhighlight %}

Which is the same as:

{% highlight python %}
val = default_val
for x in some_list:
    if match(x):
        val = x
        break
{% endhighlight %}

Commenter Emanuel Hoogeveen suggested the `default_val`, because
otherwise the `next()` would raise a `StopIteration` if there were no
matches in the list. He found the answer [here on StackOverflow][].


[here on StackOverflow]: http://stackoverflow.com/questions/7102050/how-can-i-get-a-python-generator-to-return-none-rather-than-stopiteration
