# Expressions and builtins

This RFC deals with expressions that are _not_ control-flow structures. Most notably, these will be literals, unary operators, binary operators, function calls, indexing, etc. The RFC does not specify user-defined operators, it only deals with the operator syntax, precedence and association. The built-in primitive types and their names will also be defined.

## Notation

The RFC will use a regex-flavored eBNF notation to avoid the confusion with pure regex, but to also avoid the [horrors of true, standard eBNF](https://dwheeler.com/essays/dont-use-iso-14977-ebnf.html).

In short, the used syntax will be:
 * `E1 | E2` [alternation]: either match the construct `E1` or `E2`
 * `E1 E2` [concatenation]: first match the construct `E1`, then `E2`
 * `E*` [0-repetition]: repeat the construct `E` 0 or more times
 * `E+` [1-repetition]: repeat the construct `E` 1 or more times
 * `E?` [optional]: the construct `E` is optionally matched
 * `[A-Z12]` [character class]: same as the [RegEx construct](https://www.regular-expressions.info/charclass.html), match a character from `A` to `Z` (inclusive) or `1` or `2`.
 * `'x'` [literal]: match the literal character `x`. Usual C-like escapes apply.
 * `'xyz'` [literal sequence]: Same as `'x'` `'y'` `'z'` (the concatenation of the individual characters)

**Note**: This RFC originally used regexes, but the significant whitespace made constructs harder to read. This is a slight improvement over regexes in the sense that for example `'a' 'b'` means the same as the regex `ab`.

## Built-in primitive types

Originally defined in the [0.1 proposal](https://github.com/Draco-lang/Language-suggestions/issues/33).

The following primitive types are supported:
 * `int8`
 * `uint8`
 * `int16`
 * `uint16`
 * `int32`
 * `uint32`
 * `int64`
 * `uint64`
 * `bool`
 * `float32`
 * `float64`
 * `unit`, which is roughly equivalent to the `void` type in C#, but is a true type in the type system, not a marker for no-return (meaning that you can for example use it as a generic parameter, or create a variable of type `unit`)
 * `unreachable`, which is a type of all expressions that take away the execution control from the evaluated expression. This is the type of all unconditional jumps. `unreachable` is always implicitly convertible to other types.

The naming of these types gets rid of the C heritage, which is very inconsistent among the C family. The slight inconsistency of `byte` and `sbyte` is also removed. The explicit sizes make sure we don't look up docs to know integer sizes.

## Literals

Originally defined in the [literal values issue](https://github.com/Draco-lang/Language-suggestions/issues/50).

**Important**: Parts of this section depend on what we end up in the type inference issue (#42). If we end up deciding that literals always have a fixed type, then we can introduce the usual suffixes for literals. I'm personally not a fan of those, so for now, this proposal assumes that we can agree on literals being specified during inference.

### Integer literals

* Decimal integers would match `[0-9]+`. Examples: `0`, `123`, `9625`
* Hexadecimal integers would match `'0x' [0-9a-fA-F]+`. Examples: `0x0`, `0xbadc0fee`, `0x2f5a`
* Binary integers would match `'0b' [01]+`. Examples: `0b0`, `0b011101`

### Boolean literals

The keywords `true` and `false`.

### Floating-point literals

They would have two forms, the normal decimal-separated form and a scientific form.

 * Decimal separated form would match `[0-9]+ '.' [0-9]+`. Examples: `0.0`, `0.123`, `25.0`, `62.73`. **Note that omitting either side completely is not enabled on purpose.**
 * Scientific notation form would match `[0-9]+ ('.' [0-9]+)? [eE] [+-]? [0-9]+`. Examples: `10E3`, `0.1e+4`, `123.345E-12`

### Escape sequences

Escaping would be the usual `\`. Escape sequences would be:
 * `\'`: Just a `'`. It does not have to be escaped in a string literal, but simplifies code-generation for the users. Since it's otherwise meaningless, it's essentially no effort to allow it in string literals. (inspired by C#)
 * `\"`: Just a `"`. It does not have to be escaped in a character literal, but simplifies code-generation for the users. Since it's otherwise meaningless, it's essentially no effort to allow it in character literals. (inspired by C#)
 * `\\`: Escapes the `\` to literally mean a `\`.
 * `'\' [0abfnrtv]`: Same as in every C-like programming language ([reference](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/strings/#string-escape-sequences))
 * Unicode escape sequences would take the form `'\u{' [0-9a-fA-F]+ '}'`, where the hex number between the braces denotes a Unicode codepoint. Examples:
   * `\u{70} = p`
   * `\u{AAAA} = ꪪ`
   * `\u{1F47D} = 👽` <br/> The rationale behind this syntax is to give a single, variable-length construct compared to C# that is unambiguous (unlike `\x`) and simpler to read (because of explicit braces). How these are encoded will depend on the text (or character) it is embedded in.

### Character literals

They are enclosed in single-quotes (`'`), like in C#. Any visible character can be inside (no control characters), or an escape sequence.

### String literals

There are two variations of string literals: single-line and multi-line strings. This is heavily inspired by the [Swift specifications](https://docs.swift.org/swift-book/LanguageGuide/StringsAndCharacters.html). The originating issue is #71.

#### Single-line string literals

Single-line string literals would start and end with double quotes and they can not span multiple lines. They also can't contain non-graphical characters. Example:

```swift
"Hello, World!"
```

They can also contain the usual escape sequences:

```swift
"Hello,\nEarth! \u{1F47D}"
```

The above is equivalent to

```
Hello,
Earth! 👽
```

#### Multi-line string literals

Multi-line string literals would start and end with 3 double-quotes. The string would start in the next line after the opening quotes and end before the line of the closing quotes. Example:

```swift
"""
Hello, World!
"""
```

Note, that this string has no newlines in it. It is equivalent to the string `"Hello, World!"`. If you want a leading or trailing newline, you can do:

```swift
"""

Hello, World!

"""
```

The placement of the ending quotes determine the amount of whitespaces cut off from each line. Example:

```swift
"""
    Lorem ipsum dolor sit amet,
    consectetur adipiscing elit,
    sed do eiusmod tempor incididunt
    ut labore et dolore magna aliqua.
"""
```

Here, nothing is cut off, the string is exactly

```
    Lorem ipsum dolor sit amet,
    consectetur adipiscing elit,
    sed do eiusmod tempor incididunt
    ut labore et dolore magna aliqua.
```

But if we indent the ending quotes, we can cut off the leading whitespace:

```swift
"""
    Lorem ipsum dolor sit amet,
    consectetur adipiscing elit,
    sed do eiusmod tempor incididunt
    ut labore et dolore magna aliqua.
    """
```

Now the string is

```
Lorem ipsum dolor sit amet,
consectetur adipiscing elit,
sed do eiusmod tempor incididunt
ut labore et dolore magna aliqua.
```

##### Breaking long lines

Breaking long lines in multiline strings can be done using a `\` at the very end of lines. Example:

```swift
"""
Hello, \
World!
"""
```

Which equals to `"Hello, World!"`. This looks similar to C-style line continuations, but this is only valid in multiline-string literals.

Note, that `#` changes the sequence here too (later section specifies what these are):

```swift
#"""
Hello, \
World!
"""#
```

The above is literally

```
Hello, \
World!
```

To have `Hello, World!`, you'd write

```swift
#"""
Hello, \#
World!
"""#
```

#### Interpolation

Interpolation introduces a new escape sequence, namely `\{`, which starts the interpolation expression until the matching `}`. For example:

```swift
"1 + 2 = \{1 + 2}"
```

Which would result in the string `1 + 2 = 3`.

#### Extended string delimeter

The escape-sequences and starting and ending sequences of string literals of both single- and multi-line strings can be changed, to make pasting literal strings easier. This is done by appending the same amount of `#` characters before the starting quotes and after the ending quotes.

For example, if we want to paste the literal string `1 + 2 = \n \{1 + 2}`, we could write it as: `#"1 + 2 = \n \{1 + 2}"#`.

Escape sequences can still be used, using the specified amount of `#` characters for the string. For example, `###"Hello,\###nWorld!"###` becomes:

```
Hello,
World!
```

This works for both single-line, and multi-line strings.

**The simplest way we could summarize the behavior, is that the number of #s modify the escape sequence:**

 * no `#` -> `\` is the escape sequence
 * `#` -> `\#` is the escape sequence
 * `##` -> `\##` is the escape sequence
 * `###` -> `\###` is the escape sequence
 * ...

Another example:

```swift
#"""
a = 5 + '\r'
Hello, World = """
  abcde e\n \n \r \u093
"""
\#u{1F47D}
"""#
```

which becomes

```
a = 5 + '\r'
Hello, World = """
  abcde e\n \n \r \u093
"""
👽
```

## Operators and precedence

Originally defined in the [0.1 proposal](https://github.com/Draco-lang/Language-suggestions/issues/33).

The following is the precedence table for the supported operators (highest precedence to lowest):

| **Operator** | **Description** | **Associativity** | **Notes** |
|---|---|:-:|---|
| expr(args...) <br/> expr[indices...] <br/> expr.member | Function call <br/> Indexing <br/> Member access | - |  |
| +expr <br/> -expr | Positive <br/> Negative |  - |  |
| expr * expr <br/> expr / expr <br/> expr mod expr <br/> expr rem expr | Multiplication <br/> Division <br/> Modulo <br/> Remainder | Left-to-right | Hopefully the keywords instead of the made up % helps disambiguate and avoid bikeshedding syntax arguments in the future. |
| expr + expr <br/> expr - expr | Addition <br/> Subtraction | Left-to-right |  |
| expr < expr <br/> expr > expr <br/> expr <= expr <br/> expr >= expr <br/> expr == expr <br/> expr != expr | Less-than<br/> Greater-than<br/> Less or equal<br/> Greater or equal<br/> Equals<br/> Not equals | Left-to-right | These operators can be chained arbitrarily, like in Python. <br/> `x < y >= z` is equivalent to `x < y and y >= z`, all expressions evaluated at most once, short-circuiting on the first falsy value. <br/> The elements in the chain can not be parenthesized, `(x < y) == (y < x)` is not equivalent to `x < y == y < x`! |
| not | Logical not |  | The placement has changed from the usual C-way. |
| and | Logical and | Left-to-right | Short-circuiting, as usual. |
| or | Logical or | Left-to-right | Short-circuiting, as usual. |
| = <br/> @= | Assignment <br/> Any compound assignment | Right-to-left | In `@=` the `@` stands for the usual symbols allowed for compound assignment: `+`, `-`, `*`, `/` |

The biggest change compared to classic C languages is that the `not` operators precedence has moved down from the usual unary prefix operators (`+` and `-`). This is also the precedence Python uses and the rationale behind it is that this should result in less parenthesizing. For example, `not x < y` would usually not make sense as `(not x) < y`, as the comparing a boolean value with relational operators don't make sense. Grouping the logical operators like this at the bottom means, that they are all right below the relational operators, already at the level where we can expect boolean values.

**Note**: If you find this odd at first, I really suggest trying it out and playing with it. Admittedly, it's a divergence from the usual C-way, but the change has made a lot of sense after trying it out for an extended amount of time. You can still comment your hate for this change afterwards, but don't skip it!
