.. _doc_markup:

Guide Sections and Subsections
==============================

Documentation for Builtins exists inside one or more documentation sections. Currently this section follows the module structure of the code.

For example, the documentation for the the ``Complex`` builtin function is in a subsection called ``Basic Arithmetic`` and that subsection is in section ``Mathematical Functions``.  In the Python module structure, ``Mathematical Functions`` is documented as the docstring of the ``mathics.builtins`` modules (inside file ``mathics/builtins/arithfns/__init__.py`` while the documentation for ``Basic Arithmetic`` is documented as the main docstring for Python module ``mathics.builtins.arithfns.basic`` in ``mathics/builtins/arithfns/basic``.


By default, the ordering of sections in the documentation is determined by the order of lexical order of the Python module names. Sometimes, this is not what is wanted. Therefore to change this, module variable ``sort_order`` can be set to specify the order used in sorting.
