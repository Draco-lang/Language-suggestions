# Comments in code
This document specifies regular comments and documentation comments.
## What are comments
Comments are non-functional text written in the code that does not alter the behavior of the language element. They are simply there for human readers to explain the purpose of a code segment.
## Single line comments
Single line comments start with `//` if the `//` is not part of string and they end with newline.
## Documentation comments
Documentation comments are special type of comments, which adds description to symbol. In draco, documentation comments are writen using markdown. Documentation comments are continuous lines that start with `///` and everything that comes after `///` is interpreted as markdown. Documentation comment is added to symbol declared directly under given comment, if there is no such symbol, the documentation comment is ignored like a normal comment. Every markdown heading is a section in the documentation comment. Documentation comment is expected to be either simple description without any section, or more complex structure containing sections. There is small set of standard sections, but the user can use any section they want.  
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
/// - num1: first number to add
/// - num2: sexond number to add
/// # returns 
/// Two numbers added together
func Add(num1: int32, num2: int32): int32 = num1 + num2
```
 
