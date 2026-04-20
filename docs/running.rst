.. index:: running
.. _running:

Running Mathics3
================

Here we describe the ways Mathics3 can be run.


Running natively
~~~~~~~~~~~~~~~~

Command-line interface
^^^^^^^^^^^^^^^^^^^^^^

If you have installed the Mathics3 core only you can start the
rudimentary command-line shell with:

.. codeblock:: bash

    $ mathics3

::

    $ mathics3 --help

However note that the above CLI is pretty minimal and will stay
that way.

Full Command-line Shell
^^^^^^^^^^^^^^^^^^^^^^^

The more complete command-line shell is
`mathicsscript <https://github.com/Mathics3/mathicsscript>`_.

After installing, run:

.. codeblock:: bash

    $ mathicsscript

To get a list of options run:

::

    $ mathicsscript --help


Currently, ``mathicsscript`` requires networkx to support matplotlib
graphics for the add-on package `pymathics-natlang <https://github.com/Mathics3/pymathics_natlang>`_.

This may be unbundled in the future In the future.

Django-based-line interface
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Chances are you that instead of running a command-line interface you
will want to run the fancier Django-based web server. This does support
graphics output currently (although in the future we plan to improve
that).

To start the server after Mathics3 has been installed, run:

.. codeblock:: bash

    $ Mathics3server

The first time this command is run it will create the database file for
saving your sessions. Issue

.. codeblock:: bash


    $ Mathics3server --help

to see a list of options.

You can set the used port by using the option ``-p``, as in:

.. codeblock:: bash

    $ Mathics3Server -p 8010

The default port for Mathics3 is 8000. Make sure you have the necessary
privileges to start an application that listens to this port. Otherwise,
you will have to run Mathics3 as super-user.

By default, the Web server is only reachable from your local machine. To
be able to access it from another computer, use the option ``-e``.

However, the server is only intended for local use, as it is a security
risk to run it openly on a public Web server!

If you are running from the source tree and instead want run the
webserver in a mode where if you make changes to the code, the webserver
will get restarted, we have a GNU Makefile target for doing that.

Here, run:

.. codeblock:: bash

    $ Mathics3Server

Running via docker
~~~~~~~~~~~~~~~~~~

Another way to run ``mathics`` is via `docker
<https://www.docker.com>`__ using the `Mathics3 docker image
<https://hub.docker.com/repository/docker/mathicsorg/mathics>`_ on
dockerhub.

If you do a PyPI install of the package `Mathics3 omnibus <https://pypi.org/project/Mathics-omnibus/>`_,
you will get simpler access to the docker images and a simpler way to run the various parts.

To run the command-line interface using docker image without using the Mathics3 omnibus helper script:

::

    $ docker run --rm -it --name mathics-cli -v /tmp:/usr/src/app/data mathicsorg/mathics --mode cli

However if Mathics3 omibus is installed:

::

   $ dmathicsscript

does the same thing.

If you want to add options add them at then end preceded with ``--``:
for example:

::

    $ docker run --rm -it --name mathics-cli -v /tmp:/usr/src/app/data mathicsorg/mathics --mode cli -- --help

In the above you are running ``mathicsscript`` (the enhanced CLI), not
``mathics``.

To run the Django 3.1-web interface using docker image without using the Mathics3 omnibus helper script, run :

::

    $ docker run --rm -it --name mathics-web -p 8000:8000 -v /tmp:/usr/src/app/data mathicsorg/mathics --mode ui

However the Mathics-omnibus-installed script for this simplifies this as well. So with that installed:

::

    $ dmathicsserver

does the same thing.

Consult the `docker-run
command <https://docs.docker.com/engine/reference/run/>`__ for
information about changing external port number and other for other
``docker run`` options.

Also see the previous section on security limitations.

Running Mathics3 on your server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Here we describes how to setup Mathics3 on a local network. There are
additional (security) considerations for running Mathics3 on a publicly
facing webserver.

Best practices for a local network
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Run the web server with the ``--production`` option.  Disable the
files module by setting ``ENABLE_FILES_MODULE = False`` in
``mathics/settings.py`` (otherwise remote users will be able to read
and write local files). - set an execution timeout in
``mathics/settings.py``, e.g.  ``TIMEOUT = 10`` for a 10s limit. -
Various other changes in the ``settings.py`` file like email
addresses.

You probably also want to run the server as a restricted user within a
jail shell

Running Mathics3 on a public webserver
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Warning:** You should be very careful running Mathics3 publicly, there
are some potentially large security implications to be aware of!

.. toctree::
   :maxdepth: 2


.. |Packaging status| image:: https://repology.org/badge/vertical-allrepos/mathics.svg
   :target: https://repology.org/project/mathics/versions
