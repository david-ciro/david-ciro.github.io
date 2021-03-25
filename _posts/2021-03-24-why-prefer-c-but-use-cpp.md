---
layout: post
title:  "Why I prefer C but use C++"
date:   2021-03-24 20:42:44 -0300
categories: jekyll update
---

My first experience with programming was with C, the language of choice in 2005 for the introductory course in computational physics at my Alma Mater. By the end of the course there was a short mention of C++, Python and OOP, but I was not very interested in computational physics and eventually forgot the language syntax. Since I was mainly focused in theoretical research I didn't require advanced numerical routines and my language of choice was not very influential on my everyday activities. During that time I used mostly Mathematica and some Python for scripting, but as computations became more demanding and required more control I started migrating some of my Mathematica code to Python with no much improvement in performance and a huge penalty in usability. At the time I was requiring some uncommon level of control over some stepping procedure and this lead me to the [GSL library](https://www.gnu.org/software/gsl/), which is implemented in C following the guidelines of the GNU software foundation. The excellent [documentation](https://www.gnu.org/software/gsl/doc/html/index.html) of GSL and its usage became my informal programming education and influence my current workflow in C.

By the end of my last post-doc I became curious about the advantages of abstraction and OOP, which led me to C++. Then I engaged in a discussion with a group colleges in my Physics department. By the time I was quite confortable with C and didn't see the many fundamental advantages on the intrinsic features of C++. Classes felt mostly as structures with internal references to methods, constructors felt like glorified allocators and memory management felt confusing in regards to the lifespan of objects and garbage collection. Of course, due to my C background I became used to manage memory and is not something that bothers me. It was clear that there was a significant effort by the language maintainers to separate the user from these tasks and understandably so, nasty bugs can emmerge from bad memory management and their solution to this problem was to minimize the user interaction with memory.

I prefer `int` functions that return error codes instead of `void` functions with internal exclusion throwers and I like to use `void` pointers to generalize my routines.

However, when de (...)
