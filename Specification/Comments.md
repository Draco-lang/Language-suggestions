# Comments in code
This document specifies regular comments and documentation comments.
## What are comments
Comments are non-functional text written in the code that does not alter the behavior of the language element.  
## Single line comments
Single line comments start with `//` if the `//` is not part of string and they end with newline.
## Documentation comments
Documentation comments are special type of comments, which add description to an element. In draco, documentation comments are written using markdown. Documentation comments are continuous lines that start with `///` and everything that comes after `///` is interpreted as markdown. A documentation comment is attached to the first element declared under it, if there is no such element, the documentation comment is ignored like a normal comment. Linking to other elements can be done using the regular markdown syntax `[<Text that should be displayed>](<Path to the element>)`. 
The documentation comment is in the same scope as the element it is attached to. If the element exposes any elements the comment could refer to, these elements are implicitly accessible for the comment without any further qualifications. This can is the case for function definitions and their parameters. For example, if we use function `Abs` with one parameter `num`, this parameter can be accessed like `[num](num)`
Every markdown heading is a section in the documentation comment. Documentation comment is expected to be either simple description without any section, or more complex structure containing sections. There is small set of standard sections, but the user can use any section they want.  
The standard sections are:  
`summary` - general summary of what the symbol represents.  
`returns` - value the symbol returns (only makes sense for functions).  
`parameters` - description of the parameters the symbol accepts, ither enumeration or subsections are expected here (only makes sense for functions).  
`exception` - exceptions the symbol throws (only makes sense for functions).  
`example` - code example how to use the symbol.  
Example of documentation comment:
```js
/// # summary
/// Function that adds 2 numbers
/// # parameters
/// - [num1](num1): first number to add
/// - [num1](num1): second number to add
/// # returns 
/// Two numbers added together
func Add(num1: int32, num2: int32): int32 = num1 + num2;
```
The documentation generated from this comment could look something like:
<hr/>  

#### summary
Function that adds 2 numbers
#### parameters
- [num1](num1): first number to add
- [num1](num1): second number to add
#### returns 
Two numbers added together
