# Module System
This RFC defines how code is split to multiple files, how symbols are exported and imported and how reusable units of code are defined.  
## Packages and modules
A package is a reusable unit of code that consists of modules. In C# terminology, a package is equivalent to a project. Modules are building blocks of packages, they provide or hide pieces of functionality that make up the package. A module can be a draco file, the module name of such file being the name of this file without the file extension. A module can also be a directory in a draco project, (such module will be called a module folder). For example for file `foo.draco` the module name would be `foo` and for a folder named `bar`, the module name would be also `bar`. A module can also be defined in code using the syntax `module <module name> <code block>`.
Any draco file or a folder declared under a module folder will become a submodule of the module folder. Modules defined in code will become submodules of the module they were declared in. Module folders can declare language elements by having a file named `module.draco` under the module folder. Any code declared in this file, will become part of its parent module.
Contents of a module (submodules are also contents of a module) can be accessed with the syntax `<module name>.<name of the symbol to access>`. All modules are visible in their parent module, if they should be visible elsewhere, they need to be exported by their parent module to become visible and then they become part of the public api of the parent module. The project name will become the root module for the given project. This module will not have any code in it. For modules to be visible from the outside of the package they were declared in, they need to be exported from the root of the package.  
## Exporting symbols
Each module is responsible for exporting its API to other modules. Any symbol that should be accessible from the outside must be marked for exporting, using the `export` keyword. Symbols can be exported inline, by adding the `export` keyword before the symbol declaration or as an export list, allowing to alias symbols at the export. The syntax for exporting a list of symbols is `export <symbol list>`. `<symbol list>` can be either symbol name (submodules are also valid symbols) with optional alis using the `as` keyword with the syntax `<symbol name> as <new name>`, or as a list of such symbols encapsulated in curly braces and separated by a comma.  
Example of exporting:
```js
// Exporting members in an export list, aliasing pow
export { pow as power, abs };

// Exporting PI inline
export val PI = 3.1415;

func pow(num: int32, exp: it32): int32{
  var i = 0;
  var result = 1;
  while(i < exp){
    result = result * num;
    i = i + 1;
  }
  return result;
}

func abs(x: int32): int32 = if(x > 0) x else -x;
```
Symbols can also be re-exported using the `from` keyword with syntax `from <module name> export <symbol list>` where instead of symbol name we can also use `*` to export everything. Re-exported symbols will be propagated to the parent module. Meaning that the parent module will contain the symbols directly.  
Example of re-exporting:
```js
// Assuming this is module math

// Now, everything is accessible from math directly
from constants export { GravityConstant as G, * };
```
## Imporing symbols
Importing symbols into local scope can be done with the `import` statement. The import statement has the syntax `from <module name> import <symbol list>` where symbol list has the same syntax as as symbol list for exporting, meaning that symbols can also be aliased or you can import every symbol in module using `*`.  
Example of importing:
```js
// Import module Console from System
from System import Console;
// Import every symbol from System.Collections
from System.Collections import *
```
Example of modules:

For package Algorithms with this module structure:
```
Algorithms
|-BinarySearch.draco
|-Graphs
  |-module.draco
  |-Bfs.draco
```  
If file `Algorithms/Graphs/module.draco` conntains these contents:
```js
// Make module Bfs publicly visible
export Bfs;
```
Than importing package Algorithms to another project could look like this:
```js
// Import everything from Algorithms into local scope
from Algorithms import *
// Binary search module can be accessed directly
// Bfs module can be accessed under Graphs module like this: Graphs.Bfs
```
