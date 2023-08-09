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
[const|mut] <NAME>: <TYPE> [= <DEFAULT VAL>]
```
where 
- `[const|mut]` means that, optionally, you can write `const` or `mut` before the type. `const` means the parameter is known at compile time. `mut` means the function can modify the parameter. If neither is specified, then you cannot change the value of the parameter.
- `<NAME>` is the name of the parameter, which can be used inside the function.
- `<TYPE>` is the type of the parameter.
- `[= <DEFAULT VAL>]` means that, optionally, you can provide a default value to the parameter. 

Note that having a reference parameter marked as `mut` is not the same as that parameter having type `&mut Ty`. For instance, the paramaters `mut arg: &i32` and `arg: &mut i32` are not the same.
- In the first case, `mut arg: &i32`, you can reassign what is being referenced by `arg` but cannot mutate the underlying object.
- In the second case, `arg: &mut i32`, you cannot reassign what is being referenced, but can mutate the underlying object.

### Reference parameters 

To call a function with a reference parameter, you can either provide a reference or a value. If you provide a value then it will be automatically converted into a reference. 

```
fn foo(x: &i32);

fn bar(y: &i32) = {
    # Pass a reference parameter directly.
    foo(y);
};

fn main(): i32 = {
    let x = 3i32;

    # Even though we pass in a value, since the function expects a reference it will convert it to a reference for us.
    foo(x);

    0
}
```

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
@C(extern) fn puts(str: *u8); 

# Note the allowed paramaters to `main()`.
fn main(argc: i32, argv: **u8): i32 = {
  puts("Hello, World!");
  0
};
```

## Misc

- If no body to a function is provided (and it is not `extern`) then the body defaults to null. In particular, the function can still be called.

