# Syntax
Cobalt's syntax is similar to Rust's, but has notable differences.

## Comments
Cobalt uses `#` for its comments. Multiline comments start with `#` and at least one `=`, and end with at least the same number of `=`s. This is done greedily, so `#=#`, `#==#`, etc. do not count as opening and closing the same comment. Multiline comments do not (currently) nest.

## Definitions
Definitions can take annotations, which are written as `@name` or `@name(arg)`. Parentheses inside `arg` are matched, but no annotations (at this time) use them.
*NOTE*: "declare" and "define" are used mostly synonymously throughout this section. In general, "define" tends to mean that something is being evaluated, and "declare" means that a symbol is being defined without any evaluation of its body.

### Variables
Variables can be defined with `let`, `mut`, or `const`.
| Keyword | Behavior                                 | Variable type |
|---------| -----------------------------------------|---------------|
| `let`   | Defines a runtime constant variable      | Value or ref  |
| `mut`   | Defines a runtime mutable variable       | mutable ref   |
| `const` | Defines a compile-time constant variable | value         |
With the expression `{kw} var = val;`, where `val`'s unwrapped type is `T`, `const` defines a variable of `T`, `mut` defines a variable of `T mut&`, and `let` can create `T` or `T const&`. `let` creates a reference if:
- it has the `@stack` annotation
- the variable needs a memory location (not fully implemented, may be removed)

Variables can have a type after `:`, and then a value after `=`. If the type is not set, it is inferred. If the value is not set, it defaults to `null`.

#### Annotations
| Annotation                          | Behavior                                     |
|-------------------------------------|----------------------------------------------|
| `@link(type)`                       | set linkage                                  |
| `@linkas(name)`                     | set link name                                |
| `@C(extern (optional))`             | unmangle link name                           |
| `@target(glob)`                     | only evaluate if the target matches the glob |
| `@export(true | false (optional))`  | set module export, defaults to true          |
| `@private(true | false (optional))` | does the opposite of `@export`               |

#### Example
```co
let x = 10; # x cannot be changed, and is only available at runtime
mut y = x + 1; # y can be changed, and is only available at runtime
const z = 100; # z is available at compile- and runtime
# const fails = x; # this would give a compile error
let fine = z; # this is fine, though
let explicit: f64 = 1.0;
```

### Functions
Functions are defined with the `fn` keyword. Each parameter has the form `[const | mut] name: type [= default]`. Functions are defined as `fn name(params...): return = body;`.
If a return type or body are not specified, they default to `null`.

#### Annotations
| Annotation                          | Behavior                                         |
|-------------------------------------|--------------------------------------------------|
| `@link(type)`                       | set linkage                                      |
| `@linkas(name)`                     | set link name                                    |
| `@cconv(convention)`                | set calling convention. can either be name or id |
| `@extern(cconv (optional))`         | set function as externally defined               |
| `@inline(always | never)`           | inline function. may or may not actually be done |
| `@C(extern (optional))`             | unmangle link name and use C calling convention  |
| `@target(glob)`                     | only evaluate if the target matches the glob     |
| `@export(true | false (optional))`  | set module export, defaults to true              |
| `@private(true | false (optional))` | does the opposite of `@export`                   |
| `@method` (types only)              | mark a function as a method                      |
| `@getter` (types only)              | mark a function as a getter                      |

#### Example
```co
@C(extern) fn puts(str: u8 const*); # both return type and body are inferred to be null
fn main(argc: i32, argv: u8 const* const*): i32 = {
  puts("Hello, World!"c);
  0
}; # main function
fn mutable_args(mut arg: i32) = { # note that this is *different* from `arg: i32 mut&`.
  ++arg;
  # do something with arg
};
```

### Types
Types are defined using the `type` keyword. The simple form is `type name = val;`, with `val` being the underlying representation.
To add methods, use `type name = val :: {methods...};`, where the methods are declarations. Within the methods, `self_t` is defined as the type, and `base_t` is defined as the base type.
*NOTE*: This defines a new type. To define an alias, use `const`.

#### Annotations
| `@target(glob)`                     | only evaluate if the target matches the glob |
| `@export(true | false (optional))`  | set module export, defaults to true          |
| `@private(true | false (optional))` | does the opposite of `@export`               |

#### Example
```co
@C(extern) fn printf(str: u8 const*, val: i32);
type opaque_ptr = null const*; # might be useful for C APIs
type my_int = i32 :: {
  fn new(val: base_t): self_t = val :? self_t; # a bit cast is necessary here
  @getter fn val(self: self_t): base_t = self :? base_t;
  @method fn print(self: self_t) = printf("%i\n"c, self.val);
};
fn usage() = {
  let int = my_int.new(10);
  int.print(); # only allowed because of the `@method`
  let double = my_int.new(int.val * 2);
};
```

### Modules
Modules use the `module` keyword. They can be defined as:
- `module name;`
- `module name = imp.ort.statement;`
- `module name {top-level declarations...}`
The first form declares an empty module.
The second desugars to:
```co
module name {
  import imp.ort.statement;
}
```
The third defines a module with the statements' definitions inside it.

#### Annotations
| `@target(glob)`                     | only evaluate if the target matches the glob |
| `@export(true | false (optional))`  | set module export, defaults to true          |
| `@private(true | false (optional))` | does the opposite of `@export`               |
The visibility modifiers for modules also apply those modifiers to their contents. Exporting variables inside a private module will not export them.

#### Example
```co
module empty;
module x {
  let y = 10;
  let z = 20;
}
module alias = x.*;
let sum = alias.y + alias.z;
```

### Imports
Imports are defined using the `import` keyword. Import paths can be more complex than normal ones. They are made up of `.`-separated segments, but there are different types of segments:
- `identifier`- matches the identifier literally
- `*`- matches everything
- `{a, b, c, ...}` matches each of the identifiers
Unlike in Rust, you can do weird matches that probably shouldn't be allowed:
```co
import .std.*.prelude.*;
import {a, b}.c.*;
```

#### Annotations
| `@target(glob)`                     | only evaluate if the target matches the glob |
| `@export(true | false (optional))`  | set module export, defaults to true          |
| `@private(true | false (optional))` | does the opposite of `@export`               |

#### Example
```co
module mod1 {
  let x = 10;
  let y = 20;
}
import mod1.*;
let z = x + y;
```


## Operators
Cobalt's operator precedence is:
1. Postfixes
  - `!`, `?`
  - types: `const &`, `mut &`, `const *`, `mut *`
  - calls
  - subscripts
2. Prefixes: `+`, `-`, `*`, `&`, `~`, `!`
3. `*`, `/`, `%`
4. `+`, `-`
5. `<<`, `>>`
6. `<`, `>`, `<=`, `>=`
7. `==`, `!=`
8. `&`
9. `^`
10. `|`
11. `:`, `:?` (casts)
12. `&?` (short-circuiting and)
13. `|?` (short-circuiting or)
14. `if`, `else`

## Blocks
Blocks are delimited by braces (`{}`), and contain statements. An empty set of braces or a block with a trailing `;` evaluate to `null`. All others evaluate to the last value.
*NOTE*: Unlike in other languages, blocks still need semicolons after them. This is a syntax error:
```co
let = {
  let before = 1;
  {
    let inner = 2;
  } # there needs to be a semicolon here
  let after = 3;
# ^-- syntax error happens here
};
```

## Control flow

### `if`/`else`
Conditional expressions are written as `if (cond) if_true` or `if (cond) if_true else if_false`. These *are* expressions, and they replace the functionality of a ternary operator. The condition can also be surrounded by braces, if it is more convenient for the condition to be a block.
*NOTE*: There is not a semicolon before the `else`. Inserting one will result in a syntax error.

### `while`
`while` loops are written as `while (cond) body`. Like `if`/`else` expressions, the condition can be enclosed in braces. The body **cannot** be empty, but an explicit `null` (or `()`) can be used.