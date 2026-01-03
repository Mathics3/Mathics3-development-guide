Hello, World! -- using ``Predefined``
-------------------------------------

Here is a simple "Hello, World!" Mathics3 function.

.. code-block:: python

  from mathics.builtin.base import Predefined
  from mathics.core.atom import String
  from mathics.core.evaluation import Evaluation

  class Hello(Predefined):
    def evaluate(self, evaluation: Evaluation) -> String:
      return String("Hello, World!")

Add the above at the end to a file in `mathics.builtin
<https://github.com/Mathics3/mathics-core/tree/master/mathics/builtin>`_
like ``system.py``.

Later on, we will show how to add code without modify the Mathics3 core, but
for now we'll start simple.

Now start Mathics3 from the Mathics3 source tree:

::

   $ python mathics/main.py
   Mathics3 8.0.1
   ...
   In[1]:= Hello
   Out[1]= Hello, World!


Now let's go over the code. For a Symbol ``Hello`` we
define a Python ``Class`` of type ``Predefined``. ``Predefined`` is perhaps the
most primitive class. It is used often to define a built-in system variable like ``$MachineName``.
(Built-in system variables, are called "Builtin Symbols" in WL).
See :ref:`Defining Variable ``$MachineName``` for complete example on using ``Predefined``.

In the ``Predefined`` clss you define a method *evaluate(self, evaluation: Evaluation)* which
is what will get called when the Symbol is evaluated. The
``evaluation`` parameter contains the evaluation environment that can
be used to get definitions of variables and other things that may be
needed to perform the function.

However, here all we do is return a Mathics3 String, so we don't need
to use what is in evaluation. In a simple situations, you may find you
don't need to use the ``evaluation`` parameter.

The return value of a Mathics3 function should be some sort of
superclass of :ref:`BaseElement Class`. A ``String`` is a subclass of
the :ref:`Atom Class` which in turn is a subclass of the
``BaseElement``. You can also return the ``None`` value, in which case
the expression is unchanged.
