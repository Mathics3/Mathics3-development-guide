Is there something similar to ``Undefined`` that we can use as an example?
---------------------------------------------------------------------------

We have already identified that code for this should go in `mathics/builtins/numbers/constants.py <https://github.com/Mathics3/mathics-core/blob/master/mathics/builtin/numbers/constants.py>`_. If we look inside that file we find that ``Underflow`` and if we look this up the `WMA documentation for this <https://reference.wolfram.com/language/ref/Underflow.html>`_ we see that this too is a Built-in Symbol.
