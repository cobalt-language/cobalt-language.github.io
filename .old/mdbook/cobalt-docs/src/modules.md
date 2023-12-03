# Modules

Modules are a way of giving a scope and namespace to your code. 

## Same-file 

The basic syntax is
```
module <NAME> {
    <TOP_LEVEL_DECLARATIONS>+
};
```
Recall that a top level declarations can be functions and variable assignments. For example,
```
module MyModule {
    let my_constant = 3i32;

    fn my_function(): i32 = {
        6i32
    };
};
```

## Using it

To use the things defined in a module, you have a few options. 

If the module is defined in the same file you want to use it, then you can prefix its elements with 
the module name, kind of like a namespace:
```
module MyModule {
    let my_constant = 3i32;
};

fn foo(): i32 = {
    MyModule.my_constant
};

```

### Imports

If/when that gets tedious, you can bring the module's contents into the current scope by importing it:
```
module MyModule {
    let my_constant = 3i32;
    let my_other_constant = 5i32;
};

import MyModule.*;

fn foo(): i32 = {
    MyModule.my_constant
};
```
The wildcard `*` imports all elements of the module. You can import a single item like this:
```
import MyModule.my_constant;
```
or multiple items like this:
```
import MyModule.{my_constant, my_other_constant};
```

## Managing scopes

When a project starts to grow it is often useful to break things up. TODO
