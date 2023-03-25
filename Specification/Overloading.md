# Overloading

_Note: The current specification for overloading is temporary, because the compiler supports a very simple form of overloading. Once we have a more elaborate type-system specification, overloading will be expanded, but the properties described here will not be lost completely._

## Definition

Functions can be defined with the same name, as long as they differ in at least one of the following:
 * Parameter count
 * Parameter type at the same position

This means, that overloading based on the return-type or parameter names only is illegal.

Legal overloads:
```swift
func foo() {}
func foo(x: int32) {} // OK: different number of parameters
func foo(x: string): bool = true; // OK: different parameter type
func foo(x: string, y: int32) {} // OK: different number of parameters
```

Illegal overloads:
```swift
func foo(x: int32) {}
func foo(x: int32): bool = true; // ERROR: Only differs in return-type
func foo(y: int32) {} // ERROR: Only differs in parameter name
```

### In local functions

Local functions contribute to the **same** set of overloads, as free-functions, meaning that the same rules apply to overloading with local functions, as free-functions. It is important to note, that the visibility of the local functions is of course limited to their containing function, so it is possible to define multiple local functions distributed in different free-functions, that would otherwise be illegally overloaded.

Legal overloads:

```swift
func foo(x: int32) {}

func bar() {
    // OK: Overloads the foo defined previously in this scope
    func foo(x: string) {}
}

func baz() {
    // OK: the previous foo of the same signature was in a different function, not visible from here
    func foo(x: string) {}
}
```

Illegal overloads:

```swift
func foo(x: int32) {}

func bar() {
    // ERROR: Only differs in parameter name
    func foo(y: int32) {}
}
```

## Resolution

Resolution gathers all visible overloads, and chooses the _single_ overload that has the exact parameter types, as the argument types on call-site. In case there is no such function, it is an overload resolution error.

Example:

```swift
func foo(): string = "A";
func foo(x: int32): string = "B";
func foo(x: string): string = "C";
func foo(x: int32, y: string): string = "D";

func main() {
    println(foo());      // A
    println(foo(1));     // B
    println(foo(""));    // C
    println(foo(1, "")); // D
}
```
