===============================
Python Modules in Mathics-Core
===============================

The fundamental Python modules that make up the ``mathics-core`` github repository are described below.

.. contents::
   :depth: 1
   :local:
   :backlinks: none

`mathics.builtin <https://github.com/Mathics3/mathics-core/tree/master/mathics/builtin>`_
==========================================================================================

``mathics.builtin`` defines top-level Mathics3 Builtin Functions and Variables.

Mathics3 has over a thousand Built-in Functions and Variables. Many of of these
have their top-level definition here. By "top-level definition", we mean defining the name that the Symbol is given inside Mathics3, its documentation string, and its summary text, and smallish things like this.

When the Mathics3 Symbol being defined is a Builtin Function, there are also rewrite rules, error messages related to the function, the function signature and function attributes, like ``Hold``, or ``Listable``, are defined here. You can think of this as analogous to C/C++ header ``.h`` files where top-level or overall information is defined without specifying too much of the implementation detail.

Each module under ``mathics.builtin`` collects a related set of Mathics3 Builtin Functions and Variables. These are typically found in WMA organized as "Language Guides".

For example, ``mathics.builtin.numeric`` contains some of the numerical functions that are found in `WMA Numerical Functions Guide <https://reference.wolfram.com/language/guide/NumericalFunctions.html>`_.
This includes built-in functions:

* ``Abs``,
* ``Chop``,
* ``RealAbs``,
* ``RealSign``, and so on.

Note however that the coverage of Builtin Functions by WMA Guides is not unique; so there is some arbitrariness in which module or guide is used to place a Builtin Function.

As Mathics3 is grows, we have been adding submodules under ``mathics.builtin``. For example, ``mathics.builtin.statistics`` is roughly equivalent to `WMA Descriptive Statistics <https://reference.wolfram.com/language/guide/DescriptiveStatistics.html>`_. Under that module, there are submodules like ``mathics.builtin.statistics.dispersion`` to group Builtin Functions related to  "Dispersion Statistics".

Modules outside of ``mathics.builtin`` implementation-specific of particular Builtin functions. ``mathics.eval`` contains the bulk of a evaluation implementation after parameter checking is done. ``mathics.compile`` contains compilation implementation details.  However as with the C/C++ header, ``.h``, versus body, ``.c``, analogy, you may find some looseness here as historically the code has not followed this organization.

`mathics.core <https://github.com/Mathics3/mathics-core/tree/master/mathics/core>`_
====================================================================================

This is the core of the ``mathics-core`` package.

Here you find the the lowest, most fundamental modules and classes.

Objects here are fundamental to the system. These include objects like:


* ``Symbols``,
* ``Numbers``,
* ``Rational``,
* ``Expressions``,
* ``Patterns``,
* ``Rules``, and others.

While there maybe some parts of ``mathics-core`` that could conceivably be written in
Mathics3, for efficiency, everything here is written in Python.

`mathics.core.builtin <https://github.com/Mathics3/mathics-core/tree/master/mathics/core/builtin>`_
---------------------------------------------------------------------------------------------------

This module contains Class definitions used in ``mathics.builtin`` modules that define the
base Mathics3's classes: ``Predefined``, ``Builtin``, ``Test``, ``SympyFunction``, ``MPMathFunction``, ``Operator`` and from that:

* ``UnaryOperator``,
* ``BinaryOperator``,
* ``PrefixOperator``,
* ``PostfixOperator``, and others.

`mathics.core.convert <https://github.com/Mathics3/mathics-core/tree/master/mathics/core/convert>`_
---------------------------------------------------------------------------------------------------

Routines here convert between various internal representations such as
between ``Expressions``, LLVM functions, SymPy Arguments, MPMath
datatypes and so on. However this does not include the initial
conversion a parsed string into one of the internal
representations. That is done in the parser.

`mathics.core.compile <https://github.com/Mathics3/mathics-core/tree/master/mathics/core/compile>`_
===================================================================================================

This module contains Mathics3 ``Compile`` implementation details. At present, we use LLVM for this.


`mathics.core.eval <https://github.com/Mathics3/mathics-core/tree/master/mathics/core/eval>`_
=============================================================================================

This module contains Mathics3 Evaluation Functions.

Routines here are core operations or functions that implement evaluation. If there
were an instruction interpreter, these would be the instructions.

These operations then should include the most commonly-used Builtin-functions like
``N[]`` and routines in support of performing those evaluation operations/instructions.

Performance of the operations here can be important for overall interpreter performance.

It may be even be that some of the functions here should be written in faster
language like C, Cython, or Rust.


`mathics.core.parser <https://github.com/Mathics3/mathics-core/tree/master/mathics/core/parser>`_
-------------------------------------------------------------------------------------------------

This module contains routines that takes tokens from the scanner (in a
separate module and repository) and parses this into some sort of
M-Expression as its AST (Abstract Syntax Tree).

There is a separate `README
<https://github.com/Mathics3/mathics-core/blob/master/mathics/core/parser/README.md>`_
for describing how this works.


`mathics.doc <https://github.com/Mathics3/mathics-core/tree/master/mathics/doc>`_
==================================================================================

Module for handling Mathics-style documentation.

Right now this covers common LaTeX/PDF and routines common to
Mathics3 Django. When this code is moved out, perhaps it will
include the Mathics3 Django-specific piece.
Mathics' home-grown documentation system.

`mathics.format <https://github.com/Mathics3/mathics-core/tree/master/mathics/format>`_
========================================================================================

This module contains Mathics3 Lower-level formatting routines.

Lower-level formatting routines.

Built-in Lower-level formatting includes Asymptote, MathML, SVG,
threejs, and plain text.  We hope and expect other formatting to other
kinds backend renderers like matplotlib, can be done by following the
pattern used here.

These routines typically get called in formatting Mathics3 Box objects.

The higher level _Forms_ (e.g. ``TeXForm``, ``MathMLForm``) typically cause
specific formatters to get called, (e.g. latex, mathml). However, the
two concepts and levels are a little bit different. A given From can
cause invoke of several formatters, which the front-end can influence
based on its capabilities and back-end renders available to it.

For example, in graphics we may be several different kinds of
renderers, SVG, or Asymptote for a particular kind of graphics Box.
The front-end nees to decides which format it better suited for it.
The Box, however, is created via a particular high-level Form.

As another example, front-end may decide to use MathJaX to render
TeXForm if the front-end supports this and the user so desires that.
Routines here are core operations or functions that implement evaluation. If there
were an instruction interpreter, these would be the instructions.

These operations then should include the most commonly-used Builtin-functions like
``N[]`` and routines in support of performing those evaluation operations/instructions.

Performance of the operations here can be important for overall interpreter performance.

It may be even be that some of the functions here should be written in faster
language like C, Cython, or Rust.
