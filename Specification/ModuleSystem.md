# Module System
This document defines how code is split to multiple files, how symbols are exported and imported and how reusable units of code are defined.  
## Packages and modules
A package is a reusable unit of code that consists of modules. In C# terminology, a package is equivalent to a project. Modules are building blocks of packages, they provide or hide pieces of functionality that make up the package.
A module is any folder in a package. Every file in such folder contributes to the module. Modules can also be defined in code using the syntax `module <module name> <code block>`.
If a folder declared in package is not declared in the top level package folder, it is considered a submodule of whatever folder is its parent folder. Modules defined in code will become submodules of the module they were declared in.
Contents of a module (submodules are also contents of a module) can be accessed with the syntax `<module name>.<name of the language element to access>`.
A package is defined by file `<package name>.dracoproj`, which is a msbuild project file.
## Visibility of language elements
Langue elements can have three types of visibility in draco: `public`, `internal` and `private`.
Any language element marked as `public` will be visible outside of the package. Language elements marked as `internal` will be visible everywhere inside one package. Language elements not marked by any visibility modifier will be considered private and will be visible only inside the module they were declared in. The syntax for marking language elements visibility is `<type of visibility> <language element declaration>`.  

Example of visibility:
```c#
// Marking function as public
public func abs(x: int32): int32 = if(x > 0) x else -x;

// Member is not marked by any visibility, it is private
val PI = 3.1415;

// Marking function as internal
internal func CircleCircumference(r: int32) = 2 * PI * r;
```
## Importing language elements
Importing language elements into local scope can be done with the `import` statement. The import statement can have two forms. You can either import a module, such import statement has the syntax `import <path to module>`. In this case, the import statement brings every member of that module into scope. You can also import a single module member while aliasing that member, then only that member is brought into the scope and can be used under its aliased name. The syntax for this import is `import <alias name> = <path to module member>`. Import statements must always be on the top of a scope. If an import in a parent scope imports a language element with the same name as an import in a child scope, the element imported in the child scope will be used.

Example of importing:
```js
// Import every member of the module System.Console
import System.Console;
// Import single type, this is different than the above example, because this type is not static, which means it is not a module
import Stream = System.IO.Stream;
// Import WriteLine from System.Console and alias it as println
import println = System.Console.WriteLine;

func main(){
  // Full name here would be System.Console.Writeline
  WriteLine();

  // Child scope
  {
    // Foo.Console also defines WriteLine
    import Foo.Console;

    // Full name here would be Foo.Console.WriteLine
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
