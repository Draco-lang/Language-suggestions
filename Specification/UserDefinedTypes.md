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
    // TODO: Static property

    // Static method
    // Reference to the implementation type through This
    func make(name: string): This;

    // TODO: Nonstatic property

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
