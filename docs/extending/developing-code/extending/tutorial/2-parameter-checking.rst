Checking Parameter Counts
-------------------------

.. index:: parameter count checking

In a programming language that allows for generic and extensible
arguments, what should we do when a function parameter list does not
match any of the patterns of a particular builtin function specification?

In WMA and systems with rewrite rules, often what is done is the
expression is returned unchanged. This allows users to write rewrite rules
involving patterns that are not covered by your code.

For example:

.. code-block:: mathematica

    In[1]:= Hello[] = "Hi, Rocky!";

    In[2]= Hello[]
    Out[2]= "Hi, Rocky!"

Above, in essence we've added form of *Hello* that does not need a parameter.
Instead, it provides a default value when none is given.

Pretty convenient and slick huh? Yes, but it can also lead to
confusion in trying to find out where a problem is coming from since
the definition can be spread to from many sources of definitions and
rewrite rules which might be mentioned  or part of other definitions:

.. code-block:: mathematica

    In[3]:= Rocky[Hello[]] = "Hi again, Rocky";

    In[3]= Rocky[Hello[]]
    Out[3]= "Hi again, Rocky"

Notice that the *In[3]* assignment takes place before the *In[1]*
assignment.  Here, these rewrite rules are in close proximity, but
they could be spread out over time or over different files.

To help you figure how what evaluation is rewriting, see *TraceEvaluation*.

Many times though, at least for me and other simple-minded users, when
I type a builtin-function with the wrong number of parameters, it is
helpful to get an error message rather than the expression unchanged.

Generally, I do not expecting (or hope not to expect) some other clever rewrite rule,
doing strange things.

So, it is sometimes helpful when writing a builtin function,
to specify the number of parameters that are allowed, and give a standard
message when the parameter counts do not match. This can be done setting class variables
``eval_error`` and ``expected_args`` of a builtin function derived from ``Builtin``.

Here is an example:

.. code-block:: python

  class Hello(Builtin):

        # Set checking that the number of arguments required to exactly one.
        eval_error = Builtin.generic_argument_error
        expected_args = 1


Now when I call *Hello* without any arguments you will see::

    In[4]:= Hello[]
    Hello::argx: Hello called with 0 arguments; 1 argument is expected.

The value of ``expected_args`` does not have to be an integer. It can be a range, like:

* ``range(5)`` one to four arguments
* ``range(2,4)``:  two or three arguments

or a set:

* ``{1, 3, 5}`` one, three, or five arguments
