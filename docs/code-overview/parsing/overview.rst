Parsing Overview
----------------

All parsing routines are located in `mathics.core.parser
<https://github.com/mathics/Mathics/tree/master/mathics/core/parser>`_. See
:ref:`Precedence-Climbing Parser` for parsing details.

However, here are the main points:

* The parser is recursive descent
* Because WL has a lot of operators an `Operator-precedence parser <https://en.wikipedia.org/wiki/Operator-precedence_parser#Precedence_climbing_method>`_ is used
* The result is an Full-form M-expression, which is a translation of the input string. See `Expressions as Trees <https://reference.wolfram.com/language/tutorial/Expressions.html#14609>`_.

To see a translation of the Full-Form *input* the flag ``--full-form`` can be given to the command-line utilities ``mathics`` or ``mathicsscript``.

Here is an example:

::

   $ mathics --full-form

   Mathics3 8.0.0
   ...
   Quit by pressing CONTROL-D

   In[1]:= 1 + 2 / 3
   System`Plus[1, System`Times[2, System`Power[3, -1]]]
   Out[1]= 5 / 3

Note that this is different from formatting the *output*:

::

   In[2]:= (x + 1) / 3
   System`Times[System`Plus[Global`x, 1], System`Power[3, -1]]
   Out[2]= (x + 1) / 3
   In[3]:= (x + 1) / 3 // FullForm
   System`FullForm[System`Times[System`Plus[Global`x, 1], System`Power[3, -1]]]
   Out[3]= Times[Rational[1, 3], Plus[1, x]]

Some things to notice:

* Parsing fully-qualifies names. So we have ``System`Times`` instead
  of ``Times``, even though the FullForm output shows the simple name.
* Parsing removes parenthesis used for grouping capturing this
  instead by the function nesting order
