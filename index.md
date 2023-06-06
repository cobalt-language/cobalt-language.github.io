# Cobalt
Cobalt is a high-level, compiled language with similar uses to C++. It aims to be more modern than C++, but offer more freedom than Rust.

## Getting Started
Prebuilt versions are available from the [releases page](https://github.com/matt-cornell/cobalt-lang/releases/latest). As of right now, only the Linux `x86_64` build is available.
In order to build from source, Git, SSH, and LLVM 14 must be installed.

### Hello, World!
Cobalt doesn't have a standard library at this point, so you have to make a call to a libc function.
```
@C(extern) fn puts(str: u8 const*);
fn main(): i32 = (puts("Hello, World!"c); 0);
```
The `@C` annotation sets disables name mangling, sets calling convention, and marks `puts` as externally defined.
To compile, use `co aot helloworld.co`, and run the `helloworld` executable that it generates (`helloworld.exe` if the target is Windows).
You can also use the JIT compiler with `co jit helloworld.co`.
## Additional links
- [language documentation](./language)
- [driver documentation](./driver)
- [implementation status](./status)
