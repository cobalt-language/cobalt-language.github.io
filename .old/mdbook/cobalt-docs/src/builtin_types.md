# Built-in types

## Numbers

| Syntax       | Description                    |
| ------------ | ------------------------------ |
| `i{bits}`    | signed integer                 |
| `isize`      | signed integer with same bits as a pointer |
| `u{bits}`    | unsigned integer               |
| `usize`      | unsigned integer with same bits as a pointer |
| <code>f{16&#x7c;32&#x7c;64&#x7c;128}</code> | floating-point number with specified bits |

`bits` is any positive integer less that 65,536. Typically you want to stick to a power of `2`, and are probably good with using `32` or `64`.

## References and pointers

The primary difference between a reference and a pointer is that a pointer needs to be dereferenced in order to act like the underlying value, while a reference does not. For instance, suppose there is a type `T` with method `foo()`. Here's how you would call this method with a reference and with a pointer:
```
# Suppose `x_ref` has type `&T`. 
x_ref.foo();

# Suppose `x_ptr` has type `*T`.
(*x_ptr).foo();
```
The underlying concept in play here is _decaying_. In this context, a reference `&T` "decays" into `T`. You can think of this as an implicit conversion.

| Syntax       | Description                    |
| ------------ | ------------------------------ |
| `*T`         | pointer to type `T`: cannot change the memory being pointed to |
| `*mut T`     | mutable pointer to type `T`: can change the memory being pointed to |
| `&T`         | reference to type `T`: cannot change the underlying instance |
| `&mut T`     | mutable reference to type `T`: can change the underlying instance |

## Arrays

| syntax       | description                    |
| ------------ | ------------------------------ |
| `T[]`        | unsized array of objects of type `T` |
| `T[{size}]`  | sized array (with size `size`) of objects of type `T` |

### Unsized arrays

There are two things to note:
- `T[]` must be behind a reference.
- The size is stored with the data as a fat pointer.

Practically, this means you will not use variables of type `T[]` directly, but rather use references `&T[]` or `&mut T[]`. In this case, the reference can be thought of as a fat pointer which stores the size of the array. In particular, you don't need to keep track of the array size in a different variable.

### Sized arrays

`size` must be computable at compile-time.

## Tuples

| syntax       | description                    |
| ------------ | ------------------------------ |
| `(T, U, V, ...)` | tuple whose first element is type `T`, second is type `U`, etc. |

You can access the members with parentheses:
```
let x: (i32, f32) = (3, 3.1415);
let x_int = x(0);
let x_float = x(1); 
```

## Structs 

### Literals

The simplest way to create a struct is to use literal notation.
```
let monster = { alive: true, health: 97i32, };
```
In the above example, `monster` has type `{alive: bool, health: i32}`.

You can access fields using dot notation:
```
let health = monster.health;
```

### Nominals

Structs can be named in the following way:
```
type <NAME> = {
    <FIELD_NAME>: <FIELD_TYPE>,
    ...
}
```
To explicitly construct this, you need to use a bitcast:

Fields are accessed with dot notation. The following is an illustrative example:
```
type MyStruct = {
	field1: bool,
	field2: i32,
};

fn main(): i32 = {
    # `x` has type `{ field1: bool, field2: i32, }`
	let x = { field1: false, field2: 3i32 };
    # `y` has type `MyStruct`
	let y = { field1: false, field2: 3i32 } :? MyStruct;
};
```

However, keep in mind that naming a type like this obfuscates the base type. What this means practically is that, to access the fields of a named struct, you must either cast it to it's base type beforehand or mark it with the `@transparent` annotation:
```
@transparent
type MyStruct = {
	field1: bool,
	field2: i32,
};

fn main(): i32 = {
	let y = { field1: false, field2: 3i32 } :? MyStruct;

    y.field2
};
```
