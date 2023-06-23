# Blocks and groupings

Blocks (`{}`) and groupings (`()`) can sometimes be used interchangeably, but differ in subtle ways.

## Blocks

_Blocks_ contain _statements_ and evaluate to a value (of some type). They are delimited by braces (`{}`) and must be followed by a semicolon (`;`). Blocks are themselves statements.

```
# This is a block:
{
    let x = 1;

    # This is a block within a block:
    {
        let y = 2;    
    };

    let z = 3;
};
```

Blocks are statements of the same kind as the last statement contained within them. In the above example, the last statement in the outermost block is `let z = 3;`. This is a non-expression statement. Therefore, the outermost block is a non-expression statement. Recall that non-expression statements have value `null` and type `null`. On the other hand, if the last statement in the block is an expression, then the block is also an expression. In that case, the value of the block will be the value of the last expression contained within it (and the type of the value will be the type of the value of the last expression contained within it).

```
# `x` has type/value `null`
let x = {
    let y = 1;
};

# `z` has type `i32` and value `5` 
let z = {
    2i32 + 3i32 # uses i32 literal syntax 
};
```

An important property of blocks is that they introduce a new scope. This is similar to the scoping behavior in other languages.

```
let x = 1;

let y = {
    let z = 2;

    # `x` is accessible in the inner scope
    z + x
};

# `z` is not accessible in the outer scope
```

## Groupings

_Groupings_ contain expressions, and are themselves expressions. Recall that every expression is a statement, but not every statement is an expression. In particular, groupings cannot contain non-expression statements. 

Groupings are delimited by parenthesis (`()`). They may or may not end with a semicolon. 

The following is valid:
```
# `x` has type `i32` and value `2`
let x = (
    2i32
);

# `a` has type/value `null`
let a = (
    1 + 1;
);

# `y` has type `i32` and value `2`
let y = (
    (
        2i32
    )
);

# `b` has type/value `null`
let b = (
    # the value of an expression ending with a semicolon is `null`
    (
        2i32
    );
);
```

This, however, is not valid (compare with blocks):
```
# Compiler error!
let x = (
    let y = 1;
);
```

In some cases, though, groupings and blocks can be used interchangeably.
```
# `x` has type `i32` and value `2` 
let x = {
    2i32  
};

# `y` has type `i32` and value `2` 
let y = (
    2i32
);
```

If a grouping contains no expressions, then it is an expression which evalutes to null.
```
# `x` has type/value `null`
let x = ();
```

Generally speaking, Cobalt only allows expressions inside parenthesis. 

