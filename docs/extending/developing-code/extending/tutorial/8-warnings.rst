Emitting Warnings and Errors
----------------------------

Previously, in :ref:`Checking Parameter Counts`, we showed how to give
and error when giving the wrong number of parameters for a function.

But other things can wrong, too. How can one emit an error from inside an evaluator?

Warnings and error messages in Mathics3 can be specified via the ``messages`` class field. The
``messages`` class field is a dictionary whose keys are the names of possible
warning messages and whose values are template warning messages. For example,
we may want to display a warning when our users pass something other than a
string to ``Hello``:

.. code-block:: python

  from typing import Optional
  from mathics.builtin.base import Builtin, String
  from mathics.core.evaluation import Evaluation

  class Hello(Builtin):
    """
    <dl>
      <dt>Hello[$person$]
      <dd>An example function in a Python-importable Mathics3 module.
    </dl>
    >> Hello["World"]
     = Hello, World!
    """

    messages = {
      'nstr': '`1` is not a string',
    }

    def eval(self, person, evaluation: Evaluation) -> Optional[String]:
      "Hello[person_]"

      if not isnstance(person, String):
        return evaluation.message('Hello', 'nstr', person)

      return String(f"Hello, {person.value}!")

In this case, calling ``Hello[45]`` will emit the warning ``nstr: 45
is not a string``. Since it is inside a Python ``return`` statement,
the code does not continue. However, the expression will be returned unevaluated.

If you want to indicate a *failure*, then return ``SymbolFailed``:


.. code-block:: python

  ...
  from mathics.systemsymbols import SymbolFailed

  class Hello(Builtin):
    ...
    def eval(self, person, evaluation: Evaluation) -> Optional[String] | SymbolNone:
      "Hello[person_]"

      if not isinstance(person, String):
        evaluation.message("Hello", "nstr", person)
	return SymbolFailed

    ...
