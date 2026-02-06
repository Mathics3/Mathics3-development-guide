
Format and Export Expressions
-----------------------------

.. index:: Expression format and render

When the evaluation on an expression is completed, i.e. reachs its canonical
form after the successive application of evaluation rules, it must be transformed
into a representation suitable to be displayed on a front-end, or encoded
into a stream. This whole process is called *formatting*. 


Formatting consists of three main stages:

1. Expression transformations: Depending on the context and the requested Form, the expression is transformed out of its canonical form. For example, `Times[a, Power[b,-1]]` can be transformed into `Divide[a, b]` to simplify notation. This is achieved by applying `FormatValues` rules. In this stage, `FormatValues` associated with the expression are applied, followed by a new standard evaluation process. To avoid the standard evaluation process that brings the expression back to its canonical form, parts of the expression are wrapped in `HoldForm` expressions.

2. Convert to boxes: once the expression reaches its final `Form`, the expression is reduced to a boxed expression, i.e. an expression consisting on a reduced subset of WL, describing how the expression must be translated into a given output file format. This sublanguage consists of `String` objects, `List`, and symbols of the form `*Box` (as `RowBox`, `StyleBox`, etc). Box expressions can also contain options (as a sequence of `Rule` expressions) and general expressions, which are treated as literal objects. Internally, boxed expressions are stored as BoxExpressions objects. These objects contain, at the time, one or more boxed expressions, general expressions, and a set of options to control the render step.

3. Translate into a file format or front-end instructions: the final step consists of converting the boxed expression into a final stream of data (a string in plain text, text in a marked language, an image, etc). The render process is controlled mainly by the properties and capabilities of the consumer (for images, resolution, width, and height of the display, etc.; for textual output, character encoding, character width, and height of the terminal) and the options included in the Box expression. For example, when converted to plain text, a `PaneBox` is reduced to the string in its first argument, but in a graphic interface, or an image file, width and height options are taken into account to produce an image of that size.

OutputForms, PrintForms, and BoxForms
-------------------------------------

`$OutputForms` lists all the possible forms an expression can be formatted. Among them, the most straightforward representation
of the expression is the `FullForm`. The final output represents the original expression in a fully functional form,
```
In[1]:= FullForm[a+b*c+F[c, s->3]]
Out[1]//FullForm= Plus[a, Times[b, c], F[c, Rule[s, 3]]]
```
This result can be re-enter in the CLI to reproduce the original expression:
```
In[2]:= Plus[a, Times[b, c], F[c, Rule[s, 3]]]
Out[2]= a + b c + F[c, s → 3]
```
This form is mainly used for debugging purposes and is used to show errors when higher-level formatting fails.


A slightly higher-level output is the `InputForm`, where arithmetic operations are represented infix-like form:
```
In[3]:= InputForm[a+b*c+F[c, s->3]]
Out[3]//InputForm= a + b*c + F[c, s -> 3]
```
Again, the result can be copy and paste on the CLI to re-generate the expression.

In the CLI, the default format is `OutputForm`, which provides a visual, text representation of the expression:
```
In[4]:= Grid[{{a,b},{c,d}}]
Out[4]= a   b
        
        c   d

```
Unlike `FullForm` and `InputForm`, `OutputForm` is not designed to be re-entered to the CLI.
```
In[4]:= Grid[{{a,b},{c,d}}]
Out[4]= a   b
        
        c   d

In[5]:= Grid[{{a,b},{c,d}}]//InputForm
Out[5]//InputForm= Grid[{{a, b}, {c, d}}]
```
Instead, the output is similar to the pretty-print form in Sympy.

Other forms like `MathMLForm`, `TexForm` or `SympyForm` produce an output that can be interpreted
by a web browser, a LaTeX compiler or a Python interpreter, respectively.

`InputForm`, `OutputForm`, `MathMLForm`, `TexForm`, and `SympyForm` are enumerated in `$PrintForms`, and
gives a text-only representation of the expression. There are other `$OutputForms` which act as modifiers, like `BaseForm`,
`NumberForm`or `Matrixform`, which modify the way in which certain elements are shown.

On the other hand, inside `$PrintForms`, there are also forms that represent structured outputs: `StandardForm` and `TraditionalForm`.
These forms (and `FullForm`) produce nested boxed expressions. `StandardForm` and `TraditionalForm` are also listed in `$BoxForms`, which are those forms that can be used as the second argument of `MakeBoxes`.

For example, 
```
In[6] := StandardForm[Integrate[F[x],x]]
Out[6]//StandardForm= ∫⁢F[x]⁢𝑑x
```
As OutputForm, `StandardForm` and `TraditionalForm` do not produce an output that can be re-entry in the CLI, but try to reproduce a typographical representation of the expression.
```
In[7]:= ∫⁢F[x]⁢𝑑x
Syntax::sntxf: "∫" cannot be followed by "⁢F[x]⁢𝑑x" (line 1 of "[]").
```
On the other hand, internally, this structured output can be used to generate typographical representations like a LaTeX representation.

Custom PrintForms
-----------------

$PrintForms can be extended by defining custom `FormatValues`. For example
```
In[8]:=Format[MyExpr[expr_], MyCustomFormat]:= "My custom format for MyExpr[" <> ToString[expr, StandardForm]<>"]";
In[9]:=Format[MyCustomFormat[expr_]]:=Format[expr, MyCustomFormat];
```
adds `MyCustomFormat` to `$PrintForms` and `$OutputForms`.
Then, wrapping an expression in `MyCustomForm`, we get the formatted expression: 
```
In[10]:= MyCustomFormat[MyExpr[x]]

Out[10]//MyCustomFormat= My custom format for MyExpr[x]
```

Box representation
------------------

After the first stage of formatting, the conversion to Boxes converts an expression into a Box representation of the formatted expression. Box representations are built of "String" elements and `BoxExpression` elements, including

- Collections of box expressions:
   * `RowBox`: a sequence of Box elements, one at the side of the other.
   * `GridBox`: Boxes distributed in a grid.
- Wrappers
   * `StyleForm`: Fix style properties to be applied to one box expression
   * `InterpretationBox`: associate a box expression with the original expression
   * `PaneBox`: Define a block of multiline string.
   * `TagBox`: Associate a boxed expression a tag function that helps to interpret the formatted expression.   
   * `FormBox`: Associate a boxed expression with the format used to generate it.

- Graphics
   * `GraphicsBox`: contains a description of a 2D graphics
   * `Graphics3DBox`: contains a description of a 3D graphics


Here are some examples of the box expressions generated by MakeBoxes on different formats.

```
In[11]:= FullForm[Integrate[F[x],x]]//MakeBoxes//InputForm                       

Out[11]//InputForm= 
TagBox[StyleBox[RowBox[{"Integrate", "[", 
    RowBox[{RowBox[{"F", "[", "x", "]"}], ",", "x"}], "]"}], 
  ShowSpecialCharacters -> False, ShowStringCharacters -> True, 
  NumberMarks -> True], FullForm]

In[12]:= InputForm[Integrate[F[x],x]]//MakeBoxes//InputForm                      

Out[12]//InputForm= 
InterpretationBox[StyleBox["Integrate[F[x], x]", ShowStringCharacters -> True, 
  NumberMarks -> True], InputForm[Integrate[F[x], x]], Editable -> True, 
 AutoDelete -> True]

In[13]:= OutputForm[Integrate[F[x],x]]//MakeBoxes//InputForm                     

Out[13]//InputForm= 
InterpretationBox[PaneBox["\"Integrate[F[x], x]\""], Integrate[F[x], x], 
 Editable -> False]

In[14]:= StandardForm[Integrate[F[x],x]]//MakeBoxes//InputForm                   

Out[14]//InputForm= 
TagBox[FormBox[RowBox[{"∫", RowBox[{RowBox[{"F", "[", "x", "]"}], 
      RowBox[{"\[DifferentialD]", "x"}]}]}], StandardForm], StandardForm, 
 Editable -> True]
```


In Mathics3, BoxExpressions are treated as Literal objects, which can be recast to normal expressions by calling the method `to_expression`. Functions that translate formatted expressions into different text and file formats consume `String` and `BoxExpression` objects, but not normal `Expression`s.











