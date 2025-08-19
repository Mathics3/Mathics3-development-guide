.. autoload:

Mathics3 Setup Code Read on Loading
===================================

Although a lot of the Mathics3 Kernel is written in Python for efficiency, it is sometimes easier and nicer just to have little bits for core code written in Mathics3.

When Mathics3 starts up, it reads files written in the Mathics3 dialect of Wolfram Language that are found in  `mathics/autoload <https://github.com/Mathics3/mathics-core/tree/master/mathics/autoload>`_ .


For example in a file called ``settings.m`` which have some Mathics3-oriented variables that are set to default values.

One way to implement more primitives that are missing in Mathics3 but are in the Wolfram Language might be to implement the primitive using existing Mathics3 constructs. And then this function definition can be added to a file inside a directory of Mathics3 files that gets read on startup.
