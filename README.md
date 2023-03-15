# POSIX-compliant dotenv syntax specification

## Rationale

Using dotenv files for application configuration is a widespread practice.

While numerous implementations are available in a wide variety of languages,
there is currently no specification for this file format.
This result in a multitude of different syntaxes, all vaguely resembling the POSIX shell
but fundamentally incompatible due to slightly divergent behaviours.

The goal of this repository is to
* specify a dotenv syntax that is 100% compatible with the
  [POSIX shell syntax](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html),
* provide detailed algorithms for [tokenizing](tokenization.md) and [parsing](parsing.md) said syntax,
  [evaluating](evaluation.md) the generated AST to produce a set of environment variables
  and [export](exporting.md) them to the current process environment.
* provide language-agnostic [conformance tests](tests.md) in machine-readable format.


## Table of contents

* [Syntax overview](syntax.md)
* [tokenization](tokenization.md)
* [parsing](parsing.md)
* [evaluation](evaluation.md)
* [exporting](exporting.md)
* [tests](tests.md)
