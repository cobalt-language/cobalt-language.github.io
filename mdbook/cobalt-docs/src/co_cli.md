# Compiler CLI

This page details the various commands and options you can specify in the compiler. 
If you are just looking for how to get started compiling some code, this might not 
be the best place to look (at first). 

## Top level 

These are options and commands that can be specified immediately after invoking `co`, e.g.
```
> co <OPTION>
```

The following return immediately:

| Option | Description |
|--------|----------|
| `-h`, `--help` | Print help. |
| `-V`, `--version`, `version` | Print version. |

The following are commands, and entry points to more options:
  
| Command | Description |
|---------|--------|
| `version` | Print version information. |
| `debug`   | Debug compiler stuff. |
| `aot`     | AOT compile a file. |
| `jit`     | JIT compile and run a file. |
| `check`   | Check a file for errors. |
| `multi`   | Multi-file utilities. |
| `project` | Project-related utilities. |
| `package` | Package-related utilities (not currently supported). |
| `help`    | Print help or the help of the given subcommand(s). |

In particular, you can query the compiler to provide help on any of these commands
by using the `help` command, e.g. 

```
> co help aot
```

## aot

The model is 
```
> co aot [OPTION] <INPUT>
```
`<INPUT>` is the primary file to compile. The following (optional) options are supported:

| Option | Description |
|--------|-------------|
| <code>[-o &#x7c; --output] \<OUTPUT\></code> | Specify an output file name. |
| `-l <LINKED>` | Specify libraries to link. |
| `-L <LINK_DIRS>` | Specify directories to look for libraries in. |
| `-H <HEADERS>` | Specify Cobalt headers to include. |
| `-t, --triple <TRIPLE>` | Specify the target triple to build for. |
| <code>[-e &#x7c; --emit] \<EMIT\></code> | Specify the type of file to emit. |
| <code>[-p &#x7c; --profile] \<PROFILE\></code> | Specify which optimization profile to use. |
| <code>[-d &#x7c; --debug]</code> | Emit symbols with debug mangling. |
| `--no-default-link` | Don't search default directories for libraries. |
| `--timings` | Print timings. |
| `-h, --help` | Print help |

### Notes

For `-o`:
- If no output file name is specified, one is chosen based on the input filename.

For `-l`:
- The compiler automatically looks in the working directory.
- Specifying libraries is similar to `gcc` in that you need not specify the `lib`
prefix to the library file, if there is one. A file called `libadd.dylib` can be specified 
as `-l add`.

For `-e`:
- The possible options are 

| Option | Description |
|--------|-------------|
| `exe` | executable _(default)_ |
| `lib` | dynamic library |
| `obj` | object file |
| `raw-obj` | object file, minus Cobalt headers |
| `asm` | assembly |
| `llvm` | LLVM IR |
| `bc` | LLVM bitcode |
| `header` | Cobalt library header |

For `-p`:
- The following optimization profiles are built-in:

| Profile |
|---------|
| <code>none &#x7c; 0</code> | 
| <code>less &#x7c; 1</code> | 
| <code>default &#x7c; 2</code> | 
| <code>aggressive &#x7c; 3</code> | 

- Profiles can also be specified in files.
- The syntax of the profile file is as follows: each line has a pass, with an optional 
additional argument (only two passes take these). Empty lines are ignored, and anything 
after a `#` is a comment. Here is an example:
```
psccp
IPSCCP # This is the same as the previous one
scalar-repl-aggregates = 5 # both the pass and the value are trimmed
internalize = true
```
- If you specify a name of a file, the following locations are searched, in this order:

| Location |
|----------|
| `$COBALT_DIR/profiles/` |
| `$HOME/.cobalt/profiles/` |
| `$HOME/.config/cobalt/profiles/`|
| `/usr/local/share/cobalt/profiles/` |
| `/usr/share/cobalt/profiles/` |

## jit

Just-in-time compilation can be achieved via 
```
> co jit <FILE>
```
with most of the options above being valid here too.

## check

`co check` goes through the frontend stages of compilation, but does not emit a compiled
output. It takes most of the same commands as `co jit`. 

## Debug-only subcommands

The following commands are only available on debug builds of the compiler. They begin as 
```
> co dbg <SUBCOMMAND>
```
The following subcommands are available:

| Subcommand | Description |
|------------|-------------|
| `parse` | Print generated AST. |
| `llvm` | Print unoptimized llvm output. |
| `parse-header` | Loads header files and prints the symbols. |

### Notes and examples

For `parse`:
- Directly input code via the `-c` flag, e.g.
```
> co dbg parse -c 'let x = 10;'
```
- Show the locations of the AST nodes in the source file with the `-l` flag, e.g.
```
> co dbg parse -l test.co
```
- You can supply multiple input files, e.g. 
```
> co dbg parse test1.co test2.co
```

For `parse-header`:
- Sample usage:
```
> co aot --header test.coh
> co dbg parse-header test.coh
```

