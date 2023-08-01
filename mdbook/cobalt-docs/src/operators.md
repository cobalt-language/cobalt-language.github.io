# Operators

## `:?`

The `:?` operator is a bit cast. It results in a move.
```
type MyInt1 = i32;
type MyInt2 = MyInt1;

fn main() = {
    let x = 3i32 :? MyInt1;
    let y = x :? MyInt2;  # `x` moved here.

    # `x` is no longer valid.
};
```

