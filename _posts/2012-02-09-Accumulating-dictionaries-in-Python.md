---
layout: post
title: Accumulating Dictionaries in Python
redirect_from:
 - /2012/02/accumulating-dictionaries-in-python/
---

A dictionary subclass that deals with key collisions by accumulating
the values with a custom operator.

{% gist 8679536 %}

Commenter Nathan added:

> For counting things I use the
> [collections.Counter](http://docs.python.org/library/collections.html#collections.Counter)
> class. In addition to giving you a one-liner for obtaining counts from
> a sequence or other iterable, the `most_common()` method makes it easy
> to retrieve the highest-count item(s).

The reminder of the Counter class was useful, since it solves many of
the same problems as AccumulationDict. I then countered (ha!) by noting
that Counter only increments for each key collision, whereas
AccumulationDict can track counts when you already have some frequency
information, or when you want to do more than count things (accumulate
items in a list, for instance).
