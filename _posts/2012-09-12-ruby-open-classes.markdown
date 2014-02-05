---
layout: post
title: "Ruby open classes"
tags: [ "meta programming", "ruby" ]
---

Sometimes is very useful to add methods to ruby classes that are not on your code base.

Let's say you have an utility class with an utility methods called *sub\_comma\_by\_space* that substitute all commas on a String by spaces:

{% highlight ruby linenos %}
def self.sub_comma_by_space(str)
  str.sub ",", " "
end
{% endhighlight %}

This could be more object oriented if we can use this method like this:

{% highlight ruby linenos %}
"Test,string".sub_comma_by_space
{% endhighlight %}

In Java, for example, you would have to create a new "PowerString" with all String methods and add this method to your "PowerString" and then change all your references to use your new "PowerString" instead of String class.

Ruby has the concept of open classes so you can easily extend an existing ruby class, without changing the String source file:

{% highlight ruby linenos %}
class String
  def sub_comma_by_space
    sub ",", " "
  end
end
{% endhighlight %}

Now you can call *sub\_comma\_by\_space* method on all you Strings.
