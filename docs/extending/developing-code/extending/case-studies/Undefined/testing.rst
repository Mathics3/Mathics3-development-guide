Testing ``Undefined``
=====================

Now that we have a complete version, we are in a position to try the tests. Recall when we added the docstring we had:

.. code-block::

    >> ConditionalExpression[a, False]
     = Undefined
    >> Attributes[Undefined]
     = {Protected}


The lines that start with ``>>`` are examples to show and that can be tried. The lines that start with ``=`` are the expected output.
See :ref:`Documentation Markup` for a full list and description of the homegrown markup.


Understanding the markup, we can enter that in a mathics session to verify that we get expected results:


.. code-block:: Bash


    $ mathics

    Mathics3 5.0.3dev0
    on CPython 3.8.12 (heads/v2.3.4.1_release:4a6b4d3504, Jun  3 2022, 15:46:12)
    ...

    In[1]:= ConditionalExpression[a, False]
    Out[1]= Undefined

    In[2]:= Attributes[Undefined]
    Out[2]= {Protected}

So far so good. However we can run all of these example
automatically without having to retype them as we did above.  Here is
a command that will do that:

.. code-block:: Bash


    $ cd Mathics3/mathics-core
    $ python mathics/docpipeline.py -s Undefined
    Testing section(s): Undefined
    Testing section: Integer and Number-Theoretical Functions / Undefined
    b'   1 ( 0): TEST ConditionalExpression[a, False]'
    b'   2 ( 0): TEST Attributes[Undefined]'

    All tests passed.

The ``-s`` option limits the testing just to this new section we added
called ``Undefined``.  Another useful option that is often used is
``-x``. That option modeled after the ``-x`` option in pytest: it
stops on the first error.
