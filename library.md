# Libraries
Cobalt's libraries can be distributed by themselves, since they contain all of the necessary metadata for the compiler.
Libraries can be generated with the `--lib` flag on the `aot` subcommand.
## Implementation
A library is basically a shared object (or dylib or dll if you use a different OS). The metadata is stored in the `.colib` section, serialized as a header.
Note that a lot of the ABI will likely change before v1.0.0, so recompiling the libraries will be common.
## Headers
Library headers can be generated with `--header` flag on the `aot` subcommand. Library headers frequently contain null terminators, so they should not be used in C strings.
If you care about how they're implemented, the code can be found mostly in [`varmap.rs`](https://github.com/matt-cornell/cobalt-lang/blob/main/src/cobalt/varmap.rs), with a bit of code in [`types.rs`](https://github.com/matt-cornell/cobalt-lang/blob/main/src/cobalt/types.rs).
