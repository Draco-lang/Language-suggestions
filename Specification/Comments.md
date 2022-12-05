# Comments in code
This document specifies regular comments and documentation comments.
## What are comments
Comments are non-functional text written in the code that does not alter the behavior of the language element.  
## Single line comments
Single line comments start with a `//`, if the `//` is not part of a string, and end with a newline.
## Documentation comments
Documentation comments are a special type of comments, they add a description to a language element. In draco, documentation comments are written using markdown. Documentation comments are continuous lines that start with a `///`, and everything that comes after the `///` is interpreted as markdown. The documentation comment is attached to the first language element declared under it. If there is no such element, the documentation comment is ignored like a normal comment. Linking to other language elements can be done using the regular markdown syntax `[<Text that should be displayed>](<Path to the language element>)`.  
The documentation comment is in the same scope as the language element the comment is attached to. If the language element exposes any language elements the comment could refer to, these elements are implicitly accessible for the comment without any further qualifications. This is the case for function definitions and their parameters. For example, if we use function `Abs` with one parameter `num`, this parameter can be accessed like `[num](num)`.
Every markdown heading is a section in the documentation comment. Documentation comment is expected to be either a simple description without any section or a more complex structure containing sections. There is a small set of standard sections, but the user can use any section they want.  
The standard sections are:  
`summary` - general summary of what the language element represents.  
`returns` - value the language element returns (only makes sense for functions).  
`parameters` - description of the parameters the language element accepts, ither enumeration or subsections are expected here (only makes sense for functions).  
`exception` - exceptions the language element throws (only makes sense for functions).  
`example` - code example how to use the language element.  
Example of documentation comment with sections:
```js
/// # summary
/// Function, that adds 2 numbers.
/// # parameters
///  - [num1](num1): The first number to add.
///  - [num1](num1): The second number to add.
/// # returns 
/// The sum of [num1](num1) and [num2](num2).
func Add(num1: int32, num2: int32): int32 = num1 + num2;
```
The documentation generated from this comment could look something like:
<pre>
<b>summary</b>
Function, that adds 2 numbers.
<b>parameters</b>
<i><a href = "Comments.md">num1</a></i>: The first number to add.
<i><a href = "Comments.md">num2</a></i>: The second number to add.
<b>returns</b>
The sum of <a href = "Comments.md">num1</a> and <a href = "Comments.md">num2</a>.
</pre>

Example of documentation comment without sections:
```js
/// Adds [num1](num1) and [num2](num2).
func Add(num1: int32, num2: int32): int32 = num1 + num2;
```
The documentation generated from this comment could look something like:
<pre>
Adds <a href = "Comments.md">num1</a> and <a href = "Comments.md">num2</a>.
</pre>
