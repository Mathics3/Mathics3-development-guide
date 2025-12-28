``Curl`` placement in Mathics3
==============================

To find where to put ``Curl`` in Mathics3, we look this up the  `WMA docs <https://reference.wolfram.com/language/ref/Curl.html>`_  and then looking at the Related Guides drop-down menu we see:

* Vector Analysis
* Operations on Vectors
* Partial Differential Equatio=ns
* Calculus

among other sections.

Connect to the Django application. In the documentation section (on the right or click on the "?" if this doesn't appear), type in "Vector A", and we see there is no "Vector Analysis Operations" section yet.

However if we type "Operations" we see "Operations on Vectors" is already there. If we go into the documentation for that section,  we find there is a subsection of that called "Mathematical Operations".
So that is a possibility.

We also have a "Calculus" section as well, and under that, "Curl" is listed under the subsection "Vector Calculus". However we currently do not have a subsection for this. So we will put it under Mathematical Operations.

A ``git grep 'Mathematical Operations'`` tells us that this is located in ``mathics/builtins/vectors/math_ops.py``.

Down at the bottom we see of that file we find:

.. code-block:: python

    # TODO: Curl, Div

which reinforces where this should go.



When there is no existing section in Mathics3 already...
--------------------------------------------------------

When an existing section or subsection does not exist, see if there is a another categorization of the function for which we already do have a section or subsection.

If not you will need to add a new directory. Then you'll need to add your new module name to ``setup.py`` and ``mathics/builtin/__init__.py``.
