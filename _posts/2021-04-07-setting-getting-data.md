---
layout: post
title:  "Set/Get methods vs Set/Get by name"
date:   2021-04-07 21:46:44 -0300
categories: jekyll update
---

# Introduction
As mentioned in a [previous post]({% post_url 2021-03-31-encapsulation-in-c %}), encapsulation requires the usage of custom methods to set and get data to and from a container object or handler. Depending on the number of parameters stored in the object, the header file will be mostly populated by setters and getters with meaningful names to reduce unnecessary comments. However, there is an interesting possibility in which all the parameters with the same type can be setted/getted with a single pair of methods and a list of meaningful identifiers, for instance:

{% highlight C %}
// object.h
double get_param(object obj, object_param param_name);
{% endhighlight %}

where `object` is the handler name and `object_param` is an enumerate type containing a list of names that identify the readable and editable parameters of the object.

# Example: Rigid body
To better illustrate this point, consider the dynamical properties of a rigid body, i.e. its mass, inertia tensor, angular velocities about its main axis, location and velocity of the center of mass, etc. The mentioned properties add up to 16 parameters due to the symmetries of the tensor. To simplify iterative calculations it is convenient to separate the setters and getters in 5 groups with the corresponding arguments for the components, for instance:

{% highlight C %}
// rigid_body.h
void
rgb_set_Iij(rigid_body rgb, size_t i, size_t j, double Iij);

double
rgb_get_Iij(rigid_body rgb, size_t i, size_t j);
{% endhighlight C %}

can be used to set and get the individual components of the inertia, and analogously for the other physical properties.