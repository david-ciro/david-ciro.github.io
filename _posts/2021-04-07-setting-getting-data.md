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
To better illustrate this point, consider the dynamical properties of a rigid body, i.e. its mass, inertia tensor, angular velocities about its main axes, location and velocity of the center of mass, etc. The mentioned properties add up to 16 parameters due to the symmetries of the tensor. To simplify iterative calculations it is convenient to separate the setters and getters in 5 groups with the corresponding arguments for the components, for instance:

{% highlight C %}
// rigid_body.h

typedef struct rigid_body_t* rigid_body;

double
rgb_set_m(rigid_body rgb, double m);

double
rgb_get_m(rigid_body rgb);

void
rgb_set_Iij(rigid_body rgb, size_t i, size_t j, double Iij);

double
rgb_get_Iij(rigid_body rgb, size_t i, size_t j);

void
rgb_set_xi(rigid_body rgb, size_t i, double xi);

double
rgb_get_xi(rigid_body rgb, size_t i);

void
rgb_set_vi(rigid_body rgb, size_t i, double vi);

double
rgb_get_vi(rigid_body rgb, size_t i);

void
rgb_set_wi(rigid_body rgb, size_t i, double wi);

double
rgb_get_wi(rigid_body rgb, size_t i);
{% endhighlight C %}

can be used to set and get the individual components of all the vectors tensors and the mass of the body. This can be convenient in situations when the parameters can be grouped in a few contexts that share the same access method, but can be very cumbersome where there is a myriad of individual parameters that do not share a context. For the sake of this example, consider that the rigid body parameters are not easily grouped and we would need a set/get pair for each parameter in the object, leading to 32 separate access methods.

To simplify the setting and getting we can create an enumeration that contains identifiers or names for the relevant parameters. For instance:

{% highlight C %}
// rigid_body.h
typedef enum
{
    // the total mass
    rgb_m,
    // the inertia tensor (resp. C.M.)
    rgb_Ixx,
    rgb_Ixy, // Iyx
    rgb_Ixz, // Izx
    rgb_Iyy,
    rgb_Iyz, // Izy
    rgb_Izz,
    // the center of mass
    rgb_x,
    rgb_y,
    rgb_z,
    // center of mass velocity
    rgb_vx,
    rgb_vy,
    rgb_vz,
    // angular velocity about main axes
    rgb_wx,
    rgb_wy,
    rgb_wz
}
rgb_param;
{% endhighlight C %}

Since all the parameters are `double` we can define a single pair to access them using the newly defined enum type

{% highlight C %}
void
rgb_set_param(rigid_body rgb, rgb_param name, double value);

double
rgb_get_param(rigid_body rgb, rgb_param name);
{% endhighlight C %}

which results in a far more compact header file.
For the implementation side it leads also to very readable and scalable code with an easy to follow pattern, for instance:

{% highlight C %}
// rigid_body.c

typedef struct
{
    double  m;
    double* I; // {Ixx, Iyy, Izz, Ixy, Ixz, Iyz}
    double* x; // {x, y, z}
    double* v; // {vx, vy, vz}
    double* w; // {wx, wy, wz}
    // other params
} rigid_body_t;

void
rgb_set_param(rigid_body rgb_h, rgb_param name, double value)
{
    rigid_body_t* rgb = (rigid_body_t*)rgb_h;
    switch (name) {
        case rgb_m:
            if (value > 0)
                rgb->m = value;
            else
                fprintf(stderr, "rgb_set_param: invalid mass\n");
            break;

        case rgb_Ixx:
            if (value > 0)
                rgb->I[0] = value;
            else
                fprintf(stderr, "rgb_set_param: invalid Ixx\n");
            break;

        case rgb_Iyy:
            if (value > 0)
                rgb->I[1] = value;
            else
                fprintf(stderr, "rgb_set_param: invalid Iyy\n");
            break;

        case rgb_Izz:
            if (value > 0)
                rgb->I[2] = value;
            else
                fprintf(stderr, "rgb_set_param: invalid Izz\n");
            break;

        case rgb_Ixy:
            rgb->I[3] = value;
            break;

        case rgb_Ixz:
            rgb->I[4] = value;
            break;

        case rgb_Iyz:
            rgb->I[5] = value;
            break;

        case rgb_x:
            rgb->x[0] = value;
            break;

        case rgb_y:
            rgb->x[1] = value;
            break;

        case rgb_z:
            rgb->x[2] = value;
            break;

        case rgb_vx:
            rgb->v[0] = value;
            break;

        case rgb_vy:
            rgb->v[1] = value;
            break;

        case rgb_vz:
            rgb->v[2] = value;
            break;

        case rgb_wx:
            rgb->w[0] = value;
            break;

        case rgb_wy:
            rgb->w[1] = value;
            break;

        case rgb_wz:
            rgb->w[2] = value;
            break;

        default:
            fprintf(stderr, "rgb_set_param: unknown param name\n");
            break;
    }
}
{% endhighlight C %}

And a similar format for the get function. Adding new parameters generates little code footprint and only implies a new block on the seg/get methods. The complete code for this module can be accessed in the [examples repository](https://github.com/david-ciro/dciro-examples).

# Computational cost
It might appear that the use of large switch statements leads to a large computational overhead since all parameters must be matched to their corresponding case before performing the operations instead of using a direct method, however, the resulting overhead is quite small (about 15% for the experiments performed). Check the [examples](https://github.com/david-ciro/dciro-examples) under the `dbg/` folder for a direct comparison between the computational times for a large number of random direct accesses vs accesses by name.