<!-- Tracking issue for the feature-set -->

This document attempts to collect all aspects of a language (by some opinionated partitioning). The number of issues is becoming significant, and we need to know what we need to work on to actually get somewhere. Feel free to add aspects that are currently left out.

Legend:
- 🟢 - well-designed
- 🟡 - a lot of work is still required, but first steps are made
- 🔴 - hasn't been considered yet

## General features

 * 🟢 Variables
   * basic structure proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
   * [The val and var issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/12)
   * Specification in [RFC0001](https://github.com/LanguageDev/Fresh-Language-suggestions/pull/55) for both global and local variables
 * 🟡 Functions/methods
   * 🟢 Free functions
     * Proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
     * Specification in [RFC0001](https://github.com/LanguageDev/Fresh-Language-suggestions/pull/55)
   * 🟡 Member functions
     * Proposed in the issue about [record types](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/41)
   * 🟢 Main Function
      * Proposed in [Main function signatures issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/63)
      * Specification in [RFC0004](https://github.com/LanguageDev/Fresh-Language-suggestions/pull/78)
 * 🟡 Control structures
   * 🟢 If-else
     * Proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
     * Specification in [RFC0001](https://github.com/LanguageDev/Fresh-Language-suggestions/pull/55)
   * 🟢 While-loop
     * Proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
     * Specification in [RFC0001](https://github.com/LanguageDev/Fresh-Language-suggestions/pull/55)
   * 🟡 For-loop
     * Proposed in [proposal 0.2](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/40)
   * 🟡 Match
     * Proposed in the [pattern matching issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/44)
   * 🟢 Goto
     * Proposed in the [goto and implicit loop labels issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/45)
     * Specification in [RFC0001](https://github.com/LanguageDev/Fresh-Language-suggestions/pull/55)
   * 🟢 Break/continue in loops
     * Proposed in the [goto and implicit loop labels issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/45) as specialized labels
     * Specification in [RFC0001](https://github.com/LanguageDev/Fresh-Language-suggestions/pull/55)
   * 🟡 Let ... in construct
     * [Proposal](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/51)
  * 🟢Literal values
    * Proposal in the [literal values issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/50)
    * [String interpolation issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/53)
    * Specification for string literals in [RFC0005](https://github.com/LanguageDev/Fresh-Language-suggestions/pull/79)
    * Specification for primitive literals in [RFC0002](https://github.com/LanguageDev/Fresh-Language-suggestions/pull/56)
 * 🟡 Operators
   * Basic, built-in operators proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
   * [Exponential operator](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/34)
   * [Implies operator](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/36)
   * [Inequality syntax](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/35)
   * [Colon as tuple construction](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/22)
   * User-defined operators not mentioned anywhere yet
   * Specification for basic operators in [RFC0002](https://github.com/LanguageDev/Fresh-Language-suggestions/pull/56)
 * 🟡 Exceptions, throwing, catching
   * [Exceptions, try-catch-finally proposal](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/54)
 * 🟡 Modules
   * [Issue about declarations and definitions](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/30)
   * The [module system issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/58)

## Type-system

 * 🟡 Built-in primitives
   * Many are proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
   * There are quite a few left out, never mentioned (like interop primitives)
   * Specification in [RFC0002](https://github.com/LanguageDev/Fresh-Language-suggestions/pull/56)
 * 🟡 Record types
   * Proposal about the [basics of record types](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/41)
 * 🟡 Discriminated unions
   * [The DUs vs inheritance issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/5)
 * 🟡 Traits
   * [Proposal](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/52)
   * [Internals](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/39)
   * Very much related and strong hints are in the [record types issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/41)
 * 🟡 Properties
   * Issue about possibilities, possible syntaxes [for properties](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/47)
 * 🟡 Type-inference
   * Overview is in the [proposal 0.2](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/40)
   * There is the [type inference issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/42)
   * The initial [inference issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/10)
 * 🟡 Overloading
   * Overview is in the [proposal 0.2](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/40)
 * 🟡 Generics
   * For methods, mentioned in [proposal 0.2](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/40)
   * For types, nothing is mentioned yet
 * 🟡 Nullability
   * The issue [about erasable nullables](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/24)
 * 🔴 Casting
   * Not mentioned anywhere yet

## Special features

 * 🔴 Asynchronous programming
   * Not mentioned anywhere yet
 * 🔴 Generators
   * Not mentioned anywhere yet
 * 🟡 Pattern matching
   * Proposed in the [pattern matching issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/44)
   * [Case-exclusion issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/9)
 * 🟡 Lambda functions
   * Discussed in the [lambdas issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/49)


## Metaprogramming

 * 🟡 Attributes
   * Briefly mentioned in the [metaprogramming issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/16)
   * Nothing exact proposed yet
 * 🟡 Derive-metchanism
   * Briefly mentioned in the [metaprogramming issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/16)
   * Nothing exact proposed yet
 * 🟡 Macro-system
   * Possibilities outlined in the [metaprogramming issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/16)
   * A slightly more detailed "wish-list" for capabilities in the [F# CEs expressed as higher-order macros issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/29)

## Other

 * 🟡 Documentation
   * [Documentation comments issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/37)
 * 🟡 Enforcing TCO
   * [tailrec proposal](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/11)
