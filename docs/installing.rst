Installing Mathics3
===================

.. contents::
   :depth: 1
   :local:

Here we describe several ways to install Mathics3.


Mathics3 (mathics-core) Requirements
------------------------------------

The Mathics3 suite runs on Python 3.11 or later.

Underneath, the Mathics3 Kernel relies on
`SymPy <https://www.sympy.org/en/index.html>`__ which relies on
`NumPy <https://numpy.org>`__. These dependencies are
installed automatically if you use the standard Python installer
`pip <https://pip.pypa.io/en/stable/>`_.

`SciPy <https://SciPy.org/>`_ is optional. It is used for images and
provides alternative implementations for a number of builtins.

Building the PDF documentation requires a number of additional dependencies:

- ``xetex`` 3.14159265-2.6-0.999991 or greater
- ``asymptote``, 2.83 or greater
- ``ghostscript`` Version 9.56.1 or greater

Pay close attention to the version. Some OS-provided packages have
bugs in them that prevent certain images (like those involving
opacity, which is used in ``Filling``) from rendering.

OS package dependencies
+++++++++++++++++++++++

Here we only describe OS package dependencies for Mathics3. For
front-ends, ``Mathics-Frontend-django``, ``Mathics3-Frontend-CLI``, ``Mathics3-Frontend-jupyter``, or ``Mathics3-Frontend-live``, see "Install from the Mathics3 Github Organization"
for specific github repositories.

Debian/Ubuntu
~~~~~~~~~~~~~

For Debian/Ubuntu based systems, run

::

    $ sudo apt-get install python-dev libsqlite3-dev python-setuptools liblapack-dev llvm-dev

Note that asymptote version 2.83 or later and ghostscript 9.56.1 or later are required for building PDF documentation,
as stated previously.

MacOSX
~~~~~~

On Mac OS X:


::

    $ brew install sqlite3
    $ brew install llvm@18  # or llvm@19 or llvm@20

Make sure when you install llvmlite to specify the ``LLVM_CONFIG``::

    $ LLVM_CONFIG=/usr/local/Cellar/llvm@18/18.1.8/bin/llvm-config pip install llvmlite

FreeBSD
~~~~~~~

On FreeBSD:

::

    $ sudo pkg install math/py-mathics

.. |Packaging status| image:: https://repology.org/badge/vertical-allrepos/mathics.svg
			    :target: https://repology.org/project/mathics/versions

MS Windows (msys2)
~~~~~~~~~~~~~~~~~~

From https://github.com/Mathics3/mathics-core/discussions/1033#discussioncomment-9404617
::

   pacman -S --needed pactoys
   pacboy -S --needed python-llvmlite:p \
   python-mpmath:p \
   python-numpy:p \
   python-pillow:p \
   python-dateutil:p \
   python-requests:p \
   python-setuptools:p \
   python-sympy:p \
   python-pexpect:p \
   python-pytest:p \
   python-click:p \
   python-ptyprocess:p \
   python-iniconfig:p \
   python-packaging:p \
   python-pluggy:p \
   python-colorama:p \
   python-six:p \
   python-idna:p \
   python-urllib3:p \
   python-yaml:p
   make


MS Windows
~~~~~~~~~~

On Microsoft Windows:

::

      $ choco install llvm


Install from PyPI
-----------------


If you have the appropriate OS-dependent packages installed, and want to install everything in one shot,
try::

       $ pip install Mathics3-omnibus[full]

If you don't have the full suite of required OS packages `LLVM <https://llvm.org>`_, `xetex <https://en.wikipedia.org/wiki/XeTeX>`_ and several others, this installation is likely to fail.

You can try a more minimal installation using::

       $ pip install Mathics3-omnibus

*Important Note*: when installing the full suite of programs as is done in ``Mathics-omnibus``, all of the components are guaranteed to work either with everything installed via PyPI or installed using current sources. Mixing current sources with packages from PyPI may cause problems due to API changes. At some point in the far future the API will become more stable.

Additionally, note that installing ``Mathics3-omnibus`` installs the following suite of independent Mathics3 packages:

* `mathicsscript <https://pypi.org/project/mathicsscript/>`_ (``mathicsscript``)
* `Mathics3-django <https://pypi.org/project/Mathics3-Django/>`_ (``mathics_django``)
* `Mathics3-Module-networkx`__ (``pymathics.graph``),
* `Mathics3-Module-nltk`_ (``pymathics.natlang``)

.. _pymathics-graph: https://github.com/Mathics3/Mathics3-Module-networkx/
__ pymathics-graph_

These packages in turn pull in two other Mathics3 packages:

* `Mathics-Scanner <https://pypi.org/project/Mathics3-scanner/>`_ (``mathics_scanner``)
* `Mathics3 <https://pypi.org/project/Mathics3/mathics-core>`_ (``mathics``)

and possibly:

* `Mathics3-pygments <https://pypi.org/project/Mathics3/Mathics3-pygments>`_ (``Mathics3_pygments``)

Names given in parentheses correspond to the relevant package ``import`` names inside of Python. The `Mathics3-omnibus <https://pypi.org/project/Mathics3-omnibus/>`_ Python package primarily serves as a shortcut for installing all relevant Mathics packages. It's essentially equivalent to running:

       $ pip install Mathics3-Scanner # Mathics3 scanner and builtin operator definitions
       $ pip install Mathics3  # this is the core engine. It is a dependency of some of the below too
       $ pip install Mathics3-Frontend-Django[full] # web front-end with extras
       $ pip install mathicsscript[full]  # the command-line interface with extras
       $ pip install Mathics3-Module-nltk # the Natural-language Python module
       $ pip install Mathics3-Module-networkx # the Python module for working with Graphs and Networks

If initial installation fails, try running the above commands individually, and/or
remove the ``[full]`` to get a more basic installation.

Note the name "Mathics3" is for the core engine. This is the most recent release. "Mathics" has
pre-Python 3 code.

Other sections have descriptions of the various pieces, what
features they add and what dependencies are needed to run them.

If you want ``mathics-core`` to include modules that have been run through Cython, then install Cython separately::

     $ pip install cython

From docker (dockerhub)
-----------------------

As an alternative to building all the components from source or via Python
package, you can run pre-built code for *all* of the components via
`docker <https://www.docker.com>`__. To download a copy of the docker
image run:

::

    $ docker pull mathicsorg/mathics

This will pull the latest development release, which has a docker
tag name ``#latest``. If you want a stable release instead, specify the version in
the pull command. For example

    $ docker pull mathicsorg/mathics#9.0.0

From an OS-Specific Repository
------------------------------

Click on the link below from `Repology.org <https://repology.org>`__ for
details for a specific OS and distribution.

|Packaging status|

Install from the Mathics3 Github Organization
----------------------------------------------

If you need the most recent fixes, you can install them from github.  Note
that there isn't a single github repository that contains everything.

Since Mathics3 is in development, dependencies can change. The
development version might require different package dependencies than
the most recent stable release.

In particular, ``mathics-core`` uses JSON tables created by programs
and data in ``mathics-scanner``. If we change the organization of
data, particularly in ``mathics-core``,
you'll need to use the development version of ``mathics-scanner`` and
ensure that relevant JSON tables are in line with what the ``mathics-core``
development version uses.

The minimal set of Mathics3 Python packages that need to be installed is:

* `Mathics3 scanner <https://github.com/Mathics3/mathics-scanner/>`_
* `Mathics3 core <https://github.com/Mathics3/mathics-core/>`_

When ``Mathics3`` (which depends on ``Mathics3-Scanner``) is installed,
there is a minimalist command-line utility called ``mathics``
available which allows you to enter Mathics3 statements. For help on
this type::

  mathics3 --help

More sophisticated frontends are also availabe. For instance, ``mathicsscript`` is a more full
featured command-line script similar to ``wolframscript``. Alternatively, there is
Django-based front-end called ``Mathics3-django``.

There are also two Mathics3 modules written directly in Python:

* `Mathics3-Module-networkx`__
* `Mathics3-Module-nltk`__

.. _Mathics3-Module-networkx: https://github.com/Mathics3/Mathics3-Module-networkx/
__ pymathics-graph_

To build ``mathics-core``, and ``Mathics3-Frontend-django``::

  $ git clone <name-of-repository>
  $ cd <name-of-repository>
  $ make
  $ make check # to test code

Alternatively, you can use ``pip install``::

  $ git clone <name-of-repository>
  $ cd <name-of-repository>
  $ pip install -e .   # or pip install -e .[full]
  $ make check # to test code

Finally, to run the Django-based front-end type::

  $ make runserver

.. _Mathics3-Module-nltk: https://github.com/Mathics3/Mathics3-Module-nltk/
__ Mathics3-Module-nltk_
