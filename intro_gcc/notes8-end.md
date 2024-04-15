## 8 Platform-specific options

### x86

ability to compile for generic x86 but also for a specific processor for better performance

eg. for Pentium 4 use `-march=pentium4`, for AMD Athlon use `-march=athlon`
faster but obviously less portable

`-mcpu=CPU` more of a compromise
> it generates code that is tuned for a specific processor, in terms of
> instruction scheduling, but does not use any instructions which are not
> available on other CPUs in the x86 family 

less performance but more convenient in practice

ability to use extra instructions for extended instruction sets like MMX SSE etc
will only run on processors supporting the extensions
or else, `Illegal instruction`

on x86-64 systems, use `-m32` to generate 32 bit code

### DEC Alpha options

no idea what the fuck that means

### SPARC options 

no idea what the fuck that means

### POWER/PowerPC options

you know the drill

### Floating-point issues

defined at the bit level by the IEEE-754 standard

noticeable discrepancies can appear when porting as the internal precision may differ
extended vs double-precision
different intermediate values -> differences in rounding and under/overflow behaviour

### portability of signed and unsigned types

difference in default signed-ness of `char`

```c
#include <stdio.h>

int main(void)
{
    char c = 255;
    if (c > 128) {
        printf ("char is unsigned (c = %d)\n", c);
    } else {
        printf ("char is signed (c = %d)\n", c);
    }
    return 0;
}
```

> With an unsigned char, the variable c takes the value 255, but with a signed
> char it becomes −1

explicitely use `signed char` and `unsigned char` if it's important

or gcc has flags `-fsigned-char` and `funsigned-char`

> The warning message “comparison is always true/false due to limited range of
> data type” is one symptom of code which assumes a definition of char which is
> different from the actual type.

may cause problems if chars can never take the value -1 that signals a EOF
problems with getc fgetc getchar

```c
char c;

while ((c = getchar()) != EOF))
{
    /* trucs */
}
```

not portable, if char is unsigned by default, we will never get out of the loop

if char is unsigned, the return value should be stored in a (signed) int, checked as an int and then cast to char

## 9 Troubleshooting

`gcc --help`, maybe even with the `-v` flag
will produce a bunch of shit, redirect to `more` or to a file

`-v` can also be used when compiling to get the exact sequence of commands to compile, link etc
>  It displays the full directory paths used to search for header files and
>  libraries, the predefined preprocessor symbols, and the object files and
>  libraries used for linking

gdb can attach on processes
pauses the process
cab killthe process and create a core dump, unlike stopping the process with C-c

use `ulimit -v` to limit (virtual) memory usage (in kb)
> provides a way to test how robustly out of memory conditions are handled. An
> artificially low limit can be used to simulate running out of memory—a
> well-written program should not crash in this case.

other cool uses for `ulimit`
e.g. `-p` to limit number of child processes
`ulimit -a` or `help ulimit` for more info


## 10 Compiler-related tools

### Creating a lib with the GNU archiver

`ar` combines a collection of object files into a single archive file, ie a lib

e.g.

```sh
ar cr libhello.a hello_fn.o bye_fn.o
```

`cr` for create and replace
create it if it doesnt exist
replace files if new files with same name

examine toc with `t` flag

### Using the profiler `gprof`

for measuring the performance of a program
> records the number of calls to each function and the amount of time spent there

compile and link with `-pg` profiling option (dont forget)
-> creates an instrumented executable
-> contains more instructions

-> efforts to decrease the runtime of a program should concentrate on the
    functions that most time is spent on

### Coverage testing with `gcov`

analyses the number of times each line is run
-> find dead code or untested code

allows for focused optimization in coordination with `gprof`

compile with `-fprofile-arcs -ftest-coverage`

`-ftest-coverage` counts number of time each line is executed
`-fprofile-arcs` counts number of time each branch is taken

writes to `.bb` `.bbg` and `.da` files

running `gcov` on source file will create `.gcov` file, which looks like source code but with annotations
`######` will appear on lines never executed
greping the hashes is useful

## 11 How the compiler works

multi stage process including the compiler itself (through any frontend), the assembler `as` and the linker `ld`
> The complete set of tools used in the compilation process is referred to as a toolchain


### Overview

- preprocessing (expanding macros)
- compilation (source code to asm)
- assembly (asm to machine code)
- linking (create the final binary)

the individual commands are not needed, everything is done by gcc and can be seen with the `-v` option

we will be working with the following file

`hello.c`:
```c
#include <stdio.h>

int
main(void)
{
    printf("Hello, world!\n");
    return 0;
}
```

### Preprocessing

expand maccros and included header files
run with
```sh
cpp hello.c > hello.i
```

`.i` for C and `.ii` for C++ by convention
usually not saved to disk except if `-save-temps` option used

### The compiler

command line `-S` instructs gcc to convert into assembly without creating object file

```sh
gcc -Wall -S hello.i # book version
```

or

```sh
gcc -Wall -S hello.c # what actually works on my machine
```

### The assembler

convert asm into machine code and generate an object file
addresses of external functions still undefined (ie still no linking)

```sh
as hello.s -o hello.o
```

at this point, the reference to `printf` is still undefined

### The linker

linking object files together to create the executable

in practice, the binary requires external functions from system and C runtime libraries
-> actual linking commands used internally by GCC are complicated

there is never any need to type it directly, the entire process is handled by gcc and invoked by

```sh
gcc hello.o
```

tada

## 12 Examining compiled files

### `file` command

```sh
file exec
```

can reveal a lot of information

e.g.

- ELF : Executable and Linking Format, or sometimes COFF Common Object File Format on older stuff
- 32-bit : word size
- LSB : Least significant byte first word ordering, vs MSB
