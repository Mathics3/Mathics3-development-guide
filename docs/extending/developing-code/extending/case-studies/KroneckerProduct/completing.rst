Completing ``KroneckerProduct``
===============================

.. contents::
   :depth: 1
   :local:


``KroneckerProduct[]`` skeletal code
------------------------------------

Now we fill in the parts of ``KroneckerProduct`` other than the class
definition line and its docstring.

None of the other built-in Mathics3 Functions serve as good model to
copy from, so we'll use a combination ``Transpose`` and
``PauliMatrix``. The former function is selected because it takes a
Matrix parameter, and PauliMatrix function because it is a SymPy
function which was recently created at the time of this writing.

Here is the first cut for ``KroneckerProduct``:

.. code-block::

    class KroneckerProduct(SympyFunction):
        """
	... as before
        """

        attributes = A_PROTECTED | A_READ_PROTECTED
        summary_text = "Kronecker product"
        sympy_name = "physics.quantum.TensorProduct"

        def eval(self, mi: ListExpression, evaluation: Evaluation):
            "KroneckerProduct[mi__]"
            sympy_mi = [to_sympy_matrix(m) for m in mi.elements]
            return from_sympy(TensorProduct(*sympy_mi))

In the above, I needed to add imports for ``SympyFunction``,
``ListExpression``, ``Evaluation`` and other places. Examples of these
of of course can be adapted from the place where you copied the code
from.


Now let's go over the new parts...

``KroneckerProduct[]`` class variables
--------------------------------------

As in ``Undefined``, we set the attributes of function
``KroneckerProduct`` by setting the class variable ``attributes``.

Here, in addition to being "Protected", it is also "ReadProtected" and
the way we indicate both attributes is to bit-or, ``|``, the to attribute
values ``A_PROTECTED`` and ``A_READ_PROTECTED``.

The purpose of the class value ``summary_text`` is described when we
described adding ``Undefined`` so we won't go over it here. Again,
there is a unit test that checks that it exists.

However if you go into Django and type "Vector Space Operations" in
the documentation section and you should see ``KroneckerProduct`` appear
along with the summary text we just added.

There is a class variable ``sympy_name`` which we set, which here is
not necessary. If there were no evaluation method, this is the
function name that would be used by the generic evaluation method of
the ``SympyFunction`` class. I added it here, because in the future we
may want to have a way to list correspondences between Mathics3 builtin
function names and SymPy function names.

``KroneckerProduct[]`` evaluation method
----------------------------------------

Here we will focus on the evaluation method. Confusingly, the name
needs to start with ``eval``:

.. code-block:: python

        def eval(self, mi: ListExpression, evaluation: Evaluation):
            "KroneckerProduct[mi__List]"
            sympy_mi = [to_sympy_matrix(m) for m in mi.elements]
            return from_sympy(TensorProduct(*sympy_mi))

There is only one form that needs to be implemented so we can do this with one method.

The docstring for the method

.. code-block:: python

            "KroneckerProduct[mi__]"


in fact is a Mathics3 pattern that is matched in the apply phase of
Evaluation. It is used to determine which, if any, evaluation method
inside the ``KroneckerProduct`` class should be called.

By convention and to make things simple, we use the name name in the
pattern, *mi* as we did in the class docstring description. However
the name(s) that are used in the pattern must also appear in the
same order in Python definition.  Since we used ``mi__`` in the pattern in the
definition docstring, we must use ``mi`` as the first parameter in the
evaluation method. In Mathics3 ``__`` indicates a function definition
with a nonzero number of arguments.  See the `BlankSequence
<https://reference.wolfram.com/language/ref/BlankSequence.html>`_ for
more information.

When we have such a pattern object, the Mathics3 object when
represented in Python will be of type ``ListExpression``.

Evaluation methods also have an parameter of type ``Evaluation`` after
the parameters that are listed the definition docstring. By convention
we always name this parameter ``evaluation``.

Inside the function the first thing we do is to convert the elements
of the ListExpression into a Python list of SymPy Matrix values.

In this simple, first-draft function we don't do any validation of the
arguments. We should come back later and fill this in.

After we have something that we can pass on to SymPy we call the SymPy
function that corresponds to the Mathics3 function. And the return
value is converted back into Mathics3 for return.

Again we don't do checking on the return value from SymPy or from the
conversion back to Mathics3' internal Element-based object. In a future
version we should do this.

We have a number of Mathics3 builtin functions that work like this. One
thing to notice is that evaluation this way causes a great deal of
conversions from Mathics3 into SymPy followed by conversions out of
Sympy into Mathics3. Without other mechanisms like caching computed
values this can be slow. Even with caching, this can be slow.

``KroneckerProduct[]`` evaluation method, second version
---------------------------------------------------------

Instead of using pattern matching to ensure we have a list of lists,
we can generalize the pattern to match anything and then do the check in Python.

.. code-block:: python

        def eval(self, mi: ListExpression, evaluation: Evaluation):
            "KroneckerProduct[mi__]"

            #  Code in Python mi__List matching
	    if not all(m.head is for m in mi.elements):
	       return None

            sympy_mi = [to_sympy_matrix(m) for m in mi.elements]

This might be a little bit faster at the expense, code that might be
harder to maintain.  Also, while this is faster in the current
implementation, over time we may improve the interpreter so that
pattern matching performs the same kind of steps that the Python code
uses. If or when that is done, the difference would be negligible.

Keep in mind that while these kinds of tricks may add a small boost in
performance, there are also some downsides; in some cases this kind of
thing might even have to be undone in the future.

A full version of the basic implementation can be found in `this commit <https://github.com/Mathics3/mathics-core/commit/f6dcba5e2639b0fa9bf4c9ee59b2995ec257f7bb>`_.
