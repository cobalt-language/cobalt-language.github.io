# Functions

Functions are defined as
```
fn <NAME>(<PARAM>*): <TYPE> = <EXPR>
```
where
- `<NAME>` is the name of the function.
- `<PARAM>` is a function parameter. These are described in more detail below. By `<PARAM>*` we mean that a function may have zero or more parameters. Each parameter must be separated by a comma.
- `<TYPE>` is the return type of the function. If none is specified, don't include the colon `:` and the return type will be assumed to be `null`.
- `<EXPR>` is an expression. Typically, this would be a block (`{..}`). If nothing is specified, this will default to `null`.

## Parameters 

Each parameter has the form 
```
[const | mut] <NAME>: <TYPE> [= <DEFAULT VAL>]
```
where 
- `[const | mut]` means that, optionally, you can begin the parameter with `const` or `mut`. The meaning of these are the same as for variables. If neither `const` or `mut` is specified, the parameter is assumed to be runtime constant.  
- `<NAME>` is the name of the parameter, which can be used inside the function.
- `<TYPE>` is the type of the parameter.
- `[= <DEFAULT VAL>]` means that, optionally, you can provide a default value to the parameter. 

Note that having a parameter marked as `mut` is not the same as that parameter having type `Ty mut&`. For instance, the paramaters `mut arg: i32` and `arg: i32 mut&` are not the same.
- In the first case, `mut arg: i32`, the function has sole possession of the data underlying `arg`. Calling a function with such a parameter may result in an implicit copy.
- In the second case, `arg: i32 mut&`, the function does not have sole possession of the underlying data, but has permission to modify it.

## Annotations

Annotations can be placed above function definitions. The following options are available:

| Annotation                          | Behavior                                         |
| ----------------------------------- | ------------------------------------------------ |
| `@link(type)`                       | set linkage                                      |
| `@linkas(name)`                     | set link name                                    |
| `@cconv(convention)`                | set calling convention. can either be name or id |
| `@extern(cconv (optional))`         | set function as externally defined               |
| <code>@inline(always &#x7c; never)</code>          | inline function. may or may not actually be done |
| `@C(extern (optional))`             | unmangle link name and use C calling convention  |
| `@target(glob)`                     | only evaluate if the target matches the glob     |
| <code>@export(true &#x7c; false (optional))</code> | set module export, defaults to true              |
| <code>@private(true &#x7c; false (optional))</code> | does the opposite of `@export`                   |
| `@method` (types only)              | mark a function as a method                      |
| `@getter` (types only)              | mark a function as a getter                      |

## Examples

We can declare and use a function from `libc` as follows:
```
# Note both return type and body are inferred to be null.
@C(extern) fn puts(str: u8 const*); 

# Note the allowed paramaters to `main()`.
fn main(argc: i32, argv: u8 const* const*): i32 = {
  puts("Hello, World!"c);
  0
};
```

