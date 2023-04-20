# Types
Cobalt is a strongly, statically typed language.

## Builtin types
The following types are built in:
- `i{bits}`- integers of any size less than 65,536 bits are allowed
- `u{bits}`- unsized integer with size
- `isize`, `usize`- integers with the same size as a pointer
- `f16`, `f32`, `f64`, `f128`- floating-point numbers
- `T const*`, `T mut*`- constant and mutable pointers
- `T const&`, `T mut&`- constant and mutable references
  - References work the way that they do in C++.
- `T[]`- unsized array
  - `T[]` must be behind a reference
  - The size is stored with the data as a fat pointer
- `T[size]`- sized array
  - For now, sized arrays must also be behind references
  - `size` must be computable at compile-time
- `(T, U, V...)`- tuple
  - access is done to members with parentheses, not brackets
  - access to a reference to a tuple gives a reference to the element

## Nominal types
Nominal types are defined with a data layout and optional methods.
They are defined as:
```
type my_type_1 = underlying_type;
type my_type_2 = underlying_type :: {
  # methods go here
};
```
If you need multiple values, use a tuple as the underlying structure.

### Methods
Method functions are *static* by default. To call them on a variable of the type, they need to be marked with `@method`. The value will then be passed as the first argument.
Methods can also be marked as `@getter`. This automatically calls the function with the value as its only argument. This replaces named struct fields.
