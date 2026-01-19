.. toctree::
   :maxdepth: 2
   :caption: Contents:

Using Mathics3 from your code
=============================

There are various ways, which we describe here, that you can make use of
Mathics3's ability to solve equations, or run Mathics3 code.

From a shell
------------

Perhaps the least-tightly coupled way is to just call the command-line interpreter in a shell and process its output.

Here is some POSIX shell code that does this:

.. code:: bash

   $ mathicsscript -code '3!'
   5
   $ seq $(mathicsscript -code 'Integrate[x^2, {x, 0, 3}])'
   1
   2
   3
   4
   5
   6
   7
   8
   9

The above code runs the Mathics3 interpreter. The second example runs Mathics3 code a subshell and then uses the printed output in a call
to another POSX shell command, ``seq``, which uses that number.


To call Mathics3 as a subshell inside Python, the `subprocess
module <https://docs.python.org/3/library/subprocess.html>`_ might be
used like this:

.. code:: py

    from subprocess import run, PIPE

    expression = "Integrate[x^2, {x, 0, 3}]"
    cmd = ["mathicsscript", "--execute", expression]
    result = run(cmd, stdout=PIPE)
    if result.returncode == 0:
        print(int(result.stdout) + 5)

This code runs the Mathics3 interpreter as a subprocess, capturing the
output and if the execution was successful, converts the result
from its string output to a Python ``int`` and adds 5.

Using MathicsSession
--------------------


While the above is fine for running an isolated expression or two, it is pretty inefficient: Python has to be loaded every time, along with the huge Mathics3 program; all of the built-in functions need to be set up, and some terminal interaction needs to be set up as well.

If you have a sequence of Mathics3 expressions, or need to get results from Mathics3 several times inside your Python code, it is faster
to just import ``mathics`` and set up an environment for running code once.

Here is an example of that:

.. code:: py

    from mathics.session import MathicsSession
    session = MathicsSession(add_builtin=True, catch_interrupt=True)

    # Compute 20!
    result = session.evaluate("20!").to_python()
    print(result)

In the above code, ``session`` is the scratchpad area that contains
the result of the evaluations. Creating this stores all of the built-in definitions. We explicitly set the parameter ``add_builtin=True`` to
include things like ``Factorial``, which is used later.

Although we set ``add_builtin`` explicitly for pedagodical purpose,
``True`` is the default; adding this parameter wasn't necessary. We'll
leave it off in future examples.


Mathics3 Results as Python Objects
----------------------------------

In the last section, we passed a string to *session.evaluate()* we
passed a string. That string was scanned and parsed before it was
evaluated. (See the section below for what goes on in scanning and
parsing.)

A more flexible way to use Mathics3 is to skip the scanning and
parsing and call the same functions that Mathics3 calls underneath to evaluate expressions.
In this section, we will do just that.

As before, we need a ``MathicsSession`` session object as a scratchpad
area to save results, and to look up previous definitions and results.


.. code:: py

   # This is the same as before
   from mathics.session import MathicsSession
   session = MathicsSession(catch_interrupt=True)

   # These are Mathics3 classes we are going to use.
   from mathics.core.expression import Expression
   from mathics.core.atoms import Integer
   from mathics.core.systemsymbols import SymbolFactorial
   # SymbolFactorial = Symbol("System`Factorial")

   # Compute 10!
   x = Expression(SymbolFactorial, Integer(10)
                 ).evaluate(session.evaluation).to_python() # SymbolFactorial can be replaced by Symbol("Factorial")
   print(x) # 3628800

The above code computes the same value as in the last section. However we are
doing this by interacting with the Mathics3 classes now.

In this example shown above, we convert from Python's literal 10 to
Mathics3's representation of 10 using ``Integer(10)``. This value is
needed as a parameter to the ``Factorial`` function . Strictly speaking,
the full name of the factorial function is ``System`Factorial``, but
we can leave off the context name, ``System``, and Mathics3 will look
that up.

Notice how to evaluate a general Mathics3 expression in Python using
*Expression()*: the first parameter is an instance of ``Symbol`` constructed
with the Full-Form name of the function to get called. Here that is ``Symbol("System`Factorial")``.
The parameters after the first one are the parameters to the function
specified in the first parameter. Here it is that parameter ``Integer(10)``.
Each of the parameters should have type Mathics3 Expression.

The returned value of *Expression()* is Python object and data
structure that Mathics3 uses to evaluation expressions. However that
object isn't evaluated until you invoke its *evaluate()* method.
Aside from evaluating the expression, other things you might do are
format the expression so that it can be displayed nicely, or inspect
the expression in the same way you might inspect a lambda function in
Lisp.

When the *evaluate()* method is called, the function is evaluated but
the return value is still a Mathics3 *Expression*, even if it is the
computed value rather than its more symbolic form. So if Python is
going to use the value, it needs to call *to_python()* to convert
the value into a Python integer value.

Just as Python expressions can be composed from other Python
expressions, the same is true in Mathics: an *Expression()* parameter
can be another expression.

Here is an example of that:

.. code:: py

   # This is the same as before
   from mathics.session import MathicsSession
   session = MathicsSession(catch_interrupt=True)

   # These are Mathics3 classes we are going to use.
   from mathics.core.expression import Expression
   from mathics.core.atoms import Integer
   from mathics.core.symbols import SymbolPlus, SymbolTimes
   # SymbolPlus = Symbol("System`Plus"), SymbolTimes = Symbol("System`Times")

   # Compute 5 * (6 + 3)
   x = Expression(SymbolTimes, Integer(5),
         Expression(SymbolPlus, Integer(6), Integer(3))
	 ).evaluate(session.evaluation).to_python()
   print(x) # 45

Notice that precedence between operations, like *Plus()* and
*Times()* is handled simply in the order in which these functions are called, so no parenthesis is used in the functional way.

To simplify the above, we have overloaded the standard binary and unary
numeric operators ``+``, ``-``, ``/``, ``*``, ``abs()``, ``//``, and
``**`` in the ``Expressions`` and ``Numbers`` classes. With this, the above can be written as:

.. code:: py

   # This is the same as before
   from mathics.session import MathicsSession
   session = MathicsSession(catch_interrupt=True)

   from mathics.core.atoms import Integer

   # Compute 5 * (6 + 3)

   x = (
	Integer(5) * (Integer(6) + Integer(3))
        ).evaluate(session.evaluation).to_python()
   print(x) # 45

Note that when we switch to (overloaded) infix operators now need parentheses again, since ``*`` binds tighter than ``+``.


Conversion to and from Python, and SymPy
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As we saw in the last section, if you have a Mathics3 ``Element`` of
some sor, an Atomic value like an Integer or Real number, or a
compound Expression, you can get the Python equivalent for that using
the ``to_python()`` method on that object if that exists.  If the
method doesn't exist, probably there is no conversion possible into Python.

Similarly the top-level function ``from_python()`` can convert from a
Python literal value into its corresponding Mathics3 representation.

We also have a ``from_sympy()`` method and a top-level ``to_sympy()`` method.

.. TODO: Break out the evaluate example to show scanning and parsing
