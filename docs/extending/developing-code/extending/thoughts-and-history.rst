Thoughts and Experience from Adding Builtins
============================================

Here are some thoughts and guidelines in writing new primitives (called "Builtins") to Mathics3. These are based what I (rocky) feel are mistakes in the project that have plagued the project.


Don't Reinvent the Wheel
------------------------

In the history of this project, to when coding a new Builtin Function, code was written from scratch rather hook into one of the existing Mathematics libraries that have function.

In other words, given the choice between writing from scratch or figure out how to interface with some library's existing code, code was written from scratch. While this is tempting, and maybe the easier path, please don't do this.

Consider Mathics3's ``Transpose`` Builtin Function to transpose a Mathics3 ``Matrix`` or an ``Array``.

In the two-dimensional situation, the code to write this is pretty simple, you copy swap the *i* and *j* components of the array.

But when we run into the 3-dimensional situation, and one should assume in WL there will always be generalizations and complications. And then things are no longer as simple. For the three-dimensional situtation, we need to introduce which axes to swap. Both WL and SymPy have your back here, and allow this to be specified with a ``Permute`` function. Also, there is the problem of determining compatibilty on the size axes that are going to be swapped to make sure they are compatible.

So if this transpose code is written in the Python from scratch, a couple things are likely to occur. Either the code will be ripped out when we want to handle the more general case. Worse, it is possible we'll go down a rabbit-hole of extending the original code, basically solving a problem that has probably already been thought about deeper solved better.

``Transpose`` is not an isolated case, in the history of the Mathics3 project. You can find the kind of thing happens implementing calculus functions such as ``FindRoot[]``, ``FindMinimum[]`` or ``FindMaximum``, among too many others.

While it might be intelletually satisfying and possibly fun writing Mathemathical algorithims in Python, please refrain from doing so.

The Mathics3 project has always been understaffed and undermaintained. By hooking into SymPy or some other package, the code has a better chance of being maintained as well as getting expanded and generalized, if all of that effort hasn't occured prior to the need inside Mathics3.

As an example of the kind of problems we encounter here, consider Mathics3 original image-processing routines. Too much of this was written in pure Python. This code was both slow and buggy. And it didn't support certain image formats we wanted to use. By switching to using the Pillow Python package all of this was fixed.

Magic-bullet Technology versus Thoughtful Coding
++++++++++++++++++++++++++++++++++++++++++++++++

Historically, running Mathics3 has been slow, *very* slow. While Mathics3 will, at least for a long time, *always* be slow compared with Wolfram Alpha.

But in retrospect, a *lot* of the slowness I've seen comes from imprecise thinking and coding. Many people of opined that if the code were only written in C++ or Rust, or run through Cython the slowness problems would magically disappear.

In truth, what I've encountered is that a number of our problems have been simple-minded poor-performing implementations.

* Unecessary conversions from Python to Mathics3 and vice versa
* Not abstracting instruction-level evaluation routines as you might in a bytecode interpreter, and shoving everything through a very slow and general compound expression evaluation routine, instead of calling the faster instruction-level routines
* Custom list expression evaluation. (We currently need a custom box evalution)



Caching Expression cache vs. @lru
+++++++++++++++++++++++++++++++++

Don't Micromanage Python
-------------------------

* flattening calls,
* ordering conditions of an "if" statements
* assuming Cython, rewriting into another Programming Language (or some technology) is going to fix gross inefficiencies in design of the code.

Misuse of Rewrite Rules
------------------------


Overusing Expensive Evaluate
----------------------------
