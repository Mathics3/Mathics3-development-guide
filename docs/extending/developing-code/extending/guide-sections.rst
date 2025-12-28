.. _guide_sections_and_subsections:

Guide Sections and Subsections
==============================

Documentation for Builtins exists inside one or more documentation sections. Currently this section follows the module structure of the code.

For example, the documentation for the the ``Complex`` builtin function is in a subsection called ``Basic Arithmetic`` and that subsection is in section ``Mathematical Functions``.  In the Python module structure, ``Mathematical Functions`` is documented as the docstring of the ``mathics.builtins`` modules, inside file ``mathics/builtins/arithfns/__init__.py`` while the documentation for ``Basic Arithmetic`` is documented as the main docstring for Python module ``mathics.builtins.arithfns.basic`` in ``mathics/builtins/arithfns/basic``.

By default, the ordering of sections in the documentation is determined by the order of lexical order of the Python module names. Sometimes, this is not what is wanted. Therefore to change this, module variable ``sort_order`` can be set to specify the order used in sorting.

For example, in file ``mathics-core/mathics/builtin/box/layout.py`` we have::

    """
    Low-Level Notebook Structure

    Formatting constructs are represented as a hierarchy of low-level \
    symbolic "boxes".

    The routines here assist in boxing at the bottom of the hierarchy, typically found when using notebook.
    """

    sort_order = "mathics.builtin.low-level-notebook-structure"

Since the title of the subsection is "Low-Level Notebook Structure", not "Layout", ``sort_order`` is added so that this gets put in the same place as if the file were called ``low_level_notebook_structure.py``.
