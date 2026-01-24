Selecting a Builtin to Add
==========================

.. _selecting_a_builtin_to_add:

Now that we've given an example of the most basic of Builtin's, a simple "Builtin Symbol Name", we move on to Builtin Functions.

Here, the thing we mind want ot do is *find* a Builtin that hasn't been implemented.

Usually this is pretty easy, because you start out having a desire for
a specific Builtin Function or a Builtin Symbol. However if not, there are github
feature requests with the label `"New Builtin Function or Variable"
<https://github.com/Mathics3/mathics-core/labels/New%20Builtin%20Function%20or%20Variable>`_.

Another way to find a missing Builtin Function or Variable is to look for ``# TODO:`` at the to bottom of the Python code under module ``mathics.builtin``.

In a cloned git directory, you can find some of missing using ``git grep -n '^# TODO: '``:

::

   $ git grep -n '^# TODO: '

   mathics/builtin/binary/bytearray.py:78:# TODO: BaseEncode, BaseDecode, ByteArrayQ, ByteArrayToString, StringToByteArray, ImportByteArray, ExportByteArray
   mathics/builtin/binary/io.py:730:# TODO: BinaryWrite, BinaryWriteList
   mathics/builtin/binary/types.py:28:# TODO: Bit Integer8, Integer14, UnsignedInteger8, Real32, Real64, Real128, Complex64, Complex128, Complex256, Character8, Character16, TerminatedString
   mathics/builtin/directories/directory_names.py:319:# TODO: FileNameDepth, NotebookFileName
   mathics/builtin/directories/directory_operations.py:200:# TODO: CopyDirectory
     ....

Below, we have a few examples of some actual Mathics3 Builtins that we have added. Hopefully you can use this as an aid for filling in one of the many Builtin Functions, such as one of the above.


*  :ref: `Undefined`
*  :ref: `KroneckerProduct`
*  :ref: `Curl`


As we added these Builtins, we recorded the steps that were taken. We ordered the list above to go from the simple to more advanced.


See also :ref:`Tutorial: Adding a new Mathics3 Function`.
