# POSIX-compliant dotenv syntax overview

## Relation to POSIX

The POSIX-compliant dotenv syntax is a strict subset of the
[POSIX shell command language specification](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html).

Conforming implementations MUST follows the rules in the aforementioned specification for:
* variable assignment
* [parameter expansion](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_02)
* [quote removal](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_07)

Conforming implementations MUST apply the restrictions to the former rules
that are defined in the documents found in this repository.

In conforming implementations, the following features
MUST NOT be supported AND result in a parse error:
* [positional parameters](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_05_01)
* [special parameters](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_05_02)
* [command substitution](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_03)
* [arithmetic expansion](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_04)
* string-length expansion (`${#name}`)
* pattern-matching expansions (`${name%pattern}`, `${name%%pattern}`, `${name#pattern}`, `${name##pattern}`)

In conforming implementations, the following MUST NOT be performed:
* [tilde expansion](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_01)
* [pathname expansion](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_06)
* [field splitting](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_05)

## Syntax overview

The file encoding MUST be UTF-8.

The file MUST NOT contain null characters (U+0000 NUL).

The `<newline>` character MUST be U+000A LINEFEED.
No newline normalization shall occur.

The `<whitespace>` characters are:
* `<space>` (U+0020 SPACE).
* `<tab>` (U+0009 TAB).

A dotenv file consists of a list of [assignment expressions](#assignment-expressions),
separated by one or more `<whitespace>` and/or `<newline>` characters,
and optionally preceded or followed by any number of [comment](#comments) expressions.


## Assignment expressions

An assignment expression consists of:
* an [identifier](#identifiers),
* followed by a `=` (U+003D EQUALS SIGN) character,
* optionally followed by an [assignment value](#assignment-values).

`<whitespace>` is not allowed between the `<identifier>` and the equal sign.

The assignment value stops at the first `<whitespace>` or `<newline>` character that is neither escaped nor quoted.


## Identifiers

An identifier starts with an ASCII alpha or underscore (`_` U+005F LOW LINE),
optionally followed by any number of ASCII alpha, ASCII digit or underscores.

In other words, an identifier matches the following regular expression:
```regexp
[a-zA-Z_][a-zA-Z0-9_]*
```

## Assignment values

Assignment values follow the
[quoting rules](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_02)
defined in the POSIX specification.

An assignment value consists of a sequence of any number of:
* unquoted strings
* single-quoted strings
* double-quoted strings

In the following example, `FOO` evaluates to `foobarbaz`:

```sh
FOO=foo'bar'"baz"
```


## Escape character

A `<backslash>` (`\` U+005C REVERSE SOLIDUS) character can be used as an escape character
in [unquoted](#unquoted-strings) and [double-quoted](#double-quoted-strings) strings,
to preserve the literal value of the following character.
It has no effect in [single-quoted](#single-quoted-strings) strings.

## Line continuation

In unquoted and double-quoted strings, if a `<newline>` follows a `<backslash>`,
the `<backslash>` and `<newline>` are entirely ignored.

In the following example, all variables evaluate to `foobar`:

```sh
A=foo\
bar
B="foo\
bar"
```


## Unquoted strings

A `<whitespace>` or `<newline>` character cannot occur inside an unquoted string unless escaped with a `<backslash>`.

[Parameter expansion](#parameter-expansion) occurs in unquoted strings.

The following characters have a special meaning in shell scripts
and MUST be escaped in unquoted strings:

* `|` (U+007C VERTICAL LINE)
* `&` (U+0026 AMPERSAND)
* `;` (U+003B SEMICOLON)
* `<` (U+003C LESS-THAN SIGN)
* `>` (U+003E GREATER-THAN SIGN)
* `(` (U+0028 LEFT PARENTHESIS)
* `)` (U+0029 RIGHT PARENTHESIS)
* `` ` `` (U+0060 GRAVE ACCENT)

Additionally, the following characters must be escaped if they are to represent themselves:

* `"` (U+0022 QUOTATION MARK)
* `'` (U+0027 APOSTROPHE)
* `$` (U+0024 DOLLAR SIGN)
* `<backslash>` (U+005C REVERSE SOLIDUS)
* `<space>` (U+0020 SPACE)
* `<tab>` (U+0009 TAB)


## Single-quoted strings

Single-quoted strings are sequences of characters enclosed in single quotes (`'` U+0027 APOSTROPHE).

Single-quoted strings **preserve the literal value** of each character within the single-quotes.

Parameter expansion does not occur in unquoted strings.

A single-quote CANNOT occur within single-quotes.

Invalid:
```sh
FOO='foo\'bar'
```

Valid:
```sh
FOO='foo'\''bar'
FOO='foo'"'"'bar'
```


## Double-quoted strings

Double-quoted strings are sequences of characters enclosed in double quotes (`"` U+0022 QUOTATION MARK).

A `<backslash>` character before a `"`, `$` or `\` preserves the literal meaning of the following character.

A `<backslash>` preceding a `<newline>` is interpreted as a line continuation.

[Parameter expansion](#parameter-expansion) occurs in double-quoted strings.


## Parameter expansion

The POSIX-compliant dotenv syntax implements the following subset of the POSIX
[parameter expansion](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_02)
specification.

Parameter expansions some in two forms: simple expansions and complex expansions.

### Simple expansions

Simple expansions start with a `$` character (U+0024 DOLLAR SIGN),
followed by an [identifier](#assignment-expressions)
optionally enclosed in curly braces (`{` U+007B LEFT CURLY BRACKET, `}` U+007D RIGHT CURLY BRACKET).

```sh
FOO='foo'
A=$FOO
B="$FOO"
C=${FOO}
D="${FOO}"
```

Both the simple forms `$FOO` and `${FOO}` are strictly equivalent to the complex form `${FOO-}`.

### Complex expansions

TODO

`${<identifier><operator><value>}`

There are eight possible operators:
* `-` (U+002D HYPHEN-MINUS)
* `:-` (U+003A COLON, U+002D HYPHEN-MINUS)
* `=` (U+003D EQUALS SIGN)
* `:=` (U+003A COLON, U+003D EQUALS SIGN)
* `+` (U+002B PLUS SIGN)
* `:+` (U+003A COLON, U+002B PLUS SIGN)
* `?` (U+003F QUESTION MARK)
* `:?` (U+003A COLON, U+003F QUESTION MARK)

| expression    | `LHS` is set and not empty | `LHS` is set but empty | `LHS` is not set       |
|---------------|----------------------------|------------------------|------------------------|
| `${LHS:-rhs}` | use the value of `$LHS`    | use the value of `rhs` | use the value of `rhs` |
| `${LHS-rhs}`  | use the value of `$LHS`    | use the empty string   | use the value of `rhs` |
| `${LHS:=rhs}` | use the value of `$LHS`    | assign `rhs` to `LHS`  | assign `rhs` to `LHS`  |
| `${LHS=rhs}`  | use the value of `$LHS`    | use the empty string   | assign `rhs` to `LHS`  |
| `${LHS:?rhs}` | use the value of `$LHS`    | error                  | error                  |
| `${LHS?rhs}`  | use the value of `$LHS`    | use the empty string   | error                  |
| `${LHS:+rhs}` | use the value of `rhs`     | use the empty string   | use the empty string   |
| `${LHS+rhs}`  | use the value of `rhs`     | use the value of `rhs` | use the empty string   |



## Comments

A `<comment>` starts with the `#` character (U+0023 NUMBER SIGN)
and continues up to (but not including) the next `<newline>` character.

The `#` character starts a comment if all the following conditions hold:
* it is not [escaped](#escape-character) (preceded by a `<backslash>` character)
* it does not appear inside a [single](#single-quoted-strings) or [double-quoted](#double-quoted-strings) string
* it is either:
  * the first character in the file
  * preceded by a `<whitespace>` or `<newline>` character that is not [escaped](#escape-character)

In the following example, all variables evaluate to `im#not-a-comment`:

```sh
A=im#not-a-comment
B='im#not-a-comment'
C='im'#not-a-comment
D="im#not-a-comment"
E="im"#not-a-comment
F=im\ #not-a-comment
G=im\
#not-a-comment
```

In the following example, all variables evaluate to `im`:

```sh
# a comment
  # an indented comment
A=im # a comment
B='im' # a comment
C="im" # a comment
D=im\
  # a comment
```
