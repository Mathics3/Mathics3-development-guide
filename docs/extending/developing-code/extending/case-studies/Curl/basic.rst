Basic version of ``Curl`` using Rules
=====================================

Armed with information from the previous sections:

* where the function belongs: "Mathematical Operations", and
* information from WMA and SymPy on what should be implemented and how it hooks into an existing library function, and
* an implementation of Curl written in Mathics3 to guide us

we are now ready to being with a skeletal version from which we can fill out later.

"Mathematical Operations" is located in file ``mathics/builtin/vectors/math_ops.py``.

.. code-block:: python

    # TODO: Curl, Div

so we have noted the need for that here already. Good. Let's remove Curl from that list.

Basic ``Curl`` version
----------------------


At the time of writing, ``Curl`` comes after ``Cross``, and before ``Norm``.


.. code-block::

  class Curl(SympyFunction):
      """
      <url>:Curl: https://en.wikipedia.org/wiki/Curl_(mathematics)</url> (<url>:SymPy: https://docs.sympy.org/latest/modules/vector/api/vectorfunctions.html#sympy.vector.curl</url>, <url>:WMA: https://reference.wolfram.com/language/ref/Curl.html</url>)

      <dl>
        <dt>'Curl[{$f1$, $f2$}, {$x1$, $x2$}]'
        <dd>returns the curl $df2$/$dx1$ - $df1$/$dx2$

        <dt>'Curl[{$f1$, $f2$, $f3} {$x1$, $x2$, $x3}]'
        <dd>returns the curl ($df3$/$dx2$ - $df2$/$dx3$, $dx3$/$df$3 - $df3$/$dx1$, $df2$/$df1$ - $df1$/$dx2$)
      </dl>

      Two-dimensional 'Curl':

      >> Curl[{y, -x}, {x, y}]
       = -2

      >> v[x_, y_] := {Cos[x] Sin[y], Cos[y] Sin[x]}
      >> Curl[v[x, y], {x, y}]
       = 0

      Three-dimensional 'Curl':
      >> Curl[{y, -x, 2 z}, {x, y, z}]
       = {0, 0, -2}

      #> Clear[v];
      """

      attributes = A_PROTECTED
      rules = {
        "Curl[{f1_, f2_}, {x1_, x2_}]": " D[f2, x1] - D[f1, x2]",
        "Curl[{f1_, f2_, f3_}, {x1_, x2_, x3_}]": "{
           D[f3, x2] - D[f2, x3],
           D[f1, x3] - D[f3, x1],
           D[f2, x1] - D[f1, x2]
         }",
      }
      summary_text = "curl vector operator"
      sympy_name = "curl"

We implement this function entirely via rules, so we aren't making use
of the fact that this is a ``SympyFunction`` and also don't need to set class variable ``sympy_name``.

However we expect that in a more complete implementation, we would
need this to handle the form of curl that makes use of coordinates.
