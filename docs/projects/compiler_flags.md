---
title: Compiler flags
parent: Building C Projects
nav_order: 3
layout: default
---

# Compiler flags
<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Compiler flags change how GCC builds the program.

Our usual build command looks something like this:

```bash
gcc -std=c11 -Wall -Wextra -Wpedantic main.c very_cool_maths.c -o very_cool_project
```

This page explains the options used in that command.

## Choosing the C standard

The `-std` option tells GCC which version of the C standard to use:

```bash
-std=c11
```

For example:

```bash
gcc -std=c11 main.c -o main
```

Specifying the standard makes the version of C being used explicit, rather than relying on the compiler default.

We normally use C11 for the command-line activities:

```bash
-std=c11
```

You may encounter other versions such as:

```bash
-std=c99
-std=c17
-std=c23
```

The STM32 projects may use a different standard depending on its build configuration.

## Enabling compiler warnings

Warnings point out code that is valid C but may contain a mistake.

For example:

```c
int main(void)
{
    int value = 10;

    return 0;
}
```

The variable `value` is created but never used. The program may still compile, but a warning can draw our attention to the unused variable.

We normally use these warning options:

```bash
-Wall -Wextra -Wpedantic
```

### `-Wall`

`-Wall` enables a useful collection of common warnings:

```bash
-Wall
```

Despite the name, it does not enable every warning supported by GCC (look, I dont know why C is like this ok?). It enables a good general set for finding common mistakes.

### `-Wextra`

`-Wextra` enables some additional warnings that are not included in `-Wall`:

```bash
-Wextra
```

For example, it can warn about some unused function parameters.

### `-Wpedantic`

`-Wpedantic` warns when the code does not strictly follow the selected C standard:

```bash
-Wpedantic
```

It is interpreted alongside the `-std` option:

```bash
-std=c11 -Wpedantic
```

You may not notice much difference in ordinary code because most of our examples already use standard C.

## Treating warnings as errors

The `-Werror` option turns enabled warnings into errors:

```bash
-Werror
```

For example:

```bash
gcc -Wall -Wextra -Wpedantic -Werror main.c -o main
```

Without `-Werror`, GCC may create the executable even when warnings are present.

With `-Werror`, the build stops until the warnings have been resolved.

This is useful for the activities because it stops warnings from being ignored. However, remember that `-Werror` does not enable warnings itself. It only changes the warnings already enabled by options such as:

```bash
-Wall -Wextra -Wpedantic
```

## Naming the output file

The `-o` option sets the name of the output file:

```bash
-o very_cool_project
```

For example:

```bash
gcc main.c -o very_cool_project
```

Without `-o`, GCC normally creates an executable called:

```text
a.out
```

On the Linux Codespace, run the program using:

```bash
./very_cool_project
```

## Compiling without linking

The `-c` option compiles a source file into an object file without linking it:

```bash
gcc -c main.c
```

This creates:

```text
main.o
```

For a multi-file project:

```bash
gcc -c main.c
gcc -c very_cool_maths.c
```

The resulting object files can then be linked:

```bash
gcc main.o very_cool_maths.o -o very_cool_project
```

The difference between compilation and linking is covered on the previous page.

## Adding an include directory

The `-I` option adds a directory to the path used when searching for header files.

For example, suppose the project is arranged like this:

```text
very_cool_project/
├── include/
│   └── very_cool_maths.h
└── src/
    ├── main.c
    └── very_cool_maths.c
```

The source files include the header using:

```c
#include "very_cool_maths.h"
```

We can tell GCC to search the `include` directory using:

```bash
-I include
```

The complete command is:

```bash
gcc -I include src/main.c src/very_cool_maths.c -o very_cool_project
```

## Linking the maths library

Some functions from `<math.h>` require the maths library to be linked separately on Linux.

For example:

```c
#include <math.h>

double result = sqrt(25.0);
```

Compile the program using:

```bash
gcc main.c -o main -lm
```

The `-lm` option tells the linker to include the maths library.

It is normally placed after the source or object files:

```bash
gcc main.o calculations.o -o main -lm
```

If the program uses a maths function but the library is missing, the linker may report an undefined reference:

```text
undefined reference to `sqrt'
```

This is a linker error rather than a compiler error.

## A typical build command

For a small multi-file project, we might use:

```bash
gcc -std=c11 -Wall -Wextra -Wpedantic -Werror main.c very_cool_maths.c -o very_cool_project
```

The command can be read from left to right:

```text
gcc
    use GCC

-std=c11
    compile as C11

-Wall -Wextra -Wpedantic
    enable useful warnings

-Werror
    treat those warnings as errors

main.c very_cool_maths.c
    source files to compile

-o very_cool_project
    name of the executable
```

For separate compilation, use:

```bash
gcc -std=c11 -Wall -Wextra -Wpedantic -Werror -c main.c
```

and:

```bash
gcc -std=c11 -Wall -Wextra -Wpedantic -Werror -c very_cool_maths.c
```

Then link the object files:

```bash
gcc main.o very_cool_maths.o -o very_cool_project
```

Later we will look at `make` and other ways of creating bigger projects that save all this typing! 