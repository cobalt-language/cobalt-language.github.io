# Operators

## Assignment operators 

| Syntax        | Description                                       |
| ------------- | ------------------------------------------------- |
| `=`           | Ordinary assignment.                              |
| `+=`          | Add and assign.                                   |
| `-=`          | Subtract and assign.                              |
| `*=`          | Multiply and assign.                              |
| `/=`          | Divide and assign.                                |
| `%=`          | Mod and assign.                                   |
| `&=`          | Bit and, and assign.                              |
| <code>&#x7c;=</code> | Bit or, and assign.                        |
| `^=`          | Bit XOR and assign.                               |
| `<<=`         | Bit shift left and assign.                        |
| `>>=`         | Bit shift right and assign.                       |

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

