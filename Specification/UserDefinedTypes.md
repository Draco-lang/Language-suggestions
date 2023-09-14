# User-defined types

This document attempts to describe the datatypes that can be declared in Draco. We will attempt to summarize all "class-related features" of C#, our equivalents and how we would be able to interop with each of them, given that C# interop is an important aspect for us.

Prior issues:
 * [Traits](https://github.com/Draco-lang/Language-suggestions/issues/52)
 * [User-defined types](https://github.com/Draco-lang/Language-suggestions/issues/122)

## C# features to cover

Here is a loose list of C# features we are trying to cover that is needed for interop, or we intend to carry on because of its value regardless of the necessity (or I just mentioned for consideration). If there is anything missing, please notify under this proposal.

Data types:
 * `class` declarations
 * `struct` declarations
 * `enum` declarations
 * `record class` declarations
 * `record struct` declarations
 * `interface` declarations

Data type modifiers:
 * `sealed` class modifier
 * `partial` class/struct modifier
 * `abstract` class modifier
 * `readonly` struct modifier
 * `ref` struct modifier
 * class inheritance
 * implicit interface implementation
 * explicit interface implementation

Members:
 * fields
 * properties
 * constructors
 * methods
 * indexers
 * arithmetic/comparison operators
 * implicit conversion
 * explicit conversion

Member modifiers:
 * `static` member modifier
 * `readonly` field modifier
 * `required` property modifier
 * `abstract` property/method modifier
 * `abstract override` property/method modifier
 * `virtual` property/method modifier
 * `override` property/method modifier
 * covariant return type for methods/properties

## Our philosophy

I'd like to take the approach of the ML family of languages here and introduce two main categories of user defined types:
 * product-types: a bunch of state all present at once, like C# classes, records and structs
 * sum-types: (also known as discriminated-unions) mutually exclusive, alternative states, like C# enums or the stronger F# DUs or Rust enums

All C# types, except enums (and interfaces) fit into product-types, enums can be considered as a very limited version of sum-types.

There are certain things we can already sort of decide based on our prior philosophy:
 * Because we want strict modifiers by default, all types (where relevant) should be `sealed` by default. Openness for extension should be explicitly allowed by the user, annotating that the class is intended to be reused through inheritance.
 * Because we already have `var` and `val` differentiated, this is a good basis for when we want to consider field modifiers like `readonly`, or get-only/get-set auto-properties.
 * We are intending to make types extendable externally (note, this is not the inheritance kind of extension), similarly to C# extension methods or Rust `impl` blocks. This likely makes the `partial` modifier obsolete. Note, that the feature will likely introduce additional design considerations when considering this extension mechanism across assemblies.
 * C# enums can be shoved into a more general discriminated-union design.

## Interfaces

I believe one of the simplest places to tackle is interfaces, since there has been a lot of implications about taking heavy inspiration from Rust traits. For now I'd like to propose to leave out associated types intentionally until we have generic constraints fleshed out. That leaves the following features (straight from the traits issue):
 * Nonstatic methods/properties
 * Static methods/properties (also known as `static abstract`s in C#)
 * Default method/property implementations
 * Reference for the implementation type

An example for all features:

```rs
trait IAnimal {
    // Static property
    prop Species(): string { get; }

    // Static method
    // Reference to the implementation type through This
    func make(name: string): This;

    // Nonstatic property
    prop Age(this): int32 { get; set; }

    // Nonstatic method
    func moveTo(this, x: int32, y: int32): bool;

    // Defaulted function
    func cloneTo(this, x: int32, y: int32): This {
        val clone = This.make("my clone");
        clone.moveTo(x, y);
        return clone;
    }
}
```

Important details to note:
 * `This` is an alias within the trait to reference the type implementing this trait
 * Static members do not take an explicit `this` parameter
 * Nonstatic members take an explicit `this` parameter, implicitly typed `This`
 * Static members are accessed through `This`, though this could be made implicit
 * Nonstatic members are accessed through `this`, no implicit context like in C# or Java
 * Defaulted members can be overriden and are not hidden in the implementation type like in C#

Implementing a trait happens in a separate syntactical construct from the type. It does not even have to be in the same assembly, but either the trait, or the implementing type has to be defined in the assembly providing the implementation. Example:

```rs
implement IAnimal for Dog {
    prop Species(): string = "Dog";

    // This and Dog are interchangable once in the implementor type, This is merely an alias for Dog here
    func make(name: string): Dog = Dog(name, "Husky");

    // We could have written
    // func make(name: string): This = This(name, "Husky");

    prop Age(this): int32 {
        get = (DateTime.Now - this.birthDate).Years;
        set { this.birthDate = DateTime.Now - DateTime(value, 0, 0)}
    }

    func moveTo(this, x: int32, y: int32): bool {
        if (this.isObstructedAt(x, y)) return false;
        this.x = x;
        this.y = y;
        return true;
    }
}
```

> Note, that `this` does not need a `var` or `val` modifier. Just like any other parameter, it's implicitly a `val`.

### Implicit vs explicit implementations, defaulting

C# allows for implementing methods of a trait implicitly or exmplicitly. Furthermore, default members are implemented explicitly, making them painful to utilize well. Note that there is a reason C# does this, to avoid the diamond-inheritance problem:

```cs
interface I1
{
    public void Foo() => Console.WriteLine("Hello!");
}
interface I2
{
    public void Foo() => Console.WriteLine("Bye!");
}
struct T : I1, I2 {}

class Program
{
    static void Main(string[] args) => default(T).Foo(); // Ambiguous
}
```

In our case, we can use the same disambiguation that Rust uses. Since each trait implementation is explicit to its own block, we can disambiguate by calling the defaulted/ambiguous member as a static method of the trait: `I1.Foo(default(T))` or `I2.Foo(default(T))`.

## Product types (classes, records, structs)

The usual, general-purpose class declaration is provided under the `class` keyword. The only syntactic change is that the class is only allowed to declare **instance state** in its declaration, which means auto-properties (properties automatically backed by fields) and fields. Example:

```cs
class Foo {
    // Gettable and settable auto-prop
    // Equivalent C#: int X { get; set; }
    var X: int32;

    // Get-only auto-prop
    // Equivalent C#: int Y { get; }
    val Y: string;

    // Mutable field
    // Equivalent C#: int Z;
    field var Z: int32;

    // Readonly field
    // Equivalent C#: readonly int W;
    field val W: string;
}
```

Some things to note:
 * Within the class declaration, there can be only **instance, nonstatic state** provided, no static state is declared here.
 * Fields got a keyword, since properties are generally not a mistake to default to, and are more resilient to API changes.
 * Behavior and associated static data can be found in separate implementation blocks, similarly how one would implement a trait.

Associating behavior to this class would look like so:

```swift
implement Foo {
    // Pretty-printer for instances
    func print(this) =
        Console.WriteLine("Foo(X = \{this.X}; Y = \{this.Y}; Z = \{this.Z}; W = \{this.W})");
}
```

For value-types (structs), the class can be prefixed with the `value` keyword:

```cs
value class Color {
    field val R: uint8;
    field val G: uint8;
    field val B: uint8;
}

// Equivalent:
// struct Color {
//     byte R;
//     byte G;
//     byte B;
// }
```

TODO: Tuple-like classes

### Inheritance

While inheritance is heavily discouraged, it is unavoidable because of interop with the existing ecosystem. The proposed syntax for inheritance looks exactly like how it looks in C#:

```cs
class Derived : Base { ... }
```

While this might seem like a weird divergence from the commitment to the external trait implementation style, it is necessary. There can only be a single base class, and the base has to be known in the assembly declaring the type.

**Draco classes are `sealed` by default**, unlike C# classes, which are open to inheritance by default. Draco classes can be made open for inheritance using the `open` modifier:

```cs
class ClosedBase {}
open class OpenBase { }

// ERROR
// class Derived1 : ClosedBase {}

// Ok
class Derived2 : OpenBase {}
```

### Constructors

We'd like to push logic out from constructors. If there's significant logic involved with a types' construction, one should write a factory function. The construction logic itself should be fairly minimal. Initializer lists sort of facilitate a minimalistic construction, where the sole logic involved is copying some consistent initial state into the new instances' members. For example, constructing the above `Foo` type would look like so:

```cs
val f = Foo {
    X = 0;
    Y = "foo";
    Z = 1;
    W = "bar";
};
```

TODO: Visibility of this kind of construction?

### Calling base constructors

Calling the base class constructor can be done by assigning the special member `base` in the initializer list with one of the base constructor methods:

```cs
val d = Derived {
    base = Base(arg1, arg2, ...);
    // other member init...
};
```

TODO: This implies, that one could write something like:

```cs
val d = Derived {
    base = if (x < y) Base(arg1, arg2, ...) else Base(argx, argy, ...);
    // other member init...
};
```

Which might be something we don't want to, or simply can't support.

## Sum types (DUs, enums)

Discriminated unions take heavy inspiration from Rust enums. In general, they take the following shape:

```rs
enum Expr {
    // Stateless field, like a C# enum member
    Void;

    // Tuple-like variant
    Add(val Left: Expr, val Right: Expr);

    // Class-like variant
    Call {
        field val method: string;
        field val args: List<Expr>;
    };
}
```

The members within the enum declaration directly follow the same logic as classes, only instance state is declared. Note, that this declaration is a **reference type**, unlike enums in C# by default (see the C# equivalent later).

Behavior can be associated the same way, as for classes:

```cs
implement Expr {
    // ...
}
```

Value-type enums can be declared the same way as classes, by prefixing with `value`:

```cs
value enum Foo { ... }
```

### Variant tag, backing type

The "tag-value" (the field marking what number is assocaiated with the alternative) can be specified with `= constant`, like in C#:

```rs
enum Expr {
    Void = 1;
    Add(val Left: Expr, val Right: Expr) = 2;
    Call {
        field val method: string;
        field val args: List<Expr>;
    } = 3;
}
```

The backing tag field type can be changed in the declaration itself using the inheritance syntax:

```rs
enum Expr : int16 {
    // ...
}
```

### C# compatibility

C# enums can be declared by defining a `value enum`, where no members are compound types. Example:

```rs
value enum Color : int8 {
    Red = 1;
    Green = 2;
    Blue = 3;
}
```

The equivalent C# would be:

```cs
enum Color : int8 {
    Red = 1,
    Green = 2,
    Blue = 3,
}
```

## Properties

### Auto-properties

Auto-properties take two forms, `var Name: Type` for get-set, and `val Name: Type` for get-only properties:

```cs
var Foo: int32;
// Equivalent C#: int Foo { get; set; }

val Bar: int32;
// Equivalent C#: int Bar { get; }
```

### Custom properties

Static properties take the form (within `implement` blocks):

```cs
prop Foo(): int32 {
    get { ... }
    set { ... }
}
```

The getter needs to return `int32`, and the setter implicitly receives a `value: int32` parameter, similarly to C#.

The usual `= expr` sugar is available for both the getter and setter blocks:

```cs
prop Foo(): int32 {
    get = ...;
    set = ...;
}
```

Nonstatic properties only differ in receiving the `this` parameter in the property signature:

```cs
prop Age(this): int32 {
    get = (DateTime.Now - this.birthDate).Years;
    set { this.birthDate = DateTime.Now - DateTime(value, 0, 0)}
}
```

Computed get-only properties have sugar in the following form:

```cs
prop Age(this): int32 = (DateTime.Now - this.birthDate).Years;

// Equivalent C#:
// int Age => (DateTime.Now - this.birthDate).Years;
```

In interfaces, properties often need to annotate what accessors they have without computing anything, for this the `get`/`set` accessors are not followed by expression or block, but only by `;`:
```cs
prop Foo(): int32 { get; set; }
prop Bar(this): int32 { get; }
```

## Static state

Non-member, static data can be associated to types in the `implement` blocks. For example, adding a static instance counter field to the type `Foo`:

```cs
implement Foo {
    field var InstanceCount: int32;
}
```

## Operators

Since .NET 7, a bunch of interfaces have been introduced to model operators inside [System.Numerics](https://learn.microsoft.com/en-us/dotnet/api/system.numerics?view=net-7.0). I'd like to propose that instead of custom syntax, we'd treat operators as trait implementations:
 * `operator +`: `IAdditionOperators`
 * `operator -`: `ISubrtactionOperators`
 * ...

An example:

```swift
struct Vector2 {
    var X: int32;
    var Y: int32;
}

// NOTE: We could tackle eliminating CRTP implicitly since we have This type anyway
implement IAdditionOperators<Vector2, Vector2> for Vector2 {
    func Addition(left: This, rigt: This): This = Vector2 {
        X = left.X + right.X;
        Y = left.Y + right.Y;
    };
}
```

## Not yet considered

A section to list what this proposal hasn't considered yet (copied from the section listing the C# elements).

Data types:
 * `record class` declarations
 * `record struct` declarations

Data type modifiers:
 * `abstract` class modifier
 * `readonly` struct modifier
 * `ref` struct modifier

Members:
 * indexers
 * implicit conversion
 * explicit conversion

Member modifiers:
 * `required` property modifier
 * `abstract` property/method modifier
 * `abstract override` property/method modifier
 * `virtual` property/method modifier
 * `override` property/method modifier
 * covariant return type for methods/properties

## Elaborate examples

TODO
