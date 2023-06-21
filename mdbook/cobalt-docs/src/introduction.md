# Introduction

Cobalt is a high-level, compiled language with similar uses to C++. It aims to be more modern than C++, but offer more freedom than Rust.

## Installation

### Binaries

Prebuilt versions are available from the [releases page](https://github.com/matt-cornell/cobalt-lang/releases/latest). As of right now, only the Linux `x86_64` build is available.

### Building from source

In order to build from source, you will need `git`, `ssh`, and LLVM 15 (_exactly_ version 15.0.0). 

The LLVM dependency is the most likely to cause trouble. It is unlikely that this specific version (15.0.0) is already on your machine. You can download it by finding the appropriate version on the [LLVM releases page](https://releases.llvm.org/download.html). At the time of writing, it will take you the the corresponding [Github release page](https://github.com/llvm/llvm-project/releases/tag/llvmorg-15.0.0). Remember where you install it.

Now we are ready to build Cobalt. Visit the [Github page](https://github.com/matt-cornell/cobalt-lang/) and clone the repository. Then, run `cargo build`:
```
> git clone https://github.com/matt-cornell/cobalt-lang
> cd cobalt-lang
> cargo build
```
By default, `cargo` builds the `dev` profile which is unoptimized and intended for testing/development. You can build release by adding the `--release` flag, e.g. `cargo build --release`.

Depending on how you installed LLVM, you may get compilation error mentioning missing `LLVM_SYS_150_PREFIX` and `LLVM_SYS_15_PREFIX`. Both of these environment variables should be set to point to where you installed LLVM. You can export these environment variables (so they are set globally) or export them directly to `cargo`. For example, if I installed LLVM in my home directory, I could do this:
```
> LLVM_SYS_150_PREFIX=~/llvm-15.0.0/ LLVM_SYS_15_PREFIX=~/llvm-15.0.0/ cargo build
```

### Adding to path

If you downloaded the prebuilt binary, you should now have an executable called `co`. If you build from source, it will probably be in `target/<PROFILE>/co`, where `<PROFILE>` is something like `debug` or `release`. In bash you can add this to `PATH` as follows:
```
> export PATH=path/to/co:$PATH
```

## Hello, World!

Let's write and compile the customary "Hello, World!" program in Cobalt.

### Source code

The standard file extesion for Cobalt source code is `.co`. We'll create and open a file called `hello_world.co`. In it, we write the following code: 
```
@C(extern) fn puts(str: u8 const*);

fn main(): i32 = {
    puts("Hello, World!"c);
    0
};
```
The first line declares that we want to use an externally-defined function `puts()`. 
- The function `puts()` is defined in the C standard library, which Cobalt automatically links against.
- The `@C(extern)` decoration can be thought of as declaring that the function `puts()` is defined in C. On a techincal level, it disables name mangling, sets calling convention, and marks `puts()` as externally defined.
- We then tell Cobalt that the `puts()` function we are looking for takes a single argument (named `str`, though note that the actual name of the parameter is irrelevent) of type `u8 const*`.  
- The type `u8` represents a byte, and the type `u8 const*` represents a pointer to constant bytes. The C function `puts()` expects a single paramater of type `const char*`. A `char` in C is just a byte, so the C type `const char*` corresponds to the Cobalt type `u8 const*`.

Next we define a function `main()` which takes no arguments and returns an `i32`, which is a 32-bit integer.
- The name `main` is not arbitrary; the compiler will look for the function with exactly this name to execute. Executing the program can be thought of as calling the function `main()`.
- Note the `=` sign in the function declaration, and the semicolon at the very end. In Cobalt, functions are [first-class citizens](https://en.wikipedia.org/wiki/First-class_function), though this is only partially implemented at the moment.
- The function first calls `puts()` with the argument `"Hello, World!"c`. String literals, surrounded by quotations marks, have type `u8 const*`. Note the `c` after the literal. This tells Cobalt that the string is a [C string](https://en.wikipedia.org/wiki/C_string_handling), which is what the C function `puts()` expects.
- Finally, the line `0` returns the value `0` from the function. A `return` keyword is not necessary.

### Compiling

The Cobalt compiler, which we'll just call `co` from now on, supports ahead-of-time (aot) and just-in-time (jit) compilation. On a basic level, aot compilation will output an executible file that can be run at any time. Jit compilation will run the program right now, and will not output any file. Let's see how to call both. If your shell cannot find a program named `co`, make sure it's in your path (see Installation above).

Jit looks like:
```
> co jit hello_world.co 
Hello, World!
```

Aot looks like:
```
> co aot hello_world.co 
> ./hello_world
Hello, World!
```

