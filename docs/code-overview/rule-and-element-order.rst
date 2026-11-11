.. index:: rule order, canonical order, element_order, pattern_precedence

==============================
Order of Rules and of Elements
==============================

Mathics3 keeps two orders. They are separate, though they look alike:

* the **rule order**, the order in which the rules of a symbol are
  tried on an expression;
* the **canonical order** of elements, which ``Sort``, ``Order``,
  ``OrderedQ`` and ``Orderless`` functions use.

Both are meant to be those of WMA. The user-level description, with
examples that are checked as doctests, is in the section "Order of
Rules and of Elements" of the Mathics3 Manual
(``mathics/doc/documentation/1-Manual.mdoc``). This page describes the
WMA model, how Mathics3 implements it, and where the two still differ.

See also :ref:`Rules <rules>` and :ref:`Pattern Matching in Evaluation
<patternmatching>`.

Rule order
==========

The WMA model
-------------

WMA describes this in the tutorial `Patterns and Transformation Rules
<https://reference.wolfram.com/language/tutorial/PatternsAndTransformationRules.html>`_.
The behavior below was checked against WMA 15.

* **More specific rules come first**, whatever the order in which they
  were given. A rule for a particular value, such as ``f[1]``, comes
  before ``f[x_]``. ``f[x_Integer]`` comes before ``f[x_]``. ``f[x_]``
  comes before ``f[x__]``, which comes before ``f[x___]``. A rule with
  a condition comes before the same rule without one. ``f[{x_}]`` comes
  before ``f[x_List]``, which comes before ``f[x_]``. ``f[x_, x_]`` comes
  before ``f[x_, y_]``, and ``f[x_, y_]`` before ``f[x_, y_:1]``.
* **This is a partial order, not a total one.** When neither of two
  rules is more specific than the other, they are tried in the order in
  which they were defined. ``f[x_Integer]`` and ``f[x_Real]`` are such a
  pair, and so are ``f[x_?EvenQ]`` and ``f[x_?PrimeQ]``, and
  ``f[x_, y_Integer]`` and ``f[x_Integer, y_]``. In WMA, the order of
  definitions in a package matters for such rules.
* **A rule with the same left-hand side replaces the earlier one.** This
  applies to unconditional rules only: rules with conditions are kept
  side by side.
* **User rules for a builtin are tried before the builtin's own
  evaluation.** After ``Unprotect[Cos]; Cos[x___] := mine``, ``Cos[1.]``
  is ``mine``.

Where rules are kept
--------------------

The rules of a symbol are kept in its ``Definition``
(``mathics/core/definitions.py``), in one list per kind:
``ownvalues``, ``downvalues``, ``subvalues``, ``upvalues``, ``nvalues``,
``formatvalues`` and others. Each list is kept in the order in which
its rules are tried.

``insert_rule`` adds a rule to a list. For an unconditional rule, it
first removes an unconditional rule whose pattern is ``SameQ`` to the
new one. To find that rule without comparing against every rule in the
list, it uses an index of the unconditional rules, keyed by a
fingerprint of the pattern (``pattern_fingerprint``). The fingerprint
keeps the structure of the pattern, its integers, strings and symbols,
and puts every other atom in one bucket. Then it inserts the new rule
in the list, ordered by the rule's ``pattern_precedence`` key, with
``bisect.insort_left``.

How rules are tried
-------------------

In ``Expression.rewrite_apply_eval_step``
(``mathics/core/expression.py``), step 6, the rules are tried in this
order:

#. The upvalues of the symbols among the elements, unless the head is
   ``HoldAllComplete``. When all elements are alike
   (``elements_properties.is_uniform``), only the first one is looked at.
#. The downvalues of the head symbol, or for an expression such as
   ``f[1][x]``, the subvalues of ``f``.

The first rule whose application gives a result other than ``None`` is
used. A builtin ``eval_`` method is a ``FunctionApplyRule``, and when it
returns ``None``, that counts as not matching (see :ref:`Rules <rules>`).

``Definition.get_rules_for`` returns the rules to try. A list of
``LITERAL_INDEX_MIN_RULES`` (8) rules or more gets an index of its
literal rules (``LiteralRuleIndex``). A literal rule is an
unconditional rule whose left-hand side is made of symbols, integers
and strings, under a head that is not ``Orderless``, ``Flat`` or
``OneIdentity``: a stored value such as ``f[1] = a``. Only the literal
rule that is ``SameQ`` to the expression, if there is one, is tried,
at its own place among the other rules. So ``f[500]`` with 1000 stored
values does not try 499 other rules first. A list that is not in
``pattern_precedence`` order, for example after
``DownValues[f] = {...}``, is not indexed.

``Replace``, ``ReplaceAll``, ``ReplaceRepeated`` and ``ReplaceList`` do
not use these lists. They make a ``RewriteRule`` for each rule they are
given, and keep it on the rule expression together with
``definitions.now``. The compiled rule is reused while no symbol of the
rule has changed (``Definitions.is_uncertain_final_value``).

Where Mathics3 differs from WMA
-------------------------------

``pattern_precedence`` (see ``mathics/core/keycomparable.py`` and the
``pattern_precedence`` properties of the pattern classes) is a
**total** order: any two rules either get different keys, or the same
key. This differs from the WMA model in two ways:

* Rules that WMA cannot compare get different keys, so they are ordered
  by the key rather than by definition.
* Among rules with the same key, ``insort_left`` puts the newer rule
  first, while WMA tries the older one first.

These cases differ from WMA 15, and the result of an evaluation can
differ with them:

.. list-table::
   :header-rows: 1

   * - Definitions
     - Input
     - WMA 15
     - Mathics3
   * - ``f[x_?EvenQ] := even; f[x_?PrimeQ] := prime``
     - ``f[2]``
     - ``even``
     - ``prime``
   * - ``f[x_ /; x > 1] := a; f[x_ /; x > 0] := b``
     - ``f[2]``
     - ``a``
     - ``b``
   * - ``f[x_, y_Integer] := a; f[x_Integer, y_] := b``
     - ``f[1, 2]``
     - ``a``
     - ``b``
   * - ``f[x_Real] := r; f[x_Integer] := i``
     - ``DownValues[f]``
     - ``x_Real`` first
     - ``x_Integer`` first
   * - ``f[x_, x_] := same; f[x_, y_] := diff``
     - ``DownValues[f]``
     - ``f[x_, x_]`` first
     - ``f[x_, y_]`` first
   * - ``f[x_, y_:1] := o; f[x_, y_] := t``
     - ``DownValues[f]``
     - ``f[x_, y_]`` first
     - ``f[x_, y_:1]`` first

Issue `#1978 <https://github.com/Mathics3/mathics-core/issues/1978>`_
has a case from Rubi, and the draft
`#1989 <https://github.com/Mathics3/mathics-core/pull/1989>`_ replaces the
total order with a partial order by specificity, with the definition
order for rules that cannot be compared. Until #1989 is merged, the
fine structure of ``pattern_precedence`` is not described here.

Canonical order
===============

Each element has an ``element_order`` property: a tuple, compared
lexicographically. The ``KeyComparable`` mixin
(``mathics/core/keycomparable.py``) defines the Python comparison
operators from it, so ``sorted()`` on elements gives the canonical
order.

The first entry of the tuple is the class of the element, from the
``*_ELT_ORDER`` constants in ``keycomparable.py``:

.. list-table::
   :header-rows: 1

   * - Constant
     - Value
     - Elements
   * - ``BASIC_ATOM_NUMBER_ELT_ORDER``
     - 0x00
     - numbers
   * - ``BASIC_ATOM_ASSOCIATION_ELT_ORDER``
     - 0x01
     - associations
   * - ``BASIC_ATOM_STRING_ELT_ORDER``
     - 0x02
     - strings
   * - ``BASIC_ATOM_BYTEARRAY_ELT_ORDER``
     - 0x03
     - byte arrays
   * - ``LITERAL_EXPRESSION_ELT_ORDER``
     - 0x04
     - literal expressions
   * - ``BASIC_ATOM_NUMERICARRAY_ELT_ORDER``
     - 0x05
     - numeric arrays
   * - ``BASIC_NUMERIC_EXPRESSION_ELT_ORDER``
     - 0x12
     - numeric monomials
   * - ``GENERAL_NUMERIC_EXPRESSION_ELT_ORDER``
     - 0x13
     - other numeric expressions, such as ``Sqrt[2]``, and images
   * - ``BASIC_EXPRESSION_ELT_ORDER``
     - 0x22
     - symbols and monomials
   * - ``GENERAL_EXPRESSION_ELT_ORDER``
     - 0x23
     - other expressions

The rest of the tuple depends on the class:

**Numbers** are ordered by value. A ``Rational`` is compared through its
``sympy.Float``, and a ``Complex`` by its real part and then its
imaginary part.

**Strings** are ordered by ``wma_str_sort_key``: ``(s.lower(),
s.swapcase())``. That is, first without regard to case, and then, at the
first character that differs, lower case before upper case. So
``{"a", "ab", "Ab", "AB", "b", "B"}`` is sorted.

**Symbols** are ordered as monomials of degree 1 in themselves, so that a
symbol and its powers sort together. The key is made from the full name,
context included. ``Symbol.is_numeric()`` is ``False`` without an
evaluation, so a numeric constant such as ``Pi`` is ordered as a symbol:
``Sort[{Pi, Sqrt[2], E, 3}]`` is ``{3, Sqrt[2], E, Pi}``, as in WMA.

**Products and powers of symbols** (``Times`` and ``Power`` whose
elements are symbols with numeric exponents) are ordered as monomials:
``Expression.element_order`` builds a dictionary from each variable to
its exponent, and wraps it in a ``Monomial``. ``Monomial`` compares the
variables in reverse alphabetical order, because WMA treats later
symbols as the variables and earlier ones as coefficients. So
``b d`` comes before ``a c``, since ``c < d``. When the variables
agree, the exponents decide. This gives the usual order of the terms
of a polynomial: ``2 + a + b + c + c^2``.

**Other expressions** are ordered by their head, then by the number of
their elements, and then element by element. So
``{h, f[a], f[b], f[c], f[a, b], g[a]}`` is sorted.

The canonical order is used when an ``Orderless`` expression is built
(``Expression.sort``), by ``Sort`` without an ordering function, and by
``Order`` and ``OrderedQ``. The elements of an ``Orderless`` pattern
are sorted by ``pattern_precedence`` instead (``Expression.sort`` with
``pattern=True``).

Where the canonical order differs from WMA
------------------------------------------

Sums are placed differently. WMA puts a ``Plus`` with the polynomials,
among the symbols and monomials. Mathics3 has no rule for ``Plus``, so a
sum is a general expression ordered by its head. The head key includes
the context, so ``System`Plus`` comes after the ``Global``` heads:

.. code-block:: mathematica

    Sort[{f[x], 1 + x, g[x], a + b + c, h[a, b, c]}]
    (* WMA 15:   {a + b + c, 1 + x, f[x], g[x], h[a, b, c]} *)
    (* Mathics3: {f[x], g[x], h[a, b, c], 1 + x, a + b + c} *)
