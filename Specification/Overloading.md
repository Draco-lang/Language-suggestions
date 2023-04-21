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

Resolution gathers all visible overloads, and chooses the _single_ best matching overload. Best match is based on scoring the overloads.

Trivial examples that require no scoring system:

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

### Scoring

When calling an overloaded function, each overload is assigned a score, which is a vector of numbers, representing how well each argument matches the given parameter signature. 

 * If any of the elements in the score vector is 0, the overload is discarded as non-matching.
 * A vector of different dimensions is also discarded as non-matching (wrong number of arguments).
 * An overload `A` is chosen over another, when the score vector of `A` dominates the one of `B`, meaning that each element in the vector `A` paired up with the ones in `B` greater or equal. 
 For example, `(2, 3, 4)` dominates `(2, 1, 0)`, as `2 >= 2`, `3 >= 1` and `4 >= 0`. There can be cases where neither vectors dominate each other: `(1, 2, 1)` and `(2, 1, 2)` for example.
 * Two methods can dominate each-other by having the exact same overload scores.
 * A final, unambiguous overload is only chosen, when there is an overload with a score that dominates all other scores, but is not dominated by any of the other scores.
 * If by the end of resolution there are no non-discarded overloads remaining, the call causes a resolution error for no matching overloads found.
 * If by the end of resolution there are multiple non-discarded overloads remaining, the call causes a resolution error for ambiguous overloading.

The following rules determine for the score for a single argument:
 * Each argument starts with a unitary score (`1`).
 * Type-incompatibility sets the score to `0`, discarding the overload.
 * A generic parameter match halves the score.

Examples:

```swift
func identity<T>(x: T): T = x; // Overload 1
func identity(x: int32): int32 = x; // Overload 2

identity(true);
// # Overload 1
// - Overload 1 receives the score vector (1)
// - There is no type incompatibility
// - The argument matches a generic parameter, so the score is halved to (0.5)
//
// # Overload 2
// - Overload 2 receives the score vector (1)
// - The type bool is incompatible with int32, the score is set to (0)
// - There is a 0 element in the score vector, overload is discarded
//
// There is only a single overload remaining, Overload1, that one is chosen

identity(0);
// # Overload 1
// - Overload 1 receives the score vector (1)
// - There is no type incompatibility
// - The argument matches a generic parameter, so the score is halved to (0.5)
//
// # Overload 2
// - Overload 2 receives the score vector (1)
// - There is no type incompatibility, final score is (1)
//
// There are two score vectors remaining, Overload 1 with (0.5) and Overload 2 with (1). Since (1) dominates (0.5), Overload 2 is chosen.
```

```swift
func foo<T1, T2>(a: T1, b: int32, c: T2) {} // Overload 1
func foo<T>(a: int32, b: T, c: int32) {} // Overload 2

foo(true, 1, 2);
// # Overload 1
// - Overload 1 receives the score vector (1, 1, 1)
// - There is no type incompatibility
// - Since the first and last arguments match a generic parameter, their scores are halved, ending up with (0.5, 1, 0.5)
//
// # Overload 2
// - Overload 2 receives the score vector (1, 1, 1)
// - There is a type mismatch between int32 and bool, setting the score of the first component to 0, discarding this overload
//
// There is a single overload remaining, Overload 1 with a score of (0.5, 1, 0.5) is chosen.

foo(1, true, 2);
// # Overload 1
// - Overload 1 receives the score vector (1, 1, 1)
// - There is a type mismatch between int32 and bool, discarding this overload
//
// # Overload 2
// - Overload 2 receives the score vector (1, 1, 1)
// - There are no type mismatches
// - The second argument matches a generic type, halving its score (1, 0.5, 1)
//
// There is a single overload remaining, Overload 2 with a score of (1, 0.5, 1) is chosen.

foo(1, 2, 3);
// # Overload 1
// - Overload 1 receives the score vector (1, 1, 1)
// - There are no type mismatches
// - The first and the third arguments match a type parameter, so their scores are halved (0.5, 1, 0.5)
//
// # Overload 2
// - Overload 2 receives the score vector (1, 1, 1)
// - There are no type mismatches
// - The second argument matches a generic type, halving its score (1, 0.5, 1)
//
// We have Overload 1 with score (0.5, 1, 0.5) and Overload 2 with (1, 0.5, 1). Neither dominate each other, this is an ambiguous call.

foo(true, "hello", 3);
// # Overload 1
// - Overload 1 receives the score vector (1, 1, 1)
// - There is a type mismatch between string and int32, the overload is discarded
//
// # Overload 2
// - Overload 2 receives the score vector (1, 1, 1)
// - There is a type mismatch between bool and int32, the overload is discarded
//
// We have no remaining overloads, error for no matching overload.
```
