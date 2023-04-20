# Generics

_Note: Since we don't have user-defined types or traits yet, it makes little sense to define generics on user-defined types, or constraints on generic parameters. For now, this document will omit these._

## Generic functions

Generic functions are defined using the following syntax:

```swift
func foo<T1, T2, ...>() {
    // ...
}
```

Where `T1`, `T2`, ... are the generic parameters, and can be referenced as types within the function signature and body.

### Calling generic functions

Calling a generic function can be done with explicit instantiation:

```swift
foo<TypeArg1, TypeArg2, ...>();
```

If the type arguments can be inferred from context, the type argument can be partially, or even completely elided. Partial elision can be done by using the discard `_` syntax in the place of the elided type argument. Complete elision can be done by skipping the type argument list entirely.

Examples:

```cs
func second<T, U>(a: T, b: U): U = b;

// Explicitly instantiated
second<int32, string>(12, "hello");

// Partially elided, second argument is inferred
second<int32, _>(12, "hello");

// Partially elided, both arguments are inferred
second<_, _>(12, "hello");

// Fully elided
second(12, "hello");
```

## Generic types

Generic types can be instantiated using the syntax `GenericType<TypeArg1, TypeArg2, ...>`, where `TypeArg1`, `TypeArg1`, ... are type parameters. For example, `List<T>` can be used with `int32` from `System.Collection.Generics` as `List<int32>`.
