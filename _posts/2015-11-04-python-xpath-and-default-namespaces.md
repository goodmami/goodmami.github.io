---
layout: post
title: Python's XPath and Default Namespaces
---

Python's [ElementTree][] is a great interface for XML documents, but it
has terrible support for namespaces; you basically have to [do it
yourself](http://effbot.org/zone/element-namespaces.htm). ElementTree's
[XPath][] support (a subset called [ElementPath][], although that name
is not used publicly) will take a `namespaces` argument with mapping of
prefixes to URIs, but it does not handle default namespaces.

I had to do some digging, but apparently the XPath designers [didn't
consider default
namespaces](https://lists.w3.org/Archives/Public/www-dom/2002JulSep/0159.html)
when creating the API. It is referenced in the specification, however.
From the section on [node tests][] (emphasis mine):

> A [QName][] in the node test is expanded into an [expanded-name][]
> using the namespace declarations from the expression context. This is
> the same way expansion is done for element type names in start and
> end-tags except that **the default namespace declared with xmlns is
> not used: if the [QName][] does not have a prefix, then the namespace
> URI is null (this is the same way attribute names are expanded).** It
> is an error if the QName has a prefix for which there is no namespace
> declaration in the expression context.

It is again referenced (contradictorily?) in the section on [Element
Nodes][] (again, emphasis mine):

> There is an element node for every element in the document. An
> element node has an [expanded-name][] computed by expanding the
> [QName][] of the element specified in the tag in accordance with the
> XML Namespaces Recommendation [[XML Names][]]. **The namespace URI
> of the element's [expanded-name][] will be null if the [QName][] has
> no prefix and there is no applicable default namespace.**

Now back to Python, the design of [ElementPath][] chooses to [follow
the first quote from the
spec](https://mail.python.org/pipermail/xml-sig/2002-April/007558.html),
even though the qualified names of tags and attributes are strings with
the URIs embedded. Recent versions of Python allow functions like
[find()](https://docs.python.org/3/library/xml.etree.elementtree.html#xml.etree.ElementTree.Element.find)
to take a `namespaces` argument, and a given mapping will find the
qualified names when a prefix is given, but if a default namespace is
in effect, you'd need to create the qualified name manually, or do
something destructive like remove the default namespaces from all
elements. That is, for an XML document like this:

{% highlight xml %}
<root xmlns="http://example.com" xmlns:pre="http://example.com/prefix">
  <a>1</a>
  <pre:b>2</pre:b>
</root>
{% endhighlight %}


You could not find `<a>` (or even `<root>`) with a simple path like this:

{% gist 5ea94726785bcffc3db7 example.py %}

As far as I know, there is not a good solution to this problem. Even
[lxml][] is aware of the problem but [suggests modifying the document
with a bogus prefix](http://lxml.de/FAQ.html#bugs). The design of
[ElementPath][] seems to selectively choose which parts of the
[XPath][] spec to implement (when considered in conjunction with the
model that [ElementTree][] provides). In its defense, the XPath spec
itself is lacking in some ways. Nevertheless, a more *useful* (if
perhaps technically *incorrect*) implementation in the Python standard
library would replace the `xpath_tokenizer()` function shown here:

{% gist 5ea94726785bcffc3db7 ElementPath-xpath_tokenizer-original.py %}

with this:

{% gist 5ea94726785bcffc3db7 ElementPath-xpath_tokenizer-updated.py %}

[ElementTree]: https://docs.python.org/3/library/xml.etree.elementtree.html
[Xpath]: http://www.w3.org/TR/xpath/
[ElementPath]: https://docs.python.org/3/library/xml.etree.elementtree.html#elementtree-xpath
[node tests]: http://www.w3.org/TR/xpath/#node-tests
[Element Nodes]: http://www.w3.org/TR/xpath/#element-nodes
[QName]: http://www.w3.org/TR/REC-xml-names#NT-QName
[expanded-name]: http://www.w3.org/TR/xpath/#dt-expanded-name
[Xml Names]: http://www.w3.org/TR/xpath/#XMLNAMES
[lxml]: http://lxml.de/
