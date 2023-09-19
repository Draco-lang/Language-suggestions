# Comments in code
This document specifies regular comments and documentation comments.
## What are comments
Comments are non-functional text written in the code that does not alter the behavior of the language element.  
## Single line comments
Single line comments start with a `//`, if the `//` is not part of a string, and end with a newline.
## Documentation comments
Documentation comments are a special type of comments, they add a description to a language element. In draco, documentation comments are written using markdown.
Documentation comments are continuous lines that start with a `///`, and everything that comes after the `///` is interpreted as markdown.
The documentation comment is attached to the first language element declared under it. If there is no such element, the documentation comment is ignored like a normal comment.
Linking to other language elements can be done using the regular markdown syntax, either `[Text that should be displayed](Path to the language element)` or `<language element>`, the latter being a shorthand for `[language element](language element)` to avoid repetition. Alternatively, the empty link `[Path to the language element]()` will be interpreted as `[Path to the language element](Path to the language element)` as well to be able to avoid syntactical ambiguities with the angle brackets variant. Linking to symbols using HTML links is not specified yet.
The documentation comment is in the same scope as the language element the comment is attached to. If the language element exposes any language elements the comment could refer to, these elements are implicitly accessible for the comment without any further qualifications. This is the case for function definitions and their parameters. For example, if we use function `Abs` with one parameter `num`, this parameter can be accessed like `[num](num)`.
Every markdown heading corresponds to a section in the documentation comment. Documentation comments are expected to be either a simple paragraph without any sections, or a more complex structure containing sections. A general description of the language element (in C# this would be under the `summary` tag) is expected either under the `summary` section, or at the start of the documentation without being contained in any sections. There is a small set of standard sections, but the user can use any section they want.
The standard sections are:
- `summary` - general description of the language element, if this section is not present any text at the start of the documentation that is not contained in any section is considered to be summary
- `returns` - value the language element returns (only makes sense for functions).  
- `parameters` - description of the parameters the language element accepts, either enumeration or subsections are expected here (only makes sense for functions).  
- `type parameters` - description of the type parameters the language element accepts, either enumeration or subsections are expected here (only makes sense for functions).  
- `exception` - exceptions the language element throws (only makes sense for functions).  
- `example` - code example how to use the language element.  
Example of documentation comment with sections:
```js
/// Function, that adds 2 numbers.
/// # parameters
///  - num1: The first number to add.
///  - num2: The second number to add.
/// # returns 
/// The sum of <num1> and <num2>.
func Add(num1: int32, num2: int32): int32 = num1 + num2;
```
The documentation generated from this comment could look something like:
<pre>
Function, that adds 2 numbers.
<b>parameters</b>
<i><a href = "Comments.md">num1</a></i>: The first number to add.
<i><a href = "Comments.md">num2</a></i>: The second number to add.
<b>returns</b>
The sum of <a href = "Comments.md">num1</a> and <a href = "Comments.md">num2</a>.
</pre>

Example of documentation comment without sections:
```js
/// Adds [num1]() and [num2]().
func Add(num1: int32, num2: int32): int32 = num1 + num2;
```
The documentation generated from this comment could look something like:
<pre>
Adds <a href = "Comments.md">num1</a> and <a href = "Comments.md">num2</a>.
</pre>
