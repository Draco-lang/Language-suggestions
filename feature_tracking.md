<!-- Tracking issue for the feature-set -->

This document attempts to collect all aspects of a language (by some opinionated partitioning). The number of issues is becoming significant, and we need to know what we need to work on to actually get somewhere. Feel free to add aspects that are currently left out.

Legend:
- 🟢 - well-designed
- 🟡 - a lot of work is still required, but first steps are made
- 🔴 - hasn't been considered yet

## General features

 * 🟡 Variables
   * basic structure proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
   * possibility of global variables not yet mentioned
 * 🟡 Functions/methods
   * free-functions proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
   * member functions/methods proposed in the issue about [record types](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/41)
 * 🟡 Control structures
   * If-else
     * Proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
   * While-loop
     * Proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
   * For-loop
     * Not mentioned anywhere yet
   * Match
     * Proposed in the [pattern matching issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/44)
 * 🔴 Literal values
   * Not mentioned anywhere yet
 * 🟡 Operators
   * Basic, built-in operators proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
   * User-defined operators not mentioned anywhere yet
 * 🔴 Exceptions, throwing, catching
   * Not mentioned anywhere yet

## Type-system

 * 🟡 Built-in primitives
   * Many are proposed in [proposal 0.1](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/33)
   * There are quite a few left out, never mentioned (like interop primitives)
 * 🟡 Record types
   * Proposal about the [basics of record types](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/41)
 * 🔴 Discriminated unions
   * Not mentioned anywhere yet
 * 🟡 Traits
   * No exact mentions anywhere yet
   * Very much related and strong hints are in the [record types issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/41)
 * 🟡 Properties
   * Issue about possibilities, possible syntaxes [for properties](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/47)
 * 🟡 Type-inference
   * Overview is in the [proposal 0.2](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/40)
   * There is the [type inference issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/42)
 * 🟡 Generics
   * For methods, mentioned in [proposal 0.2](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/40)
   * For types, nothing is mentioned yet
 * 🟡 Nullability
   * The issue [about erasable nullables](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/24)

## Special features

 * 🔴 Asynchronous programming
   * Not mentioned anywhere yet
 * :red_circle: Generators
   * Not mentioned anywhere yet
 * 🟡 Pattern matching
   * Proposed in the [pattern matching issue](https://github.com/LanguageDev/Fresh-Language-suggestions/issues/44)

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
