## 4 Using the preprocessor

define macros from the command line with `-DMACRO_NAME`
e.g.
```sh
gcc -DTEST my_file.c
```
or can be given a value with `-DMACRO=$value`
when defined without a value, they are given the default value `

macros beginning with dunder are a reserved namespace and are automatically defined by the compiler

see predefined macros by running `cpp -dM` on an empty file
system specific macros do not use the dunder prefix
disabled with `-ansi`

e.g. on my system, the following macros are automatically defined with value 1:
```
__APPLE__
__LITTLE_ENDIAN__
```

Macros are not expanded inside strings

> Note that it is a good idea to surround macros by parentheses whenever they are part of an expression.

helps ensure proper operator precedence

