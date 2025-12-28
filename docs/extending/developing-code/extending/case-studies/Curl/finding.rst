Find corresponding Sympy, SciPy, mpmath, or NumPy function
===========================================================

In the last section, we wrote ``Curl`` in Mathics3 and arranged and put
it in a place where it would get loaded.

However we only added forms for the two and three dimensional
situations. We could go further and implement the other forms as well
a SymPy search shows that ``Curl`` is a SymPy function as well:
`sympy.vector.curl
<https://docs.sympy.org/latest/modules/vector/api/vectorfunctions.html#sympy.vector.curl>`_.

Furthermore, SymPy understands coordinate systems, which is used in
the last form.
