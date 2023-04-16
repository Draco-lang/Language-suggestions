# Module System
This document defines how code is split to multiple files, how symbols are exported and imported and how reusable units of code are defined.  

## Packages and modules
A package is a reusable unit of code that consists of modules. In C# terminology, a package is equivalent to a project. Modules are building blocks of packages, they provide or hide pieces of functionality that make up the package.
A module is a collection of source files within a folder. Every source file in the folder contributes to the module. Modules can also be defined in code using the syntax `module <module name> <code block>`.
If a module is within another module (the folder is within another folder), it is considered a submodule of that parent module. Modules defined in code will become submodules of the module they were declared in.
Contents of a module (submodules are also contents of a module) can be accessed with the syntax `<module name>.<name of the language element to access>`.
A package is defined by the projectfile `<package name>.dracoproj`, which is an MSBuild project file with a special extension.

## Visibility of language elements
Language elements can have three types of visibility in Draco: `public`, `internal` and `private`.
Any language element marked as `public` will be visible outside of the package, meaning that consumer code can reference it. Language elements marked as `internal` will be visible everywhere inside the defining package. Language elements not marked by any visibility modifier will be considered private and will be visible only inside the module they were declared in. The syntax for marking the visibility of language elements is `<type of visibility> <language element declaration>`. A module will be visible outside of a package if it defines at least one public member. If the module doesn't define any public members, it will be only visible in the package it was declared in.  

Example of visibility modifiers:
```c#
// Marking function as public
public func abs(x: int32): int32 = if(x > 0) x else -x;

// Member is not marked by any visibility, it is private
val PI = 3.1415;

// Marking function as internal
internal func CircleCircumference(r: int32): int32 = 2 * PI * r;
```

## Importing language elements
Importing language elements into local scope can be done with the `import` statement. The import statement can have two forms. You can either import a module, such import statement has the syntax `import <path to module>;`. In this case, the import statement brings every member of that module into scope. You can also import a single module member while aliasing that member, then only that member is brought into the scope and can be used under its aliased name. The syntax for this import is `import <alias name> = <path to module member>;`. Import statements must always be on the top of a scope. If an import in a parent scope imports a language element with the same name as an import in a child scope, the element imported in the child scope will be used. Even without an import, language elements can be accessed anywhere in the code using their fully qualified name.  

Example of importing:
```js
// Import every member of the module System
// C# equivalent of this would be using System;
import System;
// Import every member of the module System.Console
// C# equivalent of this would be using static System.Console;
import System.Console;
// Import single type, this is different than the above example, because this type is not static, which means it is not a module
// C# equivalent of this would be using Stream = System.IO.Stream;
import Stream = System.IO.Stream;
// Import WriteLine from System.Console and alias it as println
// C# doesn't allow importing free functions
import println = System.Console.WriteLine;

func main(){
  // References System.Console.Writeline
  WriteLine();
  // References System.Console.Writeline
  println();

  // Child scope
  {
    // Foo.Console also defines WriteLine
    import Foo.Console;

    // References Foo.Console.WriteLine
    WriteLine();
  }
}
```

## Example of module system inside a project
Consider package Algorithms with this structure:
```
Algorithms
|-Algorithms.dracoproj
|-Graphs
  |-Dfs.draco
  |-Bfs.draco
  |-Trees
    |-BinaryTree.draco
```  
There would be two modules: `Algorithms.Graphs`, where files `Dfs.draco` and `Bfs.draco` contribute to this module, and module `Algorithms.Graphs.Trees`, where the file `BinaryTree.draco` contributes to this module.
