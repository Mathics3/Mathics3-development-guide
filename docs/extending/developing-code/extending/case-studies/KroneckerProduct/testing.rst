Testing ``KroneckerProduct[]``
==============================

Now that we have a complete version, we are in a position to try the tests. Recall when we added the docstring we had:

.. code-block::

    >> a = {{a11, a12}, {a21, a22}}; b = {{b11, b12}, {b21, b22}};
    >> KroneckerProduct[a, b]
     = {{a11 b11, a11 b12, a12 b11, a12 b12}, {a11 b21, a11 b22, a12 b21, a12 b22}, {a21 b11, a21 b12, a22 b11, a22 b12}, {a21 b21, a21 b22, a22 b21, a22 b22}}

    >> a = {{0, 1}, {-1, 0}}; b = {{1, 2}, {3, 4}};
    >> KroneckerProduct[a, b] // MatrixForm
     = 0    0    1   2
      .
      .  0    0    3   4
      .
     . -1   -2   0   0
     .
     . -3   -4   0   0


Now lets try entering the doctests, or lines that start out ``>>`` inside ``mathics``:

.. code-block:: bash

  $ mathics

  Mathics3 5.0.3dev0
  on CPython 3.8.12 (heads/v2.3.4.1_release:4a6b4d3504, Jun  3 2022, 15:46:12)
  ...

  In[1]:= ?KroneckerProduct
  Kronecker product
  Out[1]= Null

  In[2]:= av = Array[Subscript[a, ##] &, {2}]; bv = Array[Subscript[b, ##] &, {2}];
  Out[2]= None

  In[3]:= av
  Out[3]= {Subscript[a, 1], Subscript[a, 2]}

  In[4]:= bv
  Out[4]= {Subscript[b, 1], Subscript[b, 2]}

  In[5]:= Cases[{av, bv}, __]
  Out[5]= {{Subscript[a, 1], Subscript[a, 2]}, {Subscript[b, 1], Subscript[b, 2]}}

  In[6]:= KroneckerProduct[av, bv]
  Out[6]= Sequence[av, bv]

As we did in ``Undefined``, we can run the doctest test automatically:

.. code-block:: Bash


    $ cd Mathics3/mathics-core
    $ python mathics/docpipeline.py -s KroneckerProduct
    Testing section(s): KroneckerProduct
    b'   1 ( 0): TEST a = {{a11, a12}, {a21, a22}}; b = {{b11, b12}, {b21, b22}};'
    b'   2 ( 0): TEST KroneckerProduct[a, b]'
    b'   3 ( 0): TEST a = {{0, 1}, {-1, 0}}; b = {{1, 2}, {3, 4}};'
    b'   4 ( 0): TEST KroneckerProduct[a, b] // MatrixForm'
    b'   5 ( 0): TEST Clear[a, b];'

   All tests passed.

We should write pytest tests for error conditions and more try some
degenerate cases. If you are looking for something to do please feel
free to complete the implementation adding error cases.
