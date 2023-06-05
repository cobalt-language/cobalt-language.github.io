# Literals
Every (usable) language has literals.

## Primitives
Primitive literals can have suffixes, which is an identifer that can be optionally separated by whitespace.

### Integers
Integer literals as you would expect. The prefixes `0b`, `0o`, and `0x` change their type to binary, octal, and hexadecimal respectively.
Integer literals have the following suffixes:
- `i{bits}`
- `u{bits}`
- `isize`
- `usize`
These make an integer of that [type](./types#builtin-types). The default type is `i64`.

### Floats
Floating-point literals can have the same prefixes as integer literals. Their suffixes are `f16`, `f32`, `f64`, and `f128`, and default to `f64`.

### Characters
Character literals have integer suffixes, but have a default type of `u32`.

### Strings
String literals evalutate to `u8[N] const&`, where `N` is the length of the string. They are **not** null-terminated by default, but a null-terminator can be appended with either the `c` or `C` suffix.

## Compound Literals
These take other values in. It might not be correct to call them literals, but I don't know where else to put them.

### Arrays
Arrays are delimited by brackets (`[]`), and the elements are separated by commas. Cobalt does **not** allow trailing commas.

### Tuples
Tuples must be delimited by parentheses. To make a tuple with one element, a trailing comma can be used.