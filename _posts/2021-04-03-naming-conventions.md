---
layout: post
title:  "Naming conventions"
date:   2021-04-03 21:46:44 -0300
categories: jekyll update
---

When we combine a large number of object files `.o` during compilation, it is required that all the methods, constants and variables have unique names. This can be difficult to ensure if we do not follow a well established naming routine.

For instance, if we have two modules `particle` and `rigid_body`, their header files will probably contain prototypes of methods to set and get their masses, something like `double get_mass(particle p)` for the first and `double get_mass(rigid_body)` for the second. This creates a problem of [name collision](https://en.wikipedia.org/wiki/Name_collision) when we need to combine their object files in a given program. Because of this, it is a good practice to define a short prefix for all the methods in a given module, for instance, methods in the `particle` module can be prefixed with `pcl_`, while those in the rigid body module can be prefixed with `rgb_`, solving then naming problem.

An interesting consequence of this naming is the emergence of an object-action pattern in the names of the methods. For instance, the rigid body handle, its creator and the mass setter can be declared as:

{% highlight C %}
// rigid_body.h
typedef struct rigid_body_t* rigid_body;

rigid_body
rgb_create();

double
rgb_set_mass(rigid_body rgb, double m);
{% endhighlight C %}

where the object was named using the prefix, leading to a very expressive syntax with little space for ambiguities in a program, for intance:

{% highlight C %}
// main.c
# include "rigid_body.h"
int main()
{
    // create rgb
    rigid_body rgb1 = rgb_create();
    rigid_body rgb2 = rgb_create();
    // edit bodies
    rgb_set_mass(rgb1, 2.0);
    rgb_set_mass(rgb2, 1.0);
    // other operations
    // ...
    // destroy bodies
    rgb_destroy(rgb1);
    rgb_destroy(rgb2);
    return 0;
}
{% endhighlight C %}