# GNU Make -- A Program for Directing Recompilation
Richard M. Stallman, Roland McGrath, Paul D. Smith

## 1 Overview of `make`

> The make utility automatically determines which pieces of a large program
> need to be recompiled, and issues commands to recompile them.

## 2 An Introduction to Makefiles

> When a target is a file, it needs to be recompiled or relinked if any of its
> prerequisites change. In addition, any prerequisites that are themselves
> automatically generated should be updated first.
