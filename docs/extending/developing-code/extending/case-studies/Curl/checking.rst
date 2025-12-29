Checking the ``Curl[]`` Basic Version
=====================================

Although the checking steps are similar to what we did in
``KroneckerProduct`` we will go through these in a more abbreviated
fashion.  If you would like steps spelled out in more detail see the
corresponding section there.


Going Django and type "Mathematical Operations" in the documentation section and you should see "Curl" appear first before "Cross". And note that it has the summary text that we added above.

Click on "Curl" in that list and we now see all of the information in the docstring we just entered.

Following links shown in blue should also be checked:

* Curl,
* SymPy, and
* WMA


Rather than go into an ``mathics`` shell, we will just run the doc tests:


.. code-block:: Bash


    $ cd Mathics3/mathics-core
    $ python mathics/docpipeline.py -s Curl
    Testing section(s): Curl
    b'   1 ( 0): TEST a = {{a11, a12}, {a21, a22}}; b = {{b11, b12}, {b21, b22}};'
    b'   2 ( 0): TEST KroneckerProduct[a, b]'
    b'   3 ( 0): TEST a = {{0, 1}, {-1, 0}}; b = {{1, 2}, {3, 4}};'
    b'   4 ( 0): TEST KroneckerProduct[a, b] // MatrixForm'
    b'   5 ( 0): TEST Clear[a, b];'

   All tests passed.
