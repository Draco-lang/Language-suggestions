# Module System
This RFC defines how code is split to multiple files, how symbols are exported and imported and how reusable units of code are defined.  
## Packages and modules
A package is a reusable unit of code, which consists of modules. In C# terminology, it is equivalent to a project that can be distributed as a NuGet package. Module is portion of code equivalent to draco file (such modules will be called just modules from now), or a directory in draco project (such modules will be called module folder). Module name is same as name of draco file without file extension, or as folder name. Modules can also be defined in code using the `module` keyword followed by name of the module and code block. Modules declared like this will become submodule of the module they were declared in. Any draco file or a folder declared under module folder will become submodule of the module folder. Module folders can group their contribution under the folder's module name by having file named `module.draco` under the module folder. Any code declared in this file, will be visible in module of its module folder. In code names of submodules are separated by `.`. Modules are not visible by default, they need to be exported by their parent module to become visible and then they become part of the public api of the parent module. Project name will become root module for given project. This module will not have any code in it. Any modules in the root folder of the project will be visible by default.  
## Exporting symbols
Each module is responsible for exporting their own API towards other modules. Any symbol that should be accessible from the outside must be marked for exporting, using the `export` keyword. Symbols can be exported inline, by adding the `export` keyword before the symbol declaration or as export list, allowning us to alias symbols at the export. The syntax for exporting list of symbols is `export <symbol list>`, where `<symbol list>` can be ither symbol name with optional alis using the `as` keyword with the syntax `<symbol name> as <new name>`, or as list of such symbols encapsulated in curly braces and separated by comma.  
Example of exporting:
```js
// Exporting members in an export list, aliasing y
export { y as second, add1 };

// Exporting x inline
export var x = 0;

var y: int32;

func add1(x: int32): int32 = x + 1;
```
Symbols can also be re-exported using the `from` keyword with syntax `from <module name> export <symbol list>` where instead of symbol list we can also use `*` to export everything.  
Example of re-exporting:
```js
// Assuming this is foo.fr

// Now, everything is accessible from foo, that was exported by bar, as is
from bar export *;

// Aliased add1, expose everything else as-is from baz
from baz export { add1 as add_one, * };
```
Syntax for exporting modules is `export <module name>`.  
**Note**: the module is only visible by its parent module before export, meaning that this is only valid at the module parent.
## Imporing symbols
Symbols can be accessed through their fully qualified name, but importing into local scope can be done with the `import` statement, that has syntax `from <module name> import <symbol list>` where symbol list has the same syntax as as symbol list for exporting, meaning that symbols can also be aliased.
