.. index:: M-expression, AST
.. _ast:

AST, M-Expression, General List: same thing
============================================

The end result of scanning and parsing is an :term:`M-expression`, which is a
Python object of type ``Expression``. In non-Lisp-oriented systems and in compiler terminology, the
M-expression is also called an *abstract syntax tree* or AST. The
first element of an ``Expression`` is called the "head".

When the ``Expression`` is to be evaluated, the head should be a
``Symbol`` for a Mathics3 Function or another ``Expression`` when
evaluated produces a function symbol.

In Mathics3, there are only very few different kinds of
non-``Expression`` nodes, called "atoms" that can appear in the tree
initially from the parser:

.. index:: Number, String, Symbol, LiteralSymbol, Filename

* ``Number``
* ``String``
* ``Symbol``
* ``Filename``

With the exception of the addition of ``Filename`` these are
almost the same atoms described in `Basic Internal Architecture
<https://reference.wolfram.com/language/tutorial/TheInternalsOfTheWolframSystem.html#6608>`_
for WL. However, these are not all of the kinds of Atoms that exist when Mathics3 starts up.
There are other atoms like ``Complex`` numbers.

We should note that ``Symbol`` really has two distinct meanings. After
a parse, a ``Symbol`` is just its name and this name is unique in the
same way that the integer 5 is unique or the string "foo".

In a programming language, you might think of a Symbol as it is born
from the parser as akin to a variable identifier.

Later on in the evaluation process, symbols often have values bound to
them. When this happens, they are more like variables in a programming
language. There is not just the name but a value bound to that for
some particular scope. Again, think about the difference between an
identifier name in a programming language and the variable it
represents.

The class definitions for these are given in `mathics.core.parser.ast
<https://github.com/Mathics3/mathics-core/blob/master/mathics/core/parser/ast.py>`_.

If you compare the above four AST types with other languages, you'll
find this is pretty sparse. For example, `Python's AST
<https://docs.python.org/3/library/ast.html>`_ has well over 30
different types.

So what's the difference? Python specializes AST types for different
kinds of programming language constructs, such as for loops,
conditional statements, exception blocks, different kinds of
expressions, and so on. In WL and Mathics3, these are all simply
built-in functions.

However we do have specialized certain kinds of list objects such as non-function List objects.

In the process of Evaluation, described in the next section, more
kinds of objects beyond the four may get introduced as the
M-expression is rewritten and transformed.

Here is an example of the transformation from an input string to the AST Form (an M-Expression)
We use the ``--full-form`` option in the ``mathics`` command-line to get this information.
Note that this shows the *input* before evaluation:

.. code-block:: mathematica

    In[1]:= 3 a - b
    System`Plus[System`Times[3, Global`a], System`Times[-1, Global`b]]
    Out[1]= 3 a - b

    In[2]:= 3 5 - 6
    System`Plus[System`Times[3, 5], -6]
    Out[2]= 9
