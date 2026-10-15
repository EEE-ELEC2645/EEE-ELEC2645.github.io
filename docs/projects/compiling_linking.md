---
title: Compiling & linking
parent: Building C Projects
nav_order: 2
layout: default
---

# Compiling and linking
<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

A project containing several `.c` files must be compiled and linked before it can run.

Our example project contains:

```text
very_cool_project/
├── main.c
├── very_cool_maths.c
└── very_cool_maths.h
```

The build process has two main stages:

```text
.c source files
      |
      | compile
      v
.o object files
      |
      | link
      v
executable program
```
## What are compiling and linking?

Building a C program has two main stages:

- **Compiling** translates each `.c` file into machine code stored in an object file:

```text
    main.c -> main.o
```

- **Linking** combines the object files into one executable program:

```text
    main.o + very_cool_maths.o -> very_cool_project
```

A useful way to remember the difference is:

```text
    Compile: translate each source file into machine code
    Link:    combine the compiled files into one program
```

## Building everything in one command

For a small project, we can compile all the source files using one command:

```bash
gcc main.c very_cool_maths.c -o very_cool_project.out
```

The source files are:

```text
main.c
very_cool_maths.c
```

The `-o` option sets the name of the executable:

```text
very_cool_project
```

We do not include the header file in the command. The source files include the headers they need:

```c
#include "very_cool_maths.h"
```

On the Linux Codespace, run the finished program using:

```bash
./very_cool_project
```

## Compiling source files separately

We can also compile each source file separately using `-c`:

```bash
gcc -c main.c
gcc -c very_cool_maths.c
```

The `-c` option tells GCC to compile the source file without trying to create the final executable.

This produces two object files:

```text
main.o
very_cool_maths.o
```

An object file contains compiled machine code, but it is not yet a complete program that we can run.

## Linking the object files

The object files are combined by the linker:

```bash
gcc main.o very_cool_maths.o -o very_cool_project
```

This produces the executable:

```text
very_cool_project
```

The complete sequence is therefore:

```bash
gcc -c main.c
gcc -c very_cool_maths.c
gcc main.o very_cool_maths.o -o very_cool_project
```

The first two commands compile the source files. The final command links the object files.

## Why compile files separately?

This seems like an unnecessary complication! Why bother doing this? Well, suppose we change only:

```text
very_cool_maths.c
```

We do not need to compile `main.c` again. We can rebuild only the file that changed:

```bash
gcc -c very_cool_maths.c
```

We then link the new object file with the existing `main.o`:

```bash
gcc main.o very_cool_maths.o -o very_cool_project
```

This does not make much difference for our very cool but very small project. It becomes useful when a project contains many source files and takes longer to compile, like your projects in Unit 3 & 4.

Makefiles automate these commands and work out which files need to be rebuilt - very handy!

## Compiler errors vs linker errors

The type of error helps tell us where in the process of creating the executable the problem occurred.

### Compiler errors

A compiler error means that one of the source files could not be compiled.

This is normally caused by a problem in the C code, like missing semicolons, undeclared variables or incorrect syntax.

For example:

```c
int add_exciting_numbers(int first, int second)
{
    return first + second
}
```

This is missing a semicolon, so `very_cool_maths.c` cannot be compiled into `very_cool_maths.o`.

### Linker errors

A linker error happens after the source files have compiled, but the object files cannot be combined into the final program.

A common example is:

```text
undefined reference to `add_exciting_numbers'
```

This means the compiler saw the function declaration, but the linker could not find its definition.

For example, we may have forgotten to include `very_cool_maths.c` in the build:

```bash
gcc main.c -o very_cool_project
```

The correct command is:

```bash
gcc main.c very_cool_maths.c -o very_cool_project
```

A linker can also report that a function has been defined more than once. This often happens when a `.c` file has been included instead of its header, or you forgot header guards.

A useful way to remember the difference is:

```text
Compiler error: one of the source files could not be compiled
Linker error: the files compiled, but could not be combined into a program
```

If you see an undefined reference, start by checking that the relevant `.c` file is included in the build.

## The complete build process

For our example project:

```text
main.c ──────────────────► main.o
                                  \
                                   ► very_cool_project
                                  /
very_cool_maths.c ───────► very_cool_maths.o
```

The header file is included while each source file is compiled:

```text
very_cool_maths.h
        |
        ├── included by main.c
        |
        └── included by very_cool_maths.c
```

The header provides declarations. It does not produce its own object file.

For now, the commands to remember are:

```bash
gcc -c main.c
gcc -c very_cool_maths.c
gcc main.o very_cool_maths.o -o very_cool_project
```

In practice, we do not want to type all of these commands whenever something changes. Later in this section, we will put the build instructions into a Makefile.