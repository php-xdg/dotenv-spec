# Parsing

Implementations must act as if they used the following algorithm to parse POSIX-compliant dotenv files.

The input to the parsing stage is a sequence of tokens from the [tokenization](tokenization.md) stage.

The output of the parsing stage is the result of [parsing an assignment list](#parsing-an-assignment-list).


## Definitions

### Next token

The next token is the first token in the input token sequence that has not yet been consumed.
Initially, the next token is the first token in the input token sequence.

### Current token

The current token is the last token to have been consumed.


## Parsing algorithm

### Parsing an assignment list

* Let `node-list` be a new empty list.
* loop:
  * Consume the [next token](#next-token).
    * `EOF`:
      * return `node-list`.
    * `Assign`:
      * let `node` be the result of [parsing an assignment](#parsing-an-assignment).
      * append `node` to `node-list`.
    * anything else:
      * Parse error. 

### Parsing an assignment

* Create a new `Assignment` node.
* set the node's `name` attribute to the [current token](#current-token)'s value.
* set the node's `value` attribute to the result of [parsing an assignment value](#parsing-an-assignment-value).
* return the node.

### Parsing an assignment value

* Let `node-list` be a new empty list.
* loop:
  * Consume the [next token](#next-token).
    * `EOF`, `Assign`:
      * return `node-list`.
    * `Characters`:
      * Create a new `Characters` node.
      * set the node's `value` attribute to the [current token](#current-token)'s value.
      * append the newly created node to `node-list`.
    * `SimpleExpansion`:
      * Create a new `Expansion` node.
      * set the node's `name` attribute to the [current token](#current-token)'s value.
      * set the node's `operator` attribute to `-` (U+002D HYPHEN-MINUS).
      * set the node's `value` attribute to an empty list.
      * append the newly created node to `node-list`.
    * `StartExpansion`:
      * Create a new `Expansion` node.
      * set the node's `name` attribute to the [current token](#current-token)'s value.
      * set the node's `operator` attribute to the result of [parsing an expansion operator](#parsing-an-expansion-operator).
      * set the node's `value` attribute to the result of [parsing an expansion value](#parsing-an-expansion-value).
      * append the newly created node to `node-list`.
    * anything else:
      * Parse error.

### Parsing an expansion operator

Consume the [next token](#next-token).

* `ExpansionOperator`:
  * return the [current token](#current-token)'s value.
* anything else:
  * Parse error.

### Parsing an expansion value

* Let `node-list` be a new empty list.
* loop:
  * Consume the [next token](#next-token).
    * `EndExpansion`:
      * return `node-list`.
    * `Characters`:
      * Create a new `Characters` node.
      * set the node's `value` attribute to the [current token](#current-token)'s value.
      * append the newly created node to `node-list`.
    * `SimpleExpansion`:
      * Create a new `Expansion` node.
      * set the node's `name` attribute to the [current token](#current-token)'s value.
      * set the node's `operator` attribute to `-` (U+002D HYPHEN-MINUS).
      * set the node's `value` attribute to an empty list.
      * append the newly created node to `node-list`.
    * `StartExpansion`:
      * Create a new `Expansion` node.
      * set the node's `name` attribute to the [current token](#current-token)'s value.
      * set the node's `operator` attribute to the result of [parsing an expansion operator](#parsing-an-expansion-operator).
      * set the node's `value` attribute to the result of [parsing an expansion value](#parsing-an-expansion-value).
      * append the newly created node to `node-list`.
    * anything else:
      * Parse error.
