# Conformance tests

This repository provides a comprehensive set of language-agnostic conformance tests in machine-readable format.

## Tokenization tests

The tokenization tests are located in the [tests/tokenization](./tests/tokenization) directory.

Each test file consist of a JSON-encoded array of test cases in the following format:

```json5
// An array of test-cases, each of which is either:
[
  // A success test-case:
  {
    "desc": "This is the test case description",
    // The input string to be tokenized
    "input": "FOO=bar",
    // An array of expected tokens that must be returned in order for the test to pass,
    // i.e. assert(tokenize(input) === expected)
    "expected": [
      {"kind":  "Assign", "value":  "FOO"},
      {"kind":  "Characters", "value":  "bar"},
      {"kind":  "EOF", "value":  ""},
    ]
  },
  // Or an error test-case:
  {
    "desc": "This is the test case description",
    // The input string to be tokenized
    "input": "$x0x$=",
    // The kind of error that must be returned in order for the test to pass,
    // i.e. assert(tokenize(input) === error)
    "error": "ParseError"
  }
]
```

## Evaluation tests

The evaluation tests are located in the [tests/evaluation](./tests/evaluation) directory.

Each test file consist of a JSON-encoded array of test cases in the following format:

```json5
// An array of test-cases, each of which is either:
[
  // A success test-case
  {
    "desc": "The test case description",
    // The input string to be evaluated
    "input": "FOO=${BAR:-baz}",
    // An optional boolean flag, that must default to false if not present.
    "override": true,
    // optional key-value map of existing environment variables
    // if not present, an empty object must be used
    "env": {
      "BAR": "qux"
    },
    // A mapping of expected variables that must be returned in order for the test to pass,
    // i.e. assert(evaluate(input, env, override) === expected)
    "expected": {
      "FOO": "qux"
    }
  },
  // An error test-case
  {
    "desc": "The test case description",
    // The input string to be evaluated
    "input": "$$=#",
    // An optional boolean flag, that must default to false if not present.
    "override": true,
    // optional key-value map of existing environment variables
    // if not present, an empty object must be used
    "env": {
      "FOO": "bar"
    },
    // The kind of error that must be returned in order for the test to pass,
    // i.e. assert(evaluate(input, env, override) === error)
    "error": "ParseError"
  }
]
```
