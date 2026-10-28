.. index:: precision, accuracy, PrecisionReal

======================
Precision and Accuracy
======================

As in the Wolfram Language, every arbitrary-precision number in
Mathics3 carries its error, and every computation works out the error
of its result from the errors of its inputs. How this looks to a user
is described in the section "Precision and Accuracy" of the Mathics3
Manual (``mathics/doc/documentation/1-Manual.mdoc``). This page
describes how it is implemented.

The aim throughout is to give the same precision and accuracy as WMA.
The tests that check this are in ``test/builtin/atomic/test_numbers.py``
(``test_real_valued_precision``, ``test_power_real_valued_precision``,
``test_trig_real_valued_precision``, ``test_complex_precision``,
``test_log_zero_precision`` and others), and their expected values
come from WMA 15. When you change this code, take new expected values
from WMA, not from what Mathics3 gives.

Precision is the relative error in decimal digits, ``-Log10`` of the
relative error, and accuracy is the absolute error in decimal digits.
So for a nonzero ``x``, ``Precision[x] == Accuracy[x] + Log10[Abs[x]]``.

Numbers
=======

``MachineReal`` is a Python float. It has no error to track: an
expression with a machine number anywhere in it has ``MachinePrecision``
(``has_machine_number`` in ``mathics/eval/numbers/numbers.py``), and
arithmetic with a machine number gives a machine number.

``PrecisionReal`` (``mathics/core/atoms/numerics.py``) holds a
``sympy.Float``, whose precision is a whole number of binary digits.
Two slots add what WMA keeps and the ``sympy.Float`` does not:

``_precision10``
    The precision in decimal digits, which can be any real number, as
    20.3 in ``1.5`20.3``. It is ``None`` when it is the precision that the
    binary digits of the ``sympy.Float`` already give, so that most
    numbers carry nothing extra. For a zero, it is the accuracy.

``_extended``
    The value with ``MIN_STORED_BITS`` (64) bits
    (``mathics/core/number.py``), kept when the precision needs fewer.
    WMA keeps about this many bits of a low-precision number, and the
    difference shows when numbers cancel: ``1.4`2`` would otherwise be
    stored as 1.400390625.

They are read through these properties and methods:

* ``precision`` is the binary precision, as it was before these slots
  existed. Most builtins use it, and it is right for them.
* ``precision10`` is the decimal precision (or a zero's accuracy).
  ``Precision`` and ``Accuracy`` return it, and the error-tracking code
  works with it.
* ``value`` is the value rounded to the precision. ``extended_value``
  is the value with all the bits kept, and it is what the error-tracking
  code computes with.
* ``PrecisionReal.from_value(value, precision10)`` makes a number from
  an mpmath or sympy value with enough bits: it rounds the value to the
  precision and keeps the 64-bit value if that has more bits. Use it
  rather than the constructor when making a number from a computed
  value.

The object cache is keyed by the ``sympy.Float``, ``_precision10`` and
``_extended``. Two numbers that differ in any of these are different
objects, though they can still be ``SameQ`` and ``Equal``. The cache
key of ``Complex`` includes the parts' ``_precision10`` for the same
reason.

``OutputForm`` shows as many digits as the rounded precision.
``InputForm`` and ``FullForm`` show the shortest decimal string that
reads back as the kept value, followed by the stored precision, as
``3.1415926535897932385`3.``.

Where errors are worked out
===========================

Sums and products
-----------------

``eval_add_numbers`` and ``eval_multiply_numbers`` are in
``mathics/eval/numeric.py``. ``Plus`` and ``Times`` use them, and so
do ``Abs``, ``Sign`` and other builtins. When every number is exact or
a ``PrecisionReal`` (``_exact_or_precision_reals``):

* In a sum, the absolute errors add up: the accuracy of the result is
  ``-Log10[Total[10^-acc_i]]``. In a product, the relative errors add up
  in the same way. ``_combine_digits`` does this sum.
* The value is computed from ``extended_value`` with ``_stored_bits``
  bits: the most bits any input keeps, and at least 64.
* ``_precision_real`` makes the result. When no digit is left, it gives
  a zero whose accuracy is the error.
* An inexact zero counts in a sum with its absolute error. In a
  product it gives a zero (``_multiply_by_zeros``), and the size of the
  other factors goes into the zero's accuracy. An exact ``0`` times
  anything gives an exact ``0``.

Complex numbers with exact or ``PrecisionReal`` parts
(``_precision_complexes``) are added and multiplied part by part with
the same rules, so each part keeps its own error. ``Times`` multiplies
as a balanced tree, ``z^n`` squares starting from the top bit of ``n``,
and ``1/z`` is ``(a - b I)/(a^2 + b^2)``. This is the order WMA uses,
and it shows in the last digits of the precision.

Any other combination falls back to the old rule: the result gets the
lowest precision of the inputs (``min_prec``).

Exact constants
---------------

``Plus``, ``Times`` and ``Power`` first call ``numerify``
(``mathics/eval/numerify.py``), which turns exact numeric elements such
as ``E`` or ``Sqrt[2]`` into numbers. It gives them ``GUARD_DIGITS``
(16) digits more than the least precise inexact element. Their error
then vanishes next to the error of the inexact elements, so
``N[Pi, 30] E`` keeps precision 30, as in WMA. With a machine number
present, no digits are added.

Functions: the condition number
-------------------------------

The relative error of ``f[x]`` is the relative error of ``x`` times the
condition number ``Abs[x f'[x] / f[x]]``. For several arguments the
errors add up. A function opts in by giving ``MPMathFunction`` a static
method ``condition_numbers`` (``mathics/core/builtin.py``). The method
is given the arguments and then the value, all as mpmath numbers, and
returns one factor per argument. For ``Sin``:

.. code-block:: python

    @staticmethod
    def condition_numbers(x: mpmath.mpf, value: mpmath.mpf) -> tuple[mpmath.mpf]:
        return (abs(x * mpmath.cos(x) / value),)

and for ``Power``, ``(Abs[e], Abs[e Log[b]])``. The factors must stay
mpmath numbers, because a tiny factor would become ``0.`` as a float.

``eval_mpmath_function_by_condition`` (``mathics/eval/numeric.py``)
does the rest. It computes the value with the stored bits plus a few
guard bits. If the result has more digits than the inputs, as for
``Sqrt``, it computes the value again with enough bits. It takes these
class attributes into account:

``has_poles``
    When no digit is left, the result is ``ComplexInfinity``, as for
    ``Tan[1.5707963`5]``.

``value_bound``
    The error is no larger than this bound, as for ``Sin[1.`20*^30]``.

``complex_values``
    When ``True`` (the default), a complex value at real arguments is
    handled by ``_complex_value_by_parts``, described below.

Arguments that are not exact reals or ``PrecisionReal`` numbers, for
example machine numbers or complex arguments, take the older path in
``MPMathFunction.eval``: the arguments are rounded to the lowest
precision, and the function is computed at that precision.

Zero values and zero arguments
------------------------------

Where the condition number does not exist, the error is how much ``f``
changes within the error of its argument (``_change_within``). That is
the case when the value is 0, when the argument is an inexact zero
(which has no relative error), or at the end of the domain, as for
``ArcCos[1.`20]``, where ``f'[x]`` is infinite. For example,
``Cos[0``20]`` is 1 with precision 40.301, since ``Cos`` changes only by
``(10^-20)^2 / 2``.

As in WMA, the change is the leading Taylor term ``c δ^p`` evaluated at
the size of the error ``δ``, even where ``δ`` is not small. The change
is measured at ``δ 2^-40`` and at half of that, and ``p`` is read off
the two and rounded to a multiple of 1/2. ``c δ^p`` is then
extrapolated from them. Measuring at ``δ`` itself is wrong at low
precision. On each side the smaller change is taken: where ``f`` is
continuous the two sides agree, and a jump, as ``ArcCot`` has at 0, is
on one side only.

With several arguments (``_change_within_args``), each inexact argument
is varied within its own error while the others are held fixed, and
the changes add up. So ``Log[2.`20, 1.`20]`` gets its error only from
``x``.

Special points
--------------

``MPMathFunction.special_point_value(args)`` is called before anything
is computed. It returns a value where ``f`` has none of its own, or
``None`` otherwise. By default it returns ``Indeterminate`` when the error
of a single argument covers one of ``indeterminate_at``. For example,
``ArcTanh`` has ``indeterminate_at = (1, -1)``, so ``ArcTanh[1.0000001`5]``
is ``Indeterminate``. ``covers_point`` makes this test. A machine number
has no error, so it covers a point only if it is that point.

A function can override ``special_point_value``. ``Log``
(``mathics/builtin/numbers/exp.py``) does: an inexact 0 in either
argument gives ``Indeterminate``, a base that is or covers 1 gives
``ComplexInfinity`` (or ``Indeterminate`` when ``x`` is or covers 1 too),
and ``Log[b, 1]`` with an exact 1 is an exact 0.

Complex values at real arguments
--------------------------------

For ``ArcSin[2.`20]``, ``Log[-1.`20]`` and similar values,
``_complex_value_by_parts`` gives each part of the value its own error.
For each argument, the part's error is the argument's relative error
times that part of ``x f'[x]``, which is taken from a central
difference. A part that does not change with the arguments, as the
``Pi`` of ``Log[-1.`20]``, still gets ``CONSTANT_PART_ERROR`` (``2^-54``)
times ``Abs[x f'[x]]``, as in WMA. A part whose value is 0 and does not
change is an exact 0.

N
-

``N[expr, p]`` (``eval_NValues_to_precision`` in
``mathics/eval/nevaluator.py``) first evaluates at ``p``. If a nonzero
number in the result has fewer than ``p`` digits, it evaluates again
with the lost digits added. It stops at ``$MaxExtraPrecision`` (50 by
default) digits more than ``p``, and then gives ``N::meprec``. It goes
on while some number that is short of ``p`` gains digits. A number that
gains nothing comes from an input with fewer than ``p`` digits, and
keeps its precision. Finally, every number with more than ``p`` digits
is set to ``p``.

N rules see the working precision. So a retry is used only if
``_same_form`` holds: the retry must have the same form as the first
result, up to the values of its numbers. Otherwise, a rule given for
``p`` alone could be lost.

``N[expr, {p, a}]`` gives each number the precision
``min(p, a + Log10[Abs[x]])``.

SetPrecision and SetAccuracy
----------------------------

They are in ``mathics/eval/numbers/precision.py``. An inexact number
keeps its binary value, so ``SetPrecision[0.1, 20]`` shows the digits of
the double. With precision ``Infinity`` a number becomes the exact
rational it stores. The numbers are set first and the expression is
evaluated afterwards, so exact exponents stay exact.

Adding precision to a new function
==================================

For an ``MPMathFunction`` with a real value:

#. Give it ``condition_numbers``: the factors ``Abs[x D[f, x] / f]``, as
   mpmath numbers. Where ``f`` is small, use the value you are given
   rather than computing ``f`` again, so that the digits lost near a zero
   are measured at full precision.
#. Set ``has_poles`` or ``value_bound`` if they apply.
#. If an inexact argument should give ``Indeterminate`` or another value
   at some points, set ``indeterminate_at`` or override
   ``special_point_value``.
#. Take the expected values for the tests from WMA, at several points,
   near zeros and poles of the function, and for an inexact zero
   argument.

Not done yet
============

These cases still take the older path, and their precision can differ
from WMA:

* functions of a complex argument, such as ``Sqrt[1.`20 + I]`` and
  ``Exp[1.`20 I]``;
* a negative base with an inexact exponent;
* ``N`` of an exact complex number whose two parts are nonzero (WMA splits
  the precision between the parts);
* ``Power`` of an inexact zero, such as ``Sqrt[0``3]``;
* a precision below 1, as in ``Log[1.`0.5]``.
