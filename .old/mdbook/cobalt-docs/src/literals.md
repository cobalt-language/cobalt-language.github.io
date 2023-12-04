# Literals

## Strings

String literals can be specified with double quotations:
```
let s = "Hello, world!";
```

A string literal can be immediately followed by `c`. This used to be required to pass string 
literals as parameters to (extern) functions expected C-style strings, _but is no longer 
necessary_. This is because string literals are automatically appended by a null terminator.
However, by default, this does not affect the length of the string. If you include the `c` 
suffix, it will:
```
# length is 2
let s1 = "hi";

# length is 3
let s1 = "hi"c;
```
