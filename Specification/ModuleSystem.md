# Module System
This RFC defines how code is split to multiple files, how symbols are exported and imported and how reusable units of code are defined.  
## Packages and modules
A package is a reusable unit of code, which consists of modules. In C# terminology, package is equivalent to a project. Modules are building blocks of packages, they provide or hide pieces functionality that makes up the package, module can be draco file, module name of such file is name of this file without file extension, module can also be a directory in draco project, module name of such directory is just the folder name (such module will be called module folder), or module can be defined in code using the syntax `module <module name> <code block>`. For example for file `foo.draco` the module name would be `foo` and for folder named `bar` the module name would be also `bar`.  
Any draco file or a folder declared under module folder will become submodule of the module folder. Modules defined in code will become submodules of the module they were declared in. Module folders can group their contribution under the folder's module name by having file named `module.draco` under the module folder. Any code declared in this file, will be visible in module of its module folder.  
Contents of module (submodules are also contents of module) can be accessed with the syntax `<module name>.<name of symbol to access>`. All modules are visible in their parent module, if they should be visible elsewhere, they need to be exported by their parent module to become visible and then they become part of the public api of the parent module. Project name will become root module for given project. This module will not have any code in it. For modules to be visible from outside package they were declared in, they need to be exported from the root of the package.  
## Exporting symbols
Each module is responsible for exporting their own API towards other modules. Any symbol that should be accessible from the outside must be marked for exporting, using the `export` keyword. Symbols can be exported inline, by adding the `export` keyword before the symbol declaration or as export list, allowning to alias symbols at the export. The syntax for exporting list of symbols is `export <symbol list>`, where `<symbol list>` can be ither symbol name (submodules are also valid symbols) with optional alis using the `as` keyword with the syntax `<symbol name> as <new name>`, or as list of such symbols encapsulated in curly braces and separated by comma.  
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
Importing symbols into local scope can be done with the `import` statement, that has syntax `from <module name> import <symbol list>` where symbol list has the same syntax as as symbol list for exporting, meaning that symbols can also be aliased or you can import every symbol in module using `*`.  
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
