.. _doc_basic_skeleton:

``KroneckerProduct`` Basic Skeleton
====================================

.. contents::
   :depth: 1
   :local:

Armed with information from the previous sections:

* Where the function belongs: "Vector Space Operations", and
* Information from WMA and SymPy on what should be implemented and how it hooks into an existing library function
* Wikipedia name of this function

we are now ready to being with a skeletal version from which we can fill out later.

As noted before, "Vector Space Operations" is located in directory ``mathics/builtin/vectors/vector_space_operations.py``.

Down that the bottom of that file we have:

.. code-block:: python

   # TODO: Orthogonalize, KroneckerProduct

so we have noted the need for that here already. Good. Let's remove ``KroneckerProduct`` from that list.

``KroneckerProduct`` Title Line
-------------------------------


Here is the the beginning of the Python code to implement ``KroneckerProduct``:


.. code-block:: python

    class KroneckerProduct(SympyFunction):
        """
        <url>:Kronecker product: https://en.wikipedia.org/wiki/Kronecker_product</url> (<url>:SymPy: https://docs.sympy.org/latest/modules/physics/quantum/tensorproduct.html</url>, <url>:WMA: https://reference.wolfram.com/language/ref/KroneckerProduct.html</url>)
	...
	"""

We will go over the docstring entry a little more quickly than we did
in describing ``Undefined``. Consult :ref:`First Skeletal version` for
a more detailed description of what goes in the docstring. Here we will
cover aspects that were not covered there.


In contrast to the class definition in ``Undefined``, which is a subclass of
``Builtin``, here we use a ``SympyFunction`` which in turn is a subclass of ``Builtin``.
``SympyFunction`` has been customized for built-in Mathics3 functions that are implemented via SymPy.

Also in contrast the title line for ``Undefined``, the title line here is longer because there is a Wikipedia entry that corresponds to this function, and because there is a corresponding SymPy function as well.


``KroneckerProduct`` Function Definition Description
----------------------------------------------------

.. code-block:: python

    class KroneckerProduct(SympyFunction):
        """
        ...
        <dl>
          <dt>'KroneckerProduct[$m1$, $m2$, ...]'
          <dd>returns the Kronecker product of the arrays $mi$
        </dl>
        """

In addition to the tags seen when we wrote ``Undefined``, we have a
new markup tag. This is the dollar signs that surround variable names. In particular, we have ``$m1$``, ``$m2$`` and ``$mi$``.

The dollar signed around the names indicate that the text inside is a variable or parameter to the function.

Also notice that the word the *entire form* surrounded in quotes the ``<dt>`` tag, that is ``'KroneckerProduct[$m1$, $m2$, ...]'``

``KroneckerProduct`` Examples
-----------------------------

As we saw with ``Undefined``, the last part of the docstring contains useful examples involving the function we are defining, ``KroneckerProduct``:

.. code-block:: python

    class KroneckerProduct(SympyFunction):
        """
        ...
        <dl>
          <dt>'KroneckerProduct[$m1$, $m2$, ...]'
          <dd>returns the Kronecker product of the arrays $mi$
        </dl>

        Show symbolically how the Kronecker product works on two two-dimensional arrays:

        >> a = {{a11, a12}, {a21, a22}}; b = {{b11, b12}, {b21, b22}};
        >> KroneckerProduct[a, b]
         = {{a11 b11, a11 b12, a12 b11, a12 b12}, {a11 b21, a11 b22, a12 b21, a12 b22}, {a21 b11, a21 b12, a22 b11, a22 b12}, {a21 b21, a21 b22, a22 b21, a22 b22}}

        Now do the same with discrete values:

        >> a = {{0, 1}, {-1, 0}}; b = {{1, 2}, {3, 4}};

        >> KroneckerProduct[a, b] // MatrixForm
         = 0    0    1   2
         .
         .  0    0    3   4
         .
         . -1   -2   0   0
         .
         . -3   -4   0   0

        #> Clear[a, b];
        """

And here we encounter some new markup. First, there are lines that start with ``.`` These are used to indicate that
the expected output has line breaks in them. The attach to the output that appears above them: either another
line that starts ``.`` or a line that starts ``=``.

Finally we have a line that starts ``#>``. This indicates a Mathics3
statement that is to be run, but should not be shown in the Django
documentation. We use this here as cleanup for the code that gets in
the examples before: we had defined two variables *a* and *b*, and we
now want to make sure these definitions get removed. Since we don't
really care about the output of ``Clear[]`` (which is ``None``) we add
the semicolon to suppress output. Notice that there is no ``=`` line
after ``Clear`` because no output is expected.
