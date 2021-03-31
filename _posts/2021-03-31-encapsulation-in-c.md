---
layout: post
title:  "Encapsulation in C"
date:   2021-03-31 20:42:44 -0300
categories: jekyll update
---

There are many situations in which it is not convenient for the user of some library or API to access the internal state of some object

{% highlight C %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}