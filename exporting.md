# Exporting

Implementations must act as if they used [the following algorithm](#exporting-algorithm)
to export POSIX-compliant dotenv files.

The inputs to the exporting stage are:
* the associative array returned by the [evaluation](evaluation.md) stage
* an optional [override flag](#override-flag)

The exporting stage has no output.

## Definitions

### Override flag

The override flag is a boolean flag that defaults to `false`

### Environment variable

Please refer to:
* The [POSIX specification](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap08.html).
* The [article on wikipedia](https://en.wikipedia.org/wiki/Environment_variable).


## Exporting algorithm

* For each (`key`, `value`) pair in the associative array returned by the [evaluation](evaluation.md) stage:
  * if any of the following conditions holds true: 
    * the [override flag](#override-flag) is `true`,
    * no [environment variable](#environment-variable) named `key`
      is defined in the environment of the current process, 
  * then set the value of the [environment variable](#environment-variable) named `key` to `value`
