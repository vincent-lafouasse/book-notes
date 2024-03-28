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
