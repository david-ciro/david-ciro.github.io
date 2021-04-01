---
layout: post
title:  "Encapsulation in C"
date:   2021-03-31 20:42:44 -0300
categories: jekyll update
---

There are many situations in which it is not convenient for the user to access the internal state of some object, and, in general, that is the case for APIs.

{% highlight C %}
typedef struct {
  double m; // mass
  double* x; // position
  double* v; // velocity
  double t; // time
} particle;
{% endhighlight %}