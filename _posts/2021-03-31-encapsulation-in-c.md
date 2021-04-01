---
layout: post
title:  "Encapsulation in C"
date:   2021-03-31 20:42:44 -0300
categories: jekyll update
---

There are many situations in which it is not convenient for the user to access the internal state of some object, and, in general, that is the case for most APIs. Consider for instance a type structure that holds the state of a particle:

{% highlight C %}
// particle.h
typedef struct {
  double m; // mass
  double* x; // position
  double* v; // velocity
  double t; // time
} particle;
{% endhighlight %}

Since users generally have access to the header files (.h), it is possible for a user to access and modify the state of the particle by simply using the postfix operators `(.)` or `(->)`. This not fundamentally wrong, but it is preferable to define a set of access methods that allow us to easily spot when state changes take place to ease debugging and lead to a more readable code. For instance one can define mass *setters and getters* to access and edit the mass.

{% highlight C %}
// particle.h
void set_mass (particle* p, double m);
double get_mass (particle* p);
{% endhighlight %}

with the following implementation

{% highlight C %}
// particle.c
void set_mass (particle* p, double m){
  p->m = m;
}
double get_mass (particle* p){
  return p->m;
}
{% endhighlight %}