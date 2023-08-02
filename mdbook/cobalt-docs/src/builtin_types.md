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

The primary difference between a reference and a pointer is that pointers may point to invalid memory, whereas a reference to a type `T` is guarenteed to point to memory constituting an instance of `T`.

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
