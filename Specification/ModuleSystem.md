# Module System
This document defines how code is split to multiple files, how symbols are exported and imported and how reusable units of code are defined.  
## Packages and modules
A package is a reusable unit of code that consists of modules. In C# terminology, a package is equivalent to a project. Modules are building blocks of packages, they provide or hide pieces of functionality that make up the package. A module is any folder in a package. Every file in such folder contributes to the module. Modules can also be defined in code using the syntax `module <module name> <code block>`.
If folder declared declared in package is not declared in the top level package folder, it is considered a submodule of whatever folder is its parent folder. Modules defined in code will become submodules of the module they were declared in.   
Contents of a module (submodules are also contents of a module) can be accessed with the syntax `<module name>.<name of the language element to access>`.
## Visibility of language elements
Langue elements can have three types of visibility in draco: `public`, `internal` and `private`.
Any language element marked as `public` will be visible outside of the package. Language elements marked as `internal` will be visible everywhere inside one package. Language elements marked as `private` will be visible only inside the module they were declared in. The default visibilty is `private`. The syntax for marking language elements visibility is `<type of visibility> <language element declaration>`.

Example of symbol visibility:
```c#
// Marking function as public
public func abs(x: int32): int32 = if(x > 0) x else -x;

// Marking member as internal
internal val PI = 3.1415;
```
## Imporing symbols
Importing symbols into local scope can be done with the `import` statement. The import statement has the syntax `import <path to module | path to module member>`. You can import module, in that case you bring every member of that module into scope, or you can also import single module member, than only the member is brought to scope. If only single module member is imported, you can alias it with the syntax `import <alias name> = <path to module member>`, in such case the module member will be useable by its new name.  
Example of importing:
```js
// Import every member of the module System.Console
import System.Console;
// Import WriteLine from System.Console and alias it as println
import println = System.Console.WriteLine;
```
