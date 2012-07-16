---
layout: page
title: Testing GitHub Pages
---

A little **brown** fox.


* An item in a bulleted (unordered) list
    * A subitem, indented with 4 spaces (does not work)
* Another item in a bulleted list

1. one
2. two

{% highlight ruby %}
require 'redcarpet'
markdown = Redcarpet.new("Hello World!")
puts markdown.to_html
{% endhighlight %}

{% highlight python %}
def python_code():
    print "hello"
{% endhighlight %}


