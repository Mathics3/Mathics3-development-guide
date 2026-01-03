Introducing the ``Builtin`` Class and Pattern-based Parameter Specification
---------------------------------------------------------------------------

.. index:: Builtin

The ``Predefined`` class is not used very much.  Most of the time,
you'll probably need to pass information into the function you want to
add. For this, use the ``Builtin`` class.  In WL these things
functions and variables defined this way are tagged as a "Built-in
Symbol".

The method that you should define in the Builtin class that should get
invoked needs to start off with the name ``eval``.

As before, this method has an ``evaluation`` parameter at the end.

Other parameters that are appropriate for the function are specified
using the docstring of the ``eval`` method. The ``eval`` method
docstring looks pretty much the same as it would look if you were
defining this in WL.

For example, let's add a string parameter, named *person*. In Mathics3, the function
might look like this:


.. code-block:: mathematica


  Hello[person_String] := Print["Hello, " <> person <> "!"]

See `Defining Functions
<https://reference.wolfram.com/language/tutorial/FunctionsAndPrograms.html#13037>`_
and `Introduction to Patterns
<https://reference.wolfram.com/language/tutorial/Patterns.html#139>`_
for more information on specifying function signatures, and the pattern
language that is used inside the function signature.

The evaluation method, looks like this:

.. code-block:: python

  def eval(self, person: String, evaluation: Evaluation) -> String:
    "Hello[person_String]"
        return String(f"Hello, {person.value}!")

Here is the complete code:

.. code-block:: python

  from mathics.builtin.base import Builtin
  from mathics.core.atom import String
  from mathics.core.evaluation import Evaluation

  class Hello(Builtin):
    def eval(self, person: String, evaluation: Evaluation) -> String:
      "Hello[person_String]"
          return String(f"Hello, {person.value}!")

The docstring for the ``eval`` method is a Mathics3 pattern:

.. code-block:: python

      "Hello[person_String]"

Inside Mathic3 you can test what this matches using the Mathics3 ``MatchQ[]`` function:

.. code-block:: mathematica

		In[1]:= MatchQ[Hello[123], Hello[person_String]]
		Out[1]= False

		In[2]:= MatchQ[Hello["123"], Hello[person_String]]
		Out[2]= True

		In[3]:= MatchQ[Hell["123"], Hello[person_String]]
		Out[3]= False



Switching now to how Python sees things, the parameter ``person`` has
type Mathics3 ``String``. We see this in the function definition:

.. code-block:: python

    def eval(self, person: String, evaluation: Evaluation) -> String:

To use the Mathics3 object for use in Python as a ``str``, we need to
convert it to a Python value. This is done with ``person.value``. And
the the return value also needs to be a Mathics3 ``String`` so we need
to convert the Python string in Python back to a Mathics3 ``String``:

.. code-block:: python

          return String(f"Hello, {person.value}!")

Previously, since we weren't modifying the Mathics3 String, no
conversions to Python and from Python were needed.

See `Patterns
<https://reference.wolfram.com/language/tutorial/Patterns.html>`_ for
more information about how to specify expressions with patterns in
them that you might use in an *eval()* method docstring.

Again, `MatchQ[]
<https://reference.wolfram.com/language/ref/MatchQ.html>`_ can be
helpful in testing the pattern used in the docstring.

When deciding what pattern to use in a the docstring for an ``eval``
method, you should give some thought to error checking. If someone writes ``Hello[5.0]``, do you want to treat this as an error or do nothing and possibly allow some other pattern to be allowed to match this?

If you want to provide an error, then the code with drop the ``_String`` pattern and check inside the code:

.. code-block:: python

  class Hello(Builtin):
    def eval(self, person, evaluation: Evaluation) -> String:
      "Hello[person_]"
          if not isinstance(person, String):
	      evaluation.message("Hello", "string", person)
	      return
          return String(f"Hello, {person.value}!")
