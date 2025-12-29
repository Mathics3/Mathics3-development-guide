Running Tests
=============

Tests come in the following kinds:

* unit tests via ``pytest``
* integration tests
* tests inside examples
* integration tests
* benchmark tests
* plot tests

Benchmark testing is a topic in of itself and we don't have things fully automated here.

To run the other tests though run:

::

    $ make test  # add option -j3 if you want to run in parallel


Unit tests
----------

To run unit tests:

::

   $ make pytest

This runs ``pytest``. If you want to run pytest directly and customize options you should do this by running ``pytest`` as a module:

::

   $ python -m pytest test # pytest options

Note if you are using ``pyston`` run this way or set the ``PYTHON`` environment variable to ``pyston`` before running ``make``.


Doctests
--------

Currently we have a homegrown version of `Sphinx autodoc <https://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html>`__.
We hope to replace this someday with Sphinx autodoc. Until then, what we have is a little fragile and alas follows its own conventions.

During a test run, the results of tests can be stored for the documentation, both in MathML and LaTeX form, by executing

::

    $ python mathics/docpipeline.py -o

or

::

    make doc

You can then create the PDF using LaTeX. All required steps can be
executed by

::

    $ make latex

in the ``doc/tex`` directory, which uses ``latexmk`` to build the LaTeX
document. You just have to adjust the ``Makefile`` and ``latexmkrc`` to
your environment. You need the Asymptote to
generate the graphics in the documentation.


The version of the documentation, that is used by Django is run similarly from the Mathics3 Django repository.
interface, is updated immediately. To produce the LaTeX documentation
file, run:

You can also run the tests for individual built-in sections using

::

    $ python mathics/docpipeline.py -s [name]

The same thing can be done for chapters.

This will not re-create the corresponding documentation results,
however. You have to run a complete test to do that.

Dependencies
++++++++++++

The following packages are required to build the PDF documentation:

- ``texlive-font-utils``
- ``latexmk``
- ``texlive-xetex``
- ``lmodern``
- ``inkscape``
- ``texlive-latex-extra``
- ``texlive-latex``
- ``texlive-fonts-recommended``
- ``asymptote``

Those can be installed in Debian-based system with

::

    $ apt-get install texlive-font-utils latexmk texlive-xetex lmodern inkscape texlive-latex-extra texlive-latex texlive-fonts-recommended asymptote

Plot Output tests
------------------

Testing SVG and plot testing are implemented in ``test/builtin/drawing/test_plot_detail.py``.
These are driven by test cases described in the ``test/builtin/drawing/test_plot_detail_ref/*.yaml``
files. These test cases provide broad coverage of plotting functions.

The test cases list in the ``*.yaml`` files are executed and then
two output files are created in ``/tmp``:

* A ``.txt`` file contains a distilled outline-form version of the
  ``Graphics*`` expression produced by the plotting routine.  For
  testing stability numbers are only printed with limited precision.
  The ``NumericArray`` value is only a sampling of the array for brevity.

* The ``Graphics*`` expression is converted to SVG, and a distilled
  outline-form version is stored in a ``*.svg.txt``. This is similarly
  printed with limited precision for testing stability.

Then the actual output is compared against a reference output stored
in ``test_plot_detail_ref`` using ordinary text line-by-line diff.  The
distilled outline form of the test files makes such a comparison an
effective way to understand what has changed.

Running the tests
+++++++++++++++++

The tests can be run in the usual way with pytest, either alone or as
part of the broader pytest suite:

::

    pytest -x test_plot_detail.py

During development testing I find pytest sometimes to be a bit noisy,
so you can run the tests more directly to get more focused output:

::

    python -m test.builtin.drawing.test_plot_detail

This will produce a diff between the actual and reference files,
stopping after the first error. You can also try this:

::

    python -m test.builtin.drawing.test_plot_detail --update

The ``--update`` flag causes it to copy any actual output files in
``/tmp`` that differ to the reference directory ``test_plot_detail_ref``.

This is useful for two purposes:

* When new test cases are added you can quickly generate the reference
  files using ``--update``.

* In development mode when some code change has changed the output you
  can use the ``--update`` flag to update the reference files, and the
  use git tools to quickly examine the changes and decide whether to
  accept them by committing, or not by reverting. You might find the
  diff view offered by GitHub Desktop useful for this purpose.
