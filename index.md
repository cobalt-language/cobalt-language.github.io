# Cobalt
Cobalt is a high-level, compiled language with similar uses to C++. It aims to be more modern than C++, but offer more freedom than Rust.

## Getting Started
As of right now, you will have to build from source, but after the first release, prebuilt versions will be available. 
LLVM 14 must be installed in order to build.

### Hello, World!
Cobalt doesn't have a standard library, or even proper FFI at this point. Therefore, a "Hello, World!" program can't be directly generated. Here's how you do it:
- Create a file called `test.co`, and write this to it:
  ```
  fn puts(str: i8 const*): null;
  fn main(): i32 = {
    puts("Hello, World!");
    0
  };
  ```
- Compile it with `co aot test.co --emit-llvm -p none`. If optimizations are enabled, the call to `puts` gets optimized out.
- Next, open `test.ll`. Replace:
  ```
  define void @puts(i8* %0) {
  entry:
    ret void
  }
  ```
  with:
  ```
  declare void @puts(i8* %0)
  ```
  - This prevents LLVM from overriding the definition of `puts` in the standard library.
- Finally, compile and link it with `clang test.ll -o test`. You now have a "Hello, World!" program.

## Additional links
- [language documentation](./language)
- [compiler documentation](./compiler)
- [implementation status](./status)
