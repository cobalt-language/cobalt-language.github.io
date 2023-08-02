# Custom types

Types are defined using the `type` keyword. The simplest form is this:
```
type <NAME> = <VAL>;
```
where
- `<NAME>` is the name of the type you want to define.
- `<VAL>` is the underlying representation.
This simple form is most useful for type-aliasing; i.e. providing a name for a preexisting type, potentially to hide the underlying properties of the preexisting type from users. For example,
```
type opaque_ptr = null const*;
```

## Construction

If you define a type like 
```
type <NAME> = <VAL>;
```
and you are able to construct the type `<VAL>`, then you can cast the type `<VAL>` into the type `<NAME>` using a bit cast, e.g.
```
type MyInt = i32;

# Works
let x = 3i32;
let my_x = x :? MyInt;

# Also works
let my_y = 3i32 :? MyInt;

# Doesn't work-- you need to explicitly bit cast
let z = 3i32;
let my_z: MyInt = z;
```

You don't have to do this every time you construct your type. You can provide a _method_ for construction. We'll discuss method more below.

## Fields

Every type has a default field `__base` (note there are two underscores) which accesses the base type.
```
type MyInt = i32;

fn main(): i32 {
    let x1 = 3i32 :? MyInt;
    let x2 = 3i32 :? MyInt;

    # Two ways to access the base type.
    let y1 = x1 :? i32;
    let y2 = x2.__base;

    0
};
```

## Methods

Methods are defined in the following way: 
```
type <NAME> = <VAL> :: {
    # Methods go here.
};
```

To define a method on an instance, use the `self_t` type: 
```
type <NAME> = <VAL> :: {
    @method
    fn immut_method(s: &self_t);

    @method
    fn mut_method(s: &mut self_t);

    @method
    fn move_method(s: self_t);
};
```

You can refer to the type `<VAL>` as `base_t`:
```
type <NAME> = <VAL> :: {
    @getter 
    fn val(self: self_t): base_t = self :? base_t;
};
```

Methods are often decorated with the following annotations:

| Annotation                          | Behavior                                         |
| ----------------------------------- | ------------------------------------------------ |
| `@method`                           | General purpose, allows method to be called.      |
| `@getter`                           | Allows the method to be called like a field.     |
| `@op(drop)`                         | Implement a destructor (see below).              |

### `@method`

Example:
```
type 
```

### `@getter`

```
@C(extern)
fn puts(str: *u8);

type MyType = (i32, f32) :: {
	@getter
	@inline
	fn x(s: &self_t): i32 = {
		s.__base(0)
	};
};

fn main(): i32 = {
	let my_type = (3i32, 9.9f32) :? MyType;
	if (my_type.x == 3i32) {  # Note we don't use `()` to call a getter.
		puts("Success");
	} else {
		puts("Failure");
	};

	0
};
```

## Destructors

A destructor is called when (after) an instance goes out of scope, and is responsible for cleaning up
memory and closing resources associated with the instance. For basic types and their derivatives, you 
do not need to worry about implementing this yourself.

Destructors are implemented as methods annotated with `@op(drop)` and accepting single `&mut self_t` 
parameter. The return type is `null`.

```
type <NAME> = <VAL> :: {
    @op(drop)
    fn my_custom_destructor(s: &mut self) = {
        # Clean up here.
    };
};
```
