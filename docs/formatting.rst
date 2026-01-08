
Format and Export Expressions
-----------------------------

.. index:: Expression format and render

When the evaluation on an expression is completed, i.e. reachs its canonical
form after the successive application of evaluation rules, it must be transformed
into a representation suitable to be displayed on a front-end, or encoded
into a stream. This whole process is called *formatting*. 


Formatting consists of three main stages:

1. Expression transformations: Depending on the context and the requested Form, the expression is transformed out of its canonical form. For example, `Times[a, Power[b,-1]]` can be transformed into `Divide[a, b]` to simplify notation. This is achieved by applying `FormatValues` rules. In this stage, `FormatValues` associated with the expression are applied, followed by a new standard evaluation process. To avoid the standard evaluation process that brings the expression back to its canonical form, parts of the expression are wrapped in `HoldForm` expressions.

2. Convert to boxes: once the expression reaches its final `Form`, the expression is reduced to a boxed expression, i.e. an expression consisting on a reduced subset of WL, describing how the expression must be render. This sublanguage consists on `String` objects, `List`, and symbols of the form `*Box` (as `RowBox`, `StyleBox`, etc). Box expressions can also contain options (as a sequence of `Rule` expressions) and general expressions, which are treated as literal objects. Internally, boxed expressions are stored as BoxExpressions objects. These objects contain, at the time, one or more boxed expressions, general expressions, and a set of options to control the render step.

3. Render into a format: the final step consists of converting the boxed expression into a final stream of data (a string in plain text, text in a marked language, an image, etc). The render process is controlled mainly by the properties and capabilities of the consumer (for images, resolution, width, and height of the display, etc; for textual output, character encoding, character width, and height of the terminal) and the options included in the Box expression. For example, when converted to plain text, a `PaneBox` is reduced to the string in its first argument, but in a graphic interface, or an image file, width and height options are taken into account to produce an image of that size.

