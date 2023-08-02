# Variables

Variables are defined with the `let` keyword. Types are deduced, but can also be manually specified:
```
let x = 3i32;       # deduced type
let y: i32 = 4i32;  # explicit type
```

Mutable variables are marked by the `mut` keyword:
```
let mut x = 3i32;
x = 5i32;
```

Variables which are known at compile time can be specified using the `const` keyword:
```
let const x = 5i32;
```
