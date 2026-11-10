Glossary
========

.. glossary::

   Atom

     The elements of an :term:`M-expression` or an expression. It is an
     object that has no structure below it.  See also "Atomic
     Primitives" in the Mathics3 documentation for user-facing Mathics
     builtin-functions that work with Atoms.

     In the code there are two kinds of Atoms. Those produced by the
     parser, which get converted to Atoms used in the interpreter. The
     main difference between the two is that ``Number`` is converted into something more specific like, ``Real`` or ``Integer``.

   apply
   application

     The process by which we take an :term:`M-expression` and prepare that expression evaluation at the top-level of the expression given.

     In Mathics3 this involves looking at the head symbol (``Head[]``)
     and taking action based on information that symbols binding.

     There are distinct kinds of application: rule application, and function application.

     See also `Apply <https://en.wikipedia.org/wiki/Apply>`_.

   binding

     Associating a symbol name with a value. In Mathics3, the symbol names an expression or :term:`M-expression` and the
     values come from an definitions found in an evaluation. See also `Free and bound variables <https://en.wikipedia.org/wiki/Free_variables_and_bound_variables>`_ and
     `Name binding <https://en.wikipedia.org/wiki/Name_binding>`_.

   builtin function

     A Mathics3 function which is predefined when the system starts up. There are thousands of these in Mathics3.

   boxing

     A special type of evaluation that is kicked off by
     formatting. Mathics3 objects can have formatting or boxing rules
     associated with them. This process allows objects objects to be
     displayed in flexibile way depending on the context that the
     object appears.  See `Formatted Output <https://reference.wolfram.com/language/tutorial/TextualInputAndOutput.html#6589676>`_.

   Definition

    A Python class used which provides for a way for a :term:`Symbol` to get its value, attributes or properties.
    The mathics builtin function ``Definition`` can be used to get information about a Symbol's definition.

   DownValue

     A binding in an evaluation which can be used in a subexpression
     should one of the other binding methods, i.e. ownvalue fail.

     Mathics3 function ``DownValues`` can be used to query downvalues.

   element
   elements

     In the singular form, *element* is any node of an Expression or
     :term:`M-expression`.  The base class :ref:`BaseElement Class`
     defines properties that an element of the the expression.

     At any given level of the expression tree described in an M-expression, the
     head element or first element is a little different in that it
     represents a function or operator. So generally when we refer to
     this kind of element, we will use the more qualified term "head element".

     In the plural, *elements* when applied to a given level in an
     expression tree, we generally mean the elements other than the
     head element. Possibly a better word, might be "arguments" or
     "function arguments", since that is the role they play in a
     S-expression.

     In the code there are accessor methods ``get_elements()``,
     property ``elements`` and attribute ``_elements``.

   eval

     A functional computation. We use this in the functional
     programming sense; it is distinct from :term:`evaluate` which is more
     complex, but has *eval()* as a component of that.

     See `eval <https://en.wikipedia.org/wiki/Eval>`_.

   evaluate
   evaluation

     The process of taking an Mathics3 Expression or :term:`M-expression`
     producing a transformation or computation on that.

     It involves the distinct phases:

        * rewriting the expression, and
	* function application which performs eval()

     Note that function application can kick off another *evaluate()*,
     so this process is recursive.

   Expression

     This is both a :term:`Symbol` defined in Mathics3, and a Python class
     which implements the idea of a generalized List used in
     :term:`evaluation`. In this document we are usually referring to
     the Python class, not the built-in Symbol.

     Conceptually, an object in this class represents a sequence atoms, and (nested)
     Expressions. An expression has two parts, a ``Head`` which is expected be a function reference,
     and 0 or more :term:`elements`.

     Atoms like ``String`` or ``Integer`` are degenerate forms of
     expressions. However when we refer to the class, we are referring
     to non-degenerate or compound Expressions. In the code, both are
     forms of :ref:`BaseElement Class`.

   Form

     The top-level item in formatting. Forms formatters when
     evaluating Boxes. Some examples are ``StandardForm``,
     ``OutputForm`` ``InputForm``, and ``MathMLForm``. However there
     are many more, and the list will be growing.

   format

     The output form of Boxing when given a particular kind of Form.

   OwnValue

     A binding in an evaluation which is intended to be use across a level of an evaluation.

   literal value

     An constant value, symbol that has a constant value, or an atom that isn't a symbol. Numbers like 5,
     The Symbol ``True``, the string "goo" are all examples of literal values.

     Lists consisting of literal values are also be literal values.


   M-expression

     A structure which consists of a sequence atoms, and (nested)
     expressions. However at each level there is a ``Head`` which
     represents some sort of function.

     A M-expression is a generalization of an `S-expression
     <https://en.wikipedia.org/wiki/M-expression>`_ which is commonly
     used in Lisp and functional languages.

     While often the head element is a :term:Symbol` in some cases it can be an expression.
     For example, in ``Derivative[1][f]`` the head element is ``Derivative[1]``.

     Having the head or function-name specifier take a parmeter is
     useful in mathematics because it allows, say, the first
     derivative or second derivative to be viewed either its own
     separate parameterized function, or in the class of generic
     functions that it belongs to.  For example, a first derivative
     can then be treated more uniformly both a functions in the class
     of derivatives, or as its own separate first-derivative function, having first-derivative properties
     distinct from the derivative class of functions.

     The ``Expression`` produced by the parser is an M-expression. In
     evaluation though this pure data structure is transformed and has
     additional state which can be attached to :term:`elements` of the expression.

     See `M-expression <https://en.wikipedia.org/wiki/M-expression>`_.

   namespace

      A Context.

   NValues

     Numeric values associated with a symbol.
     It is one of the kinds of values that can be associated with a :term:`Symbol`. The others are:

       * ``Attributes``,
       * ``DefaultValues``,
       * ``FormatValues``,
       * ``Messages``
       * ``Options``
       * ``OwnValues``, and
       * ``Upvalues``

     See the documentation for the Mathics3 builtin function ``NValues``.


   pattern object

     A object found in a definition associated with a symbol an ``Expression`` or a part of the ``Expression``.
     See the documentation for Mathics3 builtin ``Pattern``.

   replacement rule

     A replacement rule is a kind of ``Rule`` that consists of a
     ``Pattern`` and a specification for how to transform the
     expression using the matching parts. Rules are said to be
     *applied* to an ``Expression`` to produce a new ``Expression``.

     For example ``F[x_Real]-> x^2`` is a rule that when applied to
     the expression ``G[F[1.], F[a]]`` produces the new expression
     ``G[1.^2, F[a]]``. Certain (internal) rules can also produce changes
     in the state of the system (writing files, printing a string, changing
     the definitions of a symbol, or setting a timeout). This happens for
     internal rules, like the associated to the pattern ``Set[a,1.]``, which
     modifies the definition of ``a`` adding the rule ``a->1``.

   rewrite

     The first phase in evaluating an expression, where an expression is rewritten based on
     attributes and rewrite rules bound to an expression's ``Head`` Symbol.

     For the general concept, see `Rewriting <https://en.wikipedia.org/wiki/Rewriting>`_.

   S-expression

     A structure which consists of a sequence atoms, and (nested)
     expressions. However at each level there is a ``Head`` which
     represents an operator or function. In Mathics3 sometimes this
     element is instead an expression that acts like a function, so
     while most expressions that Mathics3 sees are S-expressions, a few
     are in the the more general :term:`M-expression` form.

   Symbol

     A Symbolic variable. These are found in Mathics3 Expressions. The
     name of the symbol name at at any point in time and place inside
     an expression has a deifnition to a value and has other properties which
     may vary.  Some Symbols like ``True`` are constant and heir value
     and bindings can't ever change.

     In the Python code the objects in the Symbol class represent
     Symbols.

     ``Symbol[]`` is also a Mathics3 builtin-in function. In this
     document, unless otherwise specified, we are referring to the
     meaning above.

   scope

     The range of static piece of Mathics3 code that has the same Context.

   subexpression

     See :term:`element`.

   UpValue
