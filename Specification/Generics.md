# Generics

_Note: Since we don't have user-defined types or traits yet, it makes little sense to define generics on user-defined types, or constraints on generic parameters. For now, this document will omit these._

## Generic functions

Generic functions are defined using the following syntax:

```swift
func foo<T1, T2, ...>() {
    // ...
}
```

Where `T1`, `T2`, ... are the generic parameters, and can be referenced as types within the function signature and body. Example:

```swift
//                      ok --    -- ok
//                          v    v
func foo<T1, T2, T3, T4>(x: T1): T2 {
    var z: T3; // ok
}

var w: T4; // NOT OK, outside of signature and body
```

### Calling generic functions

Calling a generic function can be done with explicit instantiation:

```swift
foo<TypeArg1, TypeArg2, ...>();
```

Here, `TypeArg1`, `TypeArg2`, ... are type arguments.

If the type arguments can be inferred from context, the type argument can be partially, or even completely elided. Partial elision can be done by using the discard `_` syntax in the place of the elided type argument. Complete elision can be done by skipping the type argument list entirely.

Examples:

```cs
func second<T, U>(a: T, b: U): U = b;

// Explicitly instantiated
second<int32, string>(12, "hello");

// Partially elided, second argument is inferred
second<int32, _>(12, "hello");

// Partially elided, first argument is inferred
second<_, string>(12, "hello");

// Partially elided, both arguments are inferred
second<_, _>(12, "hello");

// Fully elided
second(12, "hello");
```

## Generic types

Generic types can be instantiated using the syntax `GenericType<TypeArg1, TypeArg2, ...>`, where `TypeArg1`, `TypeArg1`, ... are type parameters. For example, `List<T>` can be used with `int32` from `System.Collection.Generics` as `List<int32>`.

## Syntactical ambiguities

This proposed syntax is quite familiar for C# developers, but it does mean a slight syntactical ambiguity is introduced. The following syntactical construct become ambiguous:

```
name1 < name2 > (expr)
```

Where `name1` and `name2` are (potentially qualified) identifiers, and `expr` is an arbitrary expression.

The above could be interpreted in two different ways:
 * A chained comparison between `name1`, `name2` and `expr`, where `expr` is simply a parenthesized expression.
 * A generic function call of the function `name1` with the generic argument `name2` and call argument `expr`.
