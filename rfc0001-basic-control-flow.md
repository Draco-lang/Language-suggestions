# Basic control-flow/declarations

This RFC lays down the fundamental control-flow and declaration structures for the language. It describes both the the syntax and intended semantics in a semi-formal way. The goal is to have even just some bare-bones elements that we can agree on and build the other RFCs and proposals on.

## Function definition

This RFC introduces free-functions (functions that are not tied to types), member functions are not part of it.

### Syntax

There will be two syntax variants, the "block syntax":

```swift
func function_name(arg1: Type1, arg2: Type2, ...): ReturnType {
    // Function body
}
```

And the "inline-expression" syntax (roughly equivalent to arrow-bodied methods in C#):

```swift
func function_name(arg1: Type1, arg2: Type2, ...): ReturnType = expression;
```

The `: ReturnType` part is optional for both syntaxes.

### Semantics

Omitting the return type means that the return type is `unit` (equivalent to `void` in C#).

```swift
func foo(): int {
    return 0; // OK
}

func foo() {
    return 0; // ERROR: integer returned, declared return type was unit
}

func foo(): int = 0; // OK

func foo() = 0; // ERROR: integer returned, declared return type was unit
```

Defining a local function inside another function is allowed.

```swift
func main(): int {
    func first(a: int, b: int) = a;

    return first(1, 2); // OK
}
```

## Return

Return is a statement.

### Syntax

Return is a statement, with an optional expression to return:

```swift
return; // unit return value

return expr; // some evaluated expression return value
```

### Semantics

Return terminates the currently executed function and returns the value specified (or unit, if none was specified). If a return statement is omitted from a block-syntax function, it is assumed to return unit at the very end.

## Goto

Goto is a statement. Labels are statements (or rather, declarations).

### Syntax

The syntax for a labels is:

```cs
label_name:
```

The syntax of a goto statement is:

```cs
goto label_name;
```

### Semantics

Executing a `goto` statement means jumping the execution to the specified label. The jump can not jump into or our of the containing function (not even into contained or container functions).

## Code blocks

Code-blocks are expressions that are able to evaluate to some value.

### Syntax

```swift
{
    statement1
    statement2
    // ...
    expr
}
```

The expression at the end is optional.

### Semantics

Code-blocks introduce a new lexical scope. Is there is an expression at the end of the code block, the block evaluates to that expression. If there is no expression at the end (it ends with a statement or the block is empty), it's implicitly assumed to evaluate to unit. The evaluation of block is done by executing all statements in it in sequence, finally evaluating the expression at the end as the result.

## If-else

If-else is an expression, evaluating to some value.

### Syntax

```swift
if (cond-expr) then-expr else els-expr

if (cond-expr) then-expr
```

The else branch is optional. Note, that since blocks are expressions, something like

```swift
if (cond-expr) { ... } else { ... }
```

is also valid, it requires no further specification.

### Semantics

If the condition (`cond-expr`) evaluates to true, then `then-expr` is evaluated, else `els-expr` is evaluated. If the else branch is omitted, it is assumed to evaluate to unit. The two branches must evaluate to compatible types (see the [type-inference proposal](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/42) for what this will mean exactly).

## While loop

The while-loop is an expression, always evaluating to unit.

### Syntax

```swift
while (cond-expr) body-expr
```

Note, that since blocks are expressions, something like

```swift
while (cond-expr) { ... }
```

is also valid, it requires no further specification.

### Semantics

While the condition (`cond-expr`) evaluates to true, `body-expr` is evaluated. A while-loop always evaluates to unit, the body is simply "executed", not taken into account as the result.

The loop defines specialized labels, `break` and `continue`. If we desugar the loop into ifs and gotos, it would be equivalent to the following:

```swift
continue:
if (not cond-expr) goto break;
body-expr
break:
```

For nested loops, the innermost label simply shadows the outer ones. This means, that our `break` and `continue` statements are just `goto`s with automatically generated labels and written as:

```swift
while (...) {
    if (...) goto break;
    if (...) goto continue;
}
```

## Variables

Variables can be both function-local and global and they are statements (or rather, declarations).

### Syntax

They come in two flavors, with the keyword `var` and the keyword `val`:

```kt
var var_name: VarType = VarValue;
val var_name: VarType = VarValue;
```

Both the type specification (`: VarType`) and the value initializer (`= VarValue`) are optional elements.

### Semantics

`var` introduces a mutable, and `val` introduces an immutable variable. Local variables are order-dependent (meaning they can't be referenced before use), and global variables are order-independent. By #13, arbitrary variable shadowing is allowed, meaning that function-local variables can overwrite each other. Example:

```kt
var x = 1;
val x = x.ToString(); // From now on, this x is available and is immutable

{
    var x = 1; // Inside here, x refers to the integer
}

// Here again, the immutable string is accessible
```

The syntax gives 4 combinations:
 * Type specified, value specified: The specified values type has to be assignable to the specified type
 * Only type specified: Variable has the exact type specified
 * Only value specified: The type is inferred from the specified value and usage
 * Nothing specified: The type will be inferred from use

On inference, see [the issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/42).
