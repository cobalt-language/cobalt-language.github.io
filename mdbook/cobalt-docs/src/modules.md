# Modules

## NOT WORKING

Modules are a way of giving a scope and namespace to your code. The basic syntax is
```
module <NAME> {
    <TOP_LEVEL_DECLARATIONS>+
}
```
Recall that a top level declarations can be functions and variable assignments. For example,
```
module MyModule {
    let my_constant = 3i32;

    fn my_function(): i32 = {
        6i32
    };
}
```
