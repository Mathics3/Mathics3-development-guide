.. index:: packages
.. packages:

Mathics3 Packages
=================

If you are familiar with the Wolfram Language, a natural thing to do is to write in the Mathics3 dialect of the Wolfram Language.

The Wolfram Language ``Get`` or ``<<`` can be used to read in a file and evaluate each expression returning the last one.

Higher-level packaging mechanism can be built from this.

We have a very crude one for packages that come distributed with the Mathics3 Kernel, and they can be found in `mathics/packages <https://github.com/Mathics3/mathics-core/tree/master/mathics/packages>`_.

For a list of built-in packages look at that directory or in the `Mathics3 Packages Wiki page <https://github.com/Mathics3/mathics-omnibus/wiki/Mathics-Packages>`_.

Maybe you'd like to *contribute* a new package?  Please do! Here is how...

The format for adding a package is pretty simple. You add your a directory with your package name some higher-level category like ``VectorAnalysis`` or ``DiscreteMath`` If directory for such higher-level category doesn't exist, then create one. Add your package inside this directory, this is a file that usually ends in or has the ``.m`` extension.

It is good practice to add a ``BeginPackage[]`` to restrict the context to your package and the System namespace.

Let's say you want to add a package called ``Turtle`` that does some sort of Robotics. This category doesn't exist, so you would create directory in ``mathics/packages`` and your Mathics3 code ``Turtle.m`` would be in that directory.

Inside ``Turtle.m`` Code like this might appear:


::

    (* ::Package:: *)
    (* :Title: Turtle Robotics *)
    (* :Author: you *)

     BeginPackage["Turtle`"]
    (* ... your specific code *)
    EndPackage[]

Finally you need to add your package to ``Robotics/Kernel/Init.m`` . Since this doesn't exist yet, you would create it like and
its contents would simply be:


::

    Get[ "Robotics`Turtle`"]


This is all in your fork of our code. When you want this to be included as part of the Mathics3 distribution, put in a `Gitub Pull Request <https://guides.github.com/activities/hello-world/#pr>`_
And once the package is in update the wiki to add your entry.
