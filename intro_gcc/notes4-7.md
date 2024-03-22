## 4 Using the preprocessor

define macros from the command line with `-DMACRO_NAME`
e.g.
```sh
gcc -DTEST my_file.c
```
or can be given a value with `-DMACRO=$value`
when defined without a value, they are given the default value `

macros beginning with dunder are a reserved namespace and are automatically
defined by the compiler

see predefined macros by running `cpp -dM` on an empty file
system specific macros do not use the dunder prefix
disabled with `-ansi`

e.g. on my system, the following macros are automatically defined with value 1:
```
__APPLE__
__LITTLE_ENDIAN__
```

Macros are not expanded inside strings

> Note that it is a good idea to surround macros by parentheses whenever they
> are part of an expression.

helps ensure proper operator precedence

empty quotes are treated as defined but expand to nothing

> The ‘-E’ option causes gcc to run the preprocessor, display the expanded
> output, and then exit without compiling the resulting source code.

> can be useful for examining the effect of system header files, and finding
> declarations of system functions

use `-save-temps` to save the preprocessed output in a `.i`, as well as
assembly `.s` and object `.o` files

## 5 Compiling for debugging

symbol table

core dump good

> When a program exits abnormally (i.e. crashes) the operating system can write
> out a core file (usually named ‘core’) which contains the in-memory state of
> the program at the time it crashed. This file is often referred to as a core
> dump.1 Combined with information from the symbol table produced by ‘-g’, the
> core dump can be used to find the line where the program stopped, and the
> values of its variables at that point

> Some systems are configured not to write core files by default, since the
> files can be large and rapidly fill up the available disk space on a system.
> In the GNU Bash shell the command ulimit -c controls the maximum size of core
> files. If the size limit is zero, no core files are produced.

run `ulimit -c` to check for core file max size
on my machine it's 0, and ive also never actually seen a core dump file

in lldb
set variables with
`expr p = $value`

can even malloc stuff

`expr p = (int*)malloc(sizeof(int))`

buncha useful stuff, but more complex stuff is offtopic for now ig

## 6 Compiling with optimization

optimization different depending on system and other stuff, so optimization
hard, i.e. gcc very smart yes

### source level optimization
#### common subexpression elimination, ie CSE

if it's already computed, gcc may be big brain enough to know it doesnt need to be evaluated again and might reuse previous results
THAT'S common subexpression elimination

done automatically when optimization are activated

very powerful

#### inlining

increase efficiency of frequently-called functions

function calls have a cost, storing arguments in registers, jump to function,
execute, return to original point
-> function call overhead

-> fixed by inlining

function call overhead is kinda small but may become significant if the function is small and used in a loop

use keyword `inline` to tell gcc that a function should be inlined if possible

### speed-space tradeoff

faster vs bigger binary, in one direction or the other

#### loop unrolling
loops need to check a condition at each iteration
-> unrolling might be beneficial
> allows the compiler to use parallelism on processors that support it.
