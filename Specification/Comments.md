# Comments in code
This document specifies how text is commented.
## What are comments
Comment is text that is ignored by the compiler.
## Single line comments
Single line comments start with `//` if the `//` is not part of string and they end with newline.
## Documentation comments
Documentation comments are special type of comments, which adds description to symbol. In draco, documentation comments are writen using markdown. Documentation comments start with `///` and continues on next lines that also start with `///`. Documentation comment is added to symbol declared directly under given comment, if there is no such symbol, the documentation comment is ignored like a normal comment. Every markdown heading is a section in the documentation comment. Documentation comment is expected to be ither simple description without any section, or more complex structure containing sections. There is small set of standard sections, but the user can use any section they want.  
The standard sections are:  
`summary` - general summary of what the symbol represents.  
`returns` - value the symbol returns (only makes sense for functions).  
`parameters` - description of the parameters the symbol accepts, ither enumeration or subsections are expected here (only makes sense for functions).  
`exception` - exceptions the symbol throws (only makes sense for functions).  
`example` - code example how to use the symbol.  
 
