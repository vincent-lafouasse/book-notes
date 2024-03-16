# An introduction to GCC

## 1 Introduction

> Every Unix-like operating system needs a C compiler, and as there were no free compilers in existence at that time, the GNU Project had to develop one from scratch.

features :

- emphasis on portability, from 64-bit CPUs to DSPs
    - able to run on a lot of systems
- cross compilation
    - able to write binaries for a lot of systems
    - especially systems that can't run gcc itself
    - embedded systems (no gcc on my fridge)
- many frontends, ie support for many languages
- modularity: easy to add frontends, easy to add end architectures
- GPL ofc

## 2 Compiling C

- `-o out` usually goes as last argument
- warnings help us detect UBs

> The recompilation of large source files can be very time-consuming.
    - proper build is important

> An object file contains machine code where any references to the memory addresses of functions (or variables) in other files are left undefined. This allows source files to be compiled without direct reference to each other. The linker fills in these missing addresses when it produces the executable.
??

No need for `-Wall` when linking, files have already been succesfully _compiled_
i.e. `-Wall` goes in `CFLAGS`, not `LDFLAGS`

> A more sophisticated makefile would usually contain additional targets for installation (`make
install`) and testing (`make check`).

> A library is a collection of precompiled object files which can be linked into programs.
> [Static libraries are] used by the linker to resolve references to functions AT COMPILE-TIME 

standard libs in `/usr/lib` and `/lib` on Unix-like systems
headers in `/usr/include` 
(not on macOS)

default library `libc.a` (always linked ?)

compile with `libm.a` explicitely will full path
`gcc -Wall calc.c /usr/lib/libm.a -o calc`

> [A] library containing the definition of a function should appear after any source files or object files which use it.
`cc -Wall -lm calc.c -o calc` incorrect bc `cc` looking for lib functions used
by `calc.c` AFTER `calc.c` in command
> The option ‘-lm’ should appear after the file ‘calc.c’.

-> if `libglpk.a` uses functions of `libm.a`, we should order them this way:
`cc data.c -lgplk -lm`

> Most current linkers will search all libraries, regardless of order, but since some do not do this it is best to follow the convention of ordering libraries from left to right.
> This is worth keeping in mind if you ever encounter unexpected problems with undefined references, and all the necessary libraries appear to be present on the command line.

## 3 Compilation options

> The list of directories for header files is often referred to as the include path, and the list of directories for libraries as the library search path or link path.

paths are ordered, the first match is used

default include path
`/usr/local/include/:/usr/include/`

default link path
`/usr/local/lib/:/usr/lib/`

`-I` `-L` for custom paths

or set paths with env variables
`C_INCLUDE_PATH`
`CPLUS_INCLUDE_PATH`
`LIBRARY_PATH`
this way, no need to use `-L` and `-I` flags

but using the flags is more commo than using env variables

### dynamic vs static linking

dynamically linked library == shared library (`*.so`)
shared library are not embedded into the final executable
-> can be used by many binaries, hence shared
linked differently at runtime
allows for change in the library without recompilation

> Because of these advantages gcc compiles programs to use shared libraries by
> default on most systems, if they are available. Whenever a static library
> ‘libNAME.a’ would be used for linking with the option ‘-lNAME ’ the compiler
> first checks for an alternative shared library with the same name and a ‘.so’
> extension.

### C standards

gcc compiles in the GNU dialect by default
`-ansi` disables GNU extensions  that confllict with the ISO standard
e.g. `asm` is a GNU keyword extension for native ASM in C
it is not a reserved identifier in ANSI C

exemple of GNU extension keywords : 
`asm`
`inline`
`typeof` (oooh utile)

example of GNU extensions:
`M_PI` in `math.h`

possibility to reactivate GNU extensions by defining the `GNU_SOURCE` macro
