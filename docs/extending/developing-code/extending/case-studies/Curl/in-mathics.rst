Writing Curl in Mathics
=======================

.. contents::
   :depth: 1
   :local:

Mathics3 is extensible; it allows creation of new functions, and new builtin functions all using the Mathics3 language.

So before writing this in Python or in SymPy, let us see how to do this
in Mathics3 itself.  We are only going to try the first two forms, curl
in two and three dimensions.

*Note:* by the time you are reading this ``Curl`` has already been
 added to Mathics3. So we will instead define a "builtin" function ``MCurl`` (Mathics
 Curl) so that we don't have function name clashes.

Two-dimensional Mathematical Definition
---------------------------------------

Curl is defined as:

.. math::

    \partial f_2 / \partial x_1 - \partial f_1 / \partial x_2

for two-dimensional vectors.

Two-dimensional Mathics3 Function
---------------------------------

Translating the above definition into Mathics:

.. code:: mathematica

   MCurl[{f1_, f2_}, {x1_, x2_}] :=  D[f2, x1] - D[f1, x2]

Now let's try that inside Mathics3 using the two-dimensional example that can be found in the `WMA reference for Curl <https://reference.wolfram.com/language/ref/Curl.html>`_

.. code::

    $ mathics

    Mathics3 5.0.3dev0
    In[1]:= MCurl[{f1_, f2_}, {x1_, x2_}] :=  D[f2, x1] - D[f1, x2]
    Out[1]= None

    In[2]:= (* Test the 2D definition: *)
             MCurl[{y, -x}, {x, y}]
    Out[2]= -2

    In[3] := v[x_, y_] := {Cos[x] Sin[y], Cos[y] Sin[x]}
    Out[3]= None

    In[4] := MCurl[v[x, y], {x, y}]
    Out[4]= 0


Three-dimensional Mathematical Definition
-----------------------------------------

In three dimensions, things are a little more involved:

.. math::

    ( \partial f_3 / \partial x_2 - \partial f_2 / \partial x_3, \ \ %
      \partial f_1 / \partial x_3 - \partial f_3 / \partial x_1, \ \ %
      \partial f_2 / \partial x_1 - \partial f_1 / \partial x_2 )

Three-dimensional Mathics3 Function
-----------------------------------

Translating the above definition into Mathics:

.. code:: mathematica

    In[5] := MCurl[{f1_, f2_, f3_}, {x1_, x2_, x3_}] := {
             D[f3, x2] - D[f2, x3],
             D[f1, x3] - D[f3, x1],
             D[f2, x1] - D[f1, x2]
           }
    Out[5]= None

    In[6]:= (* An example form WMA VectorAnalysis: *)
            MCurl[{y, -x, 2 z}, {x, y, z}]
    Out[6]= {0, 0, -2}

Adding ``Curl`` as an autoloaded function
-----------------------------------------

The above code was done in an interactive session.
Below we extract the function definitions and package this.

.. code:: mathematica

    (* Two and Three dimensional Curl, taken from the Mathematical definitions *)

    Begin["System`"] (* Add definition in System` namespace *)

    (* Set Information[] or ? help *)
    MCurl::usage = "returns the curl of a two-or three-dimensional vector space";

    (* Curl in two dimensions *)
    MCurl[{f1_, f2_}, {x1_, x2_}] :=  D[f2, x1] - D[f1, x2]

    (* Curl in three dimensions *)
    MCurl[{f1_, f2_, f3_}, {x1_, x2_, x3_}] := {
         D[f3, x2] - D[f2, x3],
         D[f1, x3] - D[f3, x1],
         D[f2, x1] - D[f1, x2]
	 }

    Protect[MCurl]  (* Make sure Function cannot easily be changed *)
    End[]

Place the above code in ``mathics-core/mathics/autoload/rules/Curl.m``,
and when Mathics3 starts up, this code will be evaluated.

Testing autoloaded ``Curl`` function
------------------------------------

Now let us try ``MCurl`` in a mathics session:

.. code::

    $  mathics

    Mathics3 5.0.0
    on CPython 3.8.12 (heads/v2.3.4.1_release:4a6b4d3504, Jun  3 2022, 15:46:12)
    ...

    In[1]:= ?MCurl
    returns the curl of a two-or three-dimensional vector space
    Out[1]= Null

    In[2]:= Attributes[MCurl]
    Out[2]= {Protected}

    In[3]:= MCurl[{y, -x}, {x, y}]
    Out[3]= -2

    In[4]:= v[x_, y_] := {Cos[x] Sin[y], Cos[y] Sin[x]}
    Out[4]= None

    In[5]:= MCurl[v[x, y], {x, y}]
    Out[5]= 0

    In[6]:= MCurl[{y, -x, 2 z}, {x, y, z}]
    Out[6]= {0, 0, -2}
