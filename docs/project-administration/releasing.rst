=========================
Making a Mathics3 Release
=========================

.. index:: Releassing


This information is for Project administrators. It is likely to be of
little interest for most.

To perform these steps, this you'll need a project administrator
accounts and access on github, PyPI and dockerhub.


Beforehand
==========

Let folks know in advance about the release. During the release of
course, ensure no one is changing master (except the person doing the
release).

Release Order
=============

Often due to API changes, we need to release several packages at the same time.

Here is a dependency order in which Python packages can be released:

1. mathics_scanner; no Mathics3 dependencies.
2. mathics-pygments; depends on mathics_scanner.
3. Mathics3-Kernel; depends on mathics_scanner; For the docs the Mathics3 modules need to be available.
4. Mathics-Django; depends on mathics-core.
5. mathicsscript; depends on mathics-core.
6. pymathics-graph; depends on mathics-core.
7. pymathics-natlang; depends on mathics-core.
8. mathics-hello; depends on mathics-core.
9. mathics-omnibus; depends on all of the above.

Of course, some packages may not need updating, and the exact order
can be changed. For example, the order of the Graph and Natural
Language Mathics3 Modules can be swapped. Similarly, the order of
mathicsscript and mathics-django can be swapped, if both need
updating.

Also, updating ``mathics-pygments``, if it needs changing, can be done
*any* time after ``mathis-scanner`` is updated, and only if changes to scanner
cause a need to change mathics-pygments.

After a release, you may want to update ``Mathics3-development-guide``,
this document, for changes in the release process.

.. toctree::
   :maxdepth: 1

   releasing/Mathics3-scanner.rst
   releasing/mathics-pygments.rst
   releasing/Mathics3-Kernel.rst
   releasing/mathicsscript.rst
   releasing/mathics-django.rst
   releasing/Mathics3-Module-networkx.rst
   releasing/Mathics3-Module-nltk.rst
   releasing/Mathics3-Module-hello.rst
