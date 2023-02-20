# `co`, the Cobalt compiler
`co` is the main driver. It can compile, run, and verify programs. Building projects and package management are planned.

## `co help`- explain features
`co help` prints help messages for subcommands.
Usage:
```bash
$ co help
$ co help <subcommand>
$ co help error <code> (not implemented)
```
## `co aot`- AOT compilation
`co aot` AOT compiles a file.
Usage:
```bash
$ co aot <file>
$ co aot <file> -o <output>
```
### Output type:
The following outputs can be generated:
- `--exe`- executable
- `--lib`- [Cobalt library](./library)
- `--emit-asm`- native assembly
- `--emit-obj`- native object file
- `--emit-llvm`- LLVM IR
- `--emit-bc`- LLVM bitcode
- `--header`- Library header
### Optimization profiles
Optimization profiles can be specified with the `-p` flag.
Profiles are searched for in the following order:
- `$COBALT_DIR/profiles/`
- `$HOME/.cobalt/profiles/`
- `$HOME/.config/cobalt/profiles/`
- `/usr/local/share/cobalt/profiles/`
- `/usr/share/cobalt/profiles/`
- If the profile can't be located in any of these paths, the following built-in profiles are available:
  - `none`
  - `less`
  - `default`
  - `aggressive`
#### Profile file syntax
Each line has a pass, with an optional additional argument (only used in two passes). Empty lines are ignored, and anything after a `#` is a comment.
Example:
```
ipsccp
IPSCCP # This is the same as the previous one
scalar-repl-aggregates = 5 # both the pass and the value are trimmed
internalize = true
```
### Linking libraries
Libraries can be linked with the `-l` flag, and the (recursive) search directory is specified with `-L`
Usage:
```
$ co aot test.co -L /usr/lib -l c -l m # link libc and libm, both of which are somewhere in /usr/lib
$ co aot test.co -Lll /usr/lib c m # because of the way flags are parsed, you can do this
```
### Continuing after errors
By default, compilation exits early if errors are detected. Compilation can be forced to continue with the `-c` or `--continue` flag.
## `co jit`- JIT compilation
`co jit` JIT compiles a file.
Usage:
```bash
$ co jit <file>
```
Profiles and libraries are specified the same way as they are with the `aot` subcommand.
## `co check`- Code verification
`co check` goes through the frontend stages of compilation, but does not emit a compiled output. It only takes a file as an input.
## Debug-only subcommands
The following commands are only available on debug builds:
### `co lex`- print generated tokens
Usage:
```bash
$ co lex test1.co test2.co
$ co lex -l test.co # prints token locations
$ co lex -c 'let x = 10;'
```
### `co parse`- print generated AST
This is used the same as the `lex` subcommand.
Usage:
```bash
$ co parse test1.co test2.co
$ co parse -l test.co # prints location of each AST node
$ co parse -c 'let x = 10;'
```
### `co llvm`- print unoptimized LLVM output
Usage:
```bash
$ co llvm test.co
```
### `co read-lib`- read library header
This loads files from each of its arguments as library headers and prints the symbols.
Note that `-` *cannot* be used here to read from stdin. Use `/dev/stdin` if you really need to do so.
Usage:
```bash
$ co aot --header test.co
$ co read-lib test.coh
```
### `co lib-header`- generate library header
This is deprecated in favor of `co aot --header`
