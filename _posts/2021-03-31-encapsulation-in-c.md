---
layout: post
title:  "Encapsulation in C"
date:   2021-03-31 20:42:44 -0300
categories: jekyll update
---

# Introduction
There are many situations in which it is not convenient for the user to access directly the internal state of some object, and, in general, that is the case for most APIs. Consider for instance a type structure that holds the state of a particle:

{% highlight C %}
// particle.h
typedef struct {
  double m; // mass
  // other parameters
} particle;
{% endhighlight %}

Since users generally have access to the header files (.h), it is possible for a user to access and modify the state of the particle by simply using the postfix operators `(.)` or `(->)`. This is not fundamentally wrong, but it is preferable to define a set of access methods that allows to spot easily when the object state gets changed in the code. In general such practice improves the debugging process and leads to a more readable code. For instance, one can define mass *setters and getters* prototypes to access and edit the mass.

{% highlight C %}
// particle.h
void set_mass (particle* p, double m);
double get_mass (particle* p);
{% endhighlight %}

and the implementation handles the access:

{% highlight C %}
// particle.c
void set_mass (particle* p, double m){
  p->m = m;
}
double get_mass (particle* p){
  return p->m;
}
{% endhighlight %}

This is done in many situations and creates a level of indirection that *persuades* users to avoid direct access to the internal variables. However, this does not preclude this practice, and the user can still use `p->m` to set or get the mass leaving a minimal code footprint that difficults debugging. To actually prevent this practice we will introduce the concept of *incomplete/opaque types* or *dummy pointers* in the context of encapsulation.

# Encapsulation

[Opaque pointers](https://en.wikipedia.org/wiki/Opaque_pointer) are incomplete types that allow us to define a set o methods to perform actions or get data from some unspecified object. For instance, the previous header file can be rewritten as:

{% highlight C %}
// particle.h

typedef struct particle_t* particle;

particle create();

void destroy(particle);

void set_mass (particle p, double m);

double get_mass (particle p);

void print(particle p, FILE* f);

{% endhighlight %}

In this version we have defined a *handle* named `particle` that holds the address of the actual particle type, which is now called `particle_t`. This type of header file is intended to define a clear usage pattern for the particle object, without any distraction caused by the actual implementation of the container type or its methods.

In this case, the regular user can not use `(.)` or `(->)` to access the internal state of the particle, because it has not been specified. Actually, this is ideal for defining a usage pattern with collaborators, instead of providing them with an overwhelming amount of information. The header file can be created collaboratively and later the developer focus solely on the implementation.

The implementation file for this type looks like

{% highlight C %}
// particle.c
typedef struct {
  double m; // mass
  // other parameters
} particle_t;

particle create(){
  particle_t* p = (particle_t*) malloc (sizeof(particle_t));
  return (particle) p;
}

void destroy(particle ph){
  particle_t* p = (particle_t*)ph;
  free(p);
}

void set_mass (particle ph, double m){
  particle_t* p = (particle_t*)ph;
  if (m > 0)
    p->m = m;
  else
    fprintf(stderr, "set_mass: mass must be positive\n");
}

double get_mass (particle ph){
  particle_t* p = (particle_t*)ph;
  return p->m;
}

void print(particle ph, FILE* f){
  particle_t* p = (particle_t*)ph;
  if (f != NULL){
    fprintf(f, "particle: %p", p);
    fprintf(f, "mass: %.6e\n", p->m);
  } else {
    fprintf(stderr, "print: file not open\n");
  }
}
{% endhighlight %}

Here, the letter `h` was added to the names of the handle in the functions' arguments, which are casted back to pointers to the actual type `particle_t`. This allows the developer to use the arrow `(->)` operator to access the internal state of the particle in the implementation files. Notice also, that using *setters* and *getters* also allows to check the user input before performing assignments or other operations, so that relevant messages can be generated to identify the possible sources of error in the usage.

In general, opaque pointers are seen as a way to hide the implementation details of an interface from ordinary clients, and in practice that is the case when binaries are provided instead of the implementation files, however, when the implementation files are available, a better term is *implementation separation*, since it allows advanced users to make improvements while regular users do not need to worry about the implementation. This practice tends to make the APIs more direct, readable and secure, as well as the resulting programs using the libraries. For instance:

{% highlight C %}
// main.c
#include "particle.h"

int main()
{
  particle p1 = create()
  particle p2 = create();
  set_mass(p1, 1.0);
  set_mass(p2, 2.5);
  // some process with particles 1 and 2

  // inelastic collision
  particle p3 = create();
  set_mass(p3, get_mass(p1) + get_mass(p2));
  destroy(p1);
  destroy(p2);
  // some process with particle 3

  // show particle in terminal
  print(p3, stdout);
  destroy(p3);
}
{% endhighlight %}

In this example, two particles are created, attributed different masses and some process occurs with them. Then, the particles undergo a (non-relativistic) inelastic collision, which is simulated by creating a new particle with their combined masses and immediately destroying them. This guarantees that they are no longer available for other processes and the only remaining particle is `p3`.