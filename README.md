# POSIX-compliant dotenv syntax specification

## Relation to POSIX

The POSIX-compliant dotenv syntax is a subset of the
[POSIX shell command language specification](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html).

Conforming implementations MUST follows the rules in the aforementioned specification for:
* variable assignment
* [parameter expansion](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_02)
* [quote removal](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_07)

Conforming implementations MUST apply the restrictions to the former rules that are defined in this document.

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

