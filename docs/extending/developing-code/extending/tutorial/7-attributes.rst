Class variables and Function Attributes
----------------------------------------

Class variables on the class that implements a Mathics3 Built-in
Function are used to set various characteristics of the built-in function.

In fact we already saw one example in the last section where the class
variable ``rules`` was used to create some Mathics3 *Rules* in the
definition of the *Hello* function.

Suppose you want to create a Mathics3 variable that starts with a
dollar sign (``$``) in accordance to the naming convention for many
system variables.

To accomplish this you assign the desired string name to class
variable ``name``, and that will override the default name.

All Functions in Mathics3 have `Attributes
<https://reference.wolfram.com/language/tutorial/EvaluationOfExpressions.html#9508>`_
which specify certain properties in evaluation such as whether a
nested invocations of a function are automatically flattened. (This is
the `Flat <https://reference.wolfram.com/language/ref/Flat.html>`_ attribute).

Setting a built-in function's attribute is  done
by setting the class variable name ``attributes``.

Suppose we want to define a variable called ``$Hello`` and don't want
lock down the attributes of this variable so that the attributes
cannot be modified.

This is done by setting the ``Locked`` attribute of in the
definition, via the ``attributes`` class variable.

Here is and example of setting some class variables to alter the
definition of a function:

.. code-block:: python

  from mathics.builtin.base import Predefined
  from mathics.core.attributes import locked as A_LOCKED
  from mathics.core.evaluation import Evaluation

  class Hello(Predefined):
      attributes = A_LOCKED
      name = "$Hello"

      def evaluate(self, evaluation: Evaluation) -> String:
          return String("Hello, World!")

Here is a session that demonstrates the above code:

::

   $ mathics

   In[1]:= $Hello
   Out[1]= Hello, World!

   In[2]:= Unprotect[$Hello]
   ClearAttributes::locked: Symbol $Hello is locked.
   Out[2]= None


.. TODO: Document and link to what which attribute does. Make a table somewhere
