# Evaluation

Implementations must act as if they used [the following algorithm](#evaluation-algorithm)
to evaluate POSIX-compliant dotenv files.

The inputs to the evaluation stage are:
* a sequence of nodes from the [parsing](parsing.md) stage
* an optional [override flag](#override-flag)

The output of the evaluation stage is the result of [evaluating an assignment list](#evaluating-an-assignment-list).


## Definitions

### Override flag

The override flag is a boolean flag that defaults to `false`

### Local scope

The local scope is an [associative array](https://en.wikipedia.org/wiki/Associative_array)
where both the keys and values are strings.
It is initially empty.

### Update the local scope

When a step says to update the local scope with a given (key, value) pair:
* If the given key exists in the [local scope](#local-scope):
  * set the value associated with the given key to the given value
* Otherwise:
  * insert the given (key, value) pair in the [local scope](#local-scope).

### Undefined

A special marker value used to indicate the absence of a value.

### Environment variable

Please refer to:
* The [POSIX specification](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap08.html).
* The [article on wikipedia](https://en.wikipedia.org/wiki/Environment_variable).

An environment variable named `name` is defined if calling
[getenv](https://pubs.opengroup.org/onlinepubs/9699919799/functions/getenv.html) with `name`
does not return a null pointer.

## Evaluation algorithm

### Evaluating an assignment list

1. Create a new empty [local scope](#local-scope).
2. For each node in the sequence of input nodes:
   * If the value of the node's `kind` attribute is not `Assignment`:
     * Evaluation error.
   * Otherwise, [evaluate an assignment](#evaluating-an-assignment) with node
3. Return the [local scope](#local-scope).

### Evaluating an assignment

* Let `name` be the value of the node's `name` attribute 
* If the [override flag](#override-flag) is `false`,
  and an [environment variable](#environment-variable) named `name` is defined:
  * Let `value` be the value of the environment variable named `name`
* Otherwise:
  * Let `node-list` be the value of the node's `value` attribute
  * Let `value` be the result of [evaluating an expression](#evaluating-an-expression) with `node-list`
* [Update the local scope](#update-the-local-scope) with (`name`, `value`).

### Evaluating an expression

1. Let `result` be the empty string.
2. For each node in `node-list`:
   * Switch on the node's `kind` attribute:
     * `Characters`:
       * append the value of the node's `value` attribute to `result`
     * `Expansion`:
       * let `value` be the result of [evaluate an expansion](#evaluating-an-expansion) with node.
       * append `value` to `result`
     * anything else:
       * Evaluation error.
3. Return `result`

### Evaluating an expansion

1. Let `name` be the value of the node's `name` attribute
2. Let `operator` be the value of the node's `operator` attribute
3. Let `node-list` be the value of the node's `value` attribute
4. Let `value` be the result of [resolving a name](#resolving-a-name) with `name`
5. Switch on `operator`:
    * `-` (U+002D HYPHEN-MINUS):
      * If `value` is [undefined](#undefined):
        * Set `value` to the result of [evaluating an expression](#evaluating-an-expression) with `node-list`.
    * `:-` (U+003A COLON, U+002D HYPHEN-MINUS):
      * If `value` is either [undefined](#undefined) or the empty string:
        * Set `value` to the result of [evaluating an expression](#evaluating-an-expression) with `node-list`.
    * `+` (U+002B PLUS SIGN):
      * If `value` is [undefined](#undefined):
        * Set `value` to the empty string.
      * Otherwise:
        * Set `value` to the result of [evaluating an expression](#evaluating-an-expression) with `node-list`.
    * `:+` (U+003A COLON, U+002B PLUS SIGN):
      * If `value` is either [undefined](#undefined) or the empty string:
        * Set `value` to the empty string.
      * Otherwise:
        * Set `value` to the result of [evaluating an expression](#evaluating-an-expression) with `node-list`.
    * `=` (U+003D EQUALS SIGN):
      * If `value` is [undefined](#undefined):
        * Set `value` to the result of [evaluating an expression](#evaluating-an-expression) with `node-list`.
        * [Update the local scope](#update-the-local-scope) with (`name`, `value`)
    * `:=` (U+003A COLON, U+003D EQUALS SIGN):
      * If `value` is either [undefined](#undefined) or the empty string:
        * Set `value` to the result of [evaluating an expression](#evaluating-an-expression) with `node-list`.
        * [Update the local scope](#update-the-local-scope) with (`name`, `value`)
    * `?` (U+003F QUESTION MARK):
      * If `value` is [undefined](#undefined):
        * Let `message` be the result of [evaluating an expression](#evaluating-an-expression) with `node-list`.
        * If `message` is not the empty string:
          * Missing required value error with message: `message`
        * Otherwise:
          * Missing required value error: missing required value for `name`
    * `:?` (U+003A COLON, U+003F QUESTION MARK):
      * If `value` is either [undefined](#undefined) or the empty string:
        * Let `message` be the result of [evaluating an expression](#evaluating-an-expression) with `node-list`.
        * If `message` is not the empty string:
          * Missing required value error with message: `message`
        * Otherwise:
          * Missing required value error: missing required value for `name`
    * anything else:
      * Evaluation error: unknown expansion operator. 
6. Return `value`

### Resolving a name

* If the [override flag](#override-flag) is `true`:
  * If the [local scope](#local-scope) contains a key for `name`:
    * return the value associated with the key for `name` in the [local scope](#local-scope)
  * Otherwise, if an [environment variable](#environment-variable) named `name` is defined:
    * return the value of this environment variable.
  * Otherwise:
    * return [undefined](#undefined)
* Otherwise:
  * If an [environment variable](#environment-variable) named `name` is defined:
    * return the value of this environment variable.
  * Otherwise, if the [local scope](#local-scope) contains a key for `name`:
    * return the value associated with the key for `name` in the [local scope](#local-scope)
  * Otherwise:
    * return [undefined](#undefined)
