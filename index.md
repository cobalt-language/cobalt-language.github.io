# Cobalt
Cobalt is a high-level, compiled language with similar uses to C++. It aims to be more modern than C++, but offer more freedom than Rust.

## Getting Started
As of right now, you will have to build from source, but after the first release, prebuilt versions will be available. 
LLVM 14 must be installed in order to build.

### Hello, World!
Cobalt doesn't have a standard library at this point, so you have to make a call to a libc function.
```
@extern(C) fn puts(str: i8 const*): null;
@cconv(C) fn main(): i32 = (puts("Hello, World!"); 0);
```
In this example, the `puts` function is declared, and then the `main` function calls it. They need to have the calling convention specified because by default, Cobalt uses LLVM's `fast` calling convetion.
`co`' s built-in invocation of `ld` isn't able to link the CRT library, so you need to use a C compiler to link it:
```
co aot helloworld.co --emit-obj
cc helloworld.o -o helloworld
```
Alternatively, you can do it as a one-liner:
```
co aot helloworld.co --emit-asm -o - | cc -x assembler - -o helloworld
```
## Additional links
- [language documentation](./language)
- [driver documentation](./driver)
- [implementation status](./status)
