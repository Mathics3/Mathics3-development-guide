``KroneckerProduct`` placement in Mathics3
==========================================

To find where to put ``KroneckerProduct`` in Mathics, we look this up the  `WMA docs <https://reference.wolfram.com/language/ref/KroneckerProduct.html>`_  and then looking at the Related Guides drop-down menu we see:

* Matrix Operations
* Operations on Vectors

Connect to the Django application. In the documentation section (on the right or click on the "?" if this doesn't appear), type in "Matrix", and we see there is no "Matrix Operations" section yet.

However if we type "Operations" we see "Operations on Vectors" is already there and it has a subsection called "Vector Space Operations". So let's use that.

A ``git grep 'Vector Space Operations'`` tells us that this is located in ``mathics/builtins/vectors/vector_space_operations.py``.
