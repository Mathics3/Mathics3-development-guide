Working with Multiple Patterns
------------------------------

Mathics3 allows Functions to have the same name but different function
signatures. For example maybe you can call ``Hello`` with a either
one or two parameters.

Mathics3 calls a function signature a "Form".

One way to implement this in Python code is to have two different
Python methods that start with the name ``eval``, one will
handle one kind of Form, say of the kind we saw previously,
a person name is given. The other evaluation method will handle
another kind of Form, such as one where there is an
additional parameter, a string language value.

Here is some code for this..


.. code-block:: python

  from mathics.builtin.base import Builtin, String
  from mathics.core.evaluation import Evaluation

  class Hello(Builtin):
    def eval(self, person: String, language: String, evaluation: Evaluation) -> String:
      """Hello[person_String, language_String]"""

      greeting = "Bonjour" if language.value == "French" else "Hello"
      return String(f"{greeting}, {person.value}!")

    def eval_english(self, person: String, evaluation) -> String:
      """Hello[person_]"""

      return self.eval(person, Expression("English"), evaluation)

Here is a session showing how this works:

::

   $ mathics

   In[1]:= Hello["Rocky"]
   Out[1]= Hello, Rocky!

   In[2]:= Hello["Rocky", "French"]
   Out[2]= Bonjour, Rocky!

   In[3]:= Hello["Rocky", "English"]
   Out[3]= Hello, Rocky!

   In[4]:= Hello[45]
   Out[4]= Hello[45]

   In[5]:= Hello["Rocky", "French" , c]
   Out[5]= Hello["Rocky", French, c]


As shown above, in order to evaluate a function call, Mathics
pattern matches on the inputs.

If the inputs don't match any of the patterns of the existing
definitions then the entire expression is returned unchanged. This is
seen in ``In[4]``; 45 doesn't match the ``String`` type check part of
``person_String``; 45 is not a string, so ``Hello[45]`` is returned.

Similarly in ``In[5]`` three parameters are given rather than one or two.

If a function does not return any value, the Mathics3 expression is
left unchanged. Note that you have to explicitly return
``Symbol["Null"]`` (which we have a defined for you in ``SymbolNull``)
if you want that to return *Null*.
