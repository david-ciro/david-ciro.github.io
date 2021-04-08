---
layout: post
title:  "Set/Get methods vs Set/Get by name"
date:   2021-04-07 21:46:44 -0300
categories: jekyll update
---

# Introduction
As mentioned in a [previous post]({% post_url 2021-03-31-encapsulation-in-c %}), encapsulation requires the usage of custom methods to set and get data to and from a container object or handler. Depending on the number of parameters stored in the object, the header file will be mostly populated by setters and getters with meaningful names to reduce unnecessary comments. However, there is an interesting possibility in which all the parameters with the same type can be setted/getted with a single pair of methods and a list of meaningful identifiers, for instance:

{% highlight C %}
// particle.h
double get_param(object obj, object_param param_name);
{% endhighlight %}

where `object` is the handler name and `object_param` is an enumerate type containing a list of names that identify the accessible parameters in object. 