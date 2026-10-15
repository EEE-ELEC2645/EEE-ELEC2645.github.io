---
title: Multi-file C projects
parent: Building C Projects
nav_order: 1
layout: default
---

# Multi-file C projects

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


Small programs can be written entirely inside `main.c`, but as the program grows, this quickly becomes difficult to manage!

We can split the program into separate **modules**, with each module responsible for one part of the project.

A small project might contain:

```text
very_cool_project/
├── main.c
├── very_cool_maths.c
└── very_cool_maths.h
```

The maths module uses two files:

```text
very_cool_maths.h
very_cool_maths.c
```

The `.h` header file describes the functions provided by the module. The `.c` source file contains the function definitions.

## Header files `.h`

A header file normally contains the declarations that other source files need to use.

For example:

```c
// very_cool_maths.h

#ifndef VERY_COOL_MATHS_H
#define VERY_COOL_MATHS_H

int add_exciting_numbers(int first, int second);
int multiply_thrilling_numbers(int first, int second);

#endif
```

The function prototypes tell the compiler:

- the function names
- the parameter types
- the return types

The header does not contain the function bodies.

The `#ifndef`, `#define` and `#endif` lines form a **header guard**. Header guards are explained on a later page.

## Source files `.c`

The source file contains the function definitions:

```c
// very_cool_maths.c

#include "very_cool_maths.h"

int add_exciting_numbers(int first, int second)
{
    return first + second;
}

int multiply_thrilling_numbers(int first, int second)
{
    return first * second;
}
```

The source file includes its own header:

```c
#include "very_cool_maths.h"
```

This allows the compiler to check that the function definitions match the prototypes in the header, if they don't the compiler will report an error.

## Using the module from `main.c`

The program includes the header and calls the functions:

```c
// main.c

#include <stdio.h>
#include "very_cool_maths.h"

int main(void)
{
    int exciting_result = add_exciting_numbers(10, 20);
    int thrilling_result = multiply_thrilling_numbers(4, 5);

    printf("The exciting result is %d!\n", exciting_result);
    printf("The thrilling result is %d!\n", thrilling_result);

    return 0;
}
```

`main.c` does not need to see the function bodies. The header provides enough information for the compiler to check the function calls.

The definitions are compiled separately from `very_cool_maths.c` and combined with `main.c` when the program is built.

## Using quotation marks and angle brackets

Project headers are normally included using quotation marks:

```c
#include "very_cool_maths.h"
```

Standard library headers use angle brackets:

```c
#include <stdio.h>
```

Quotation marks tell the compiler to look for a header belonging to the current project.

Angle brackets tell the compiler to look in the system header locations.

## Compiling the project

Both `.c` files must be included when compiling the program:

```bash
gcc main.c very_cool_maths.c -o very_cool_project
```

We do *not* list the header file on the command line as the source files include the headers they need. The compiler compiles the `.c` files and then combines them into the final executable.

We will look at the compilation and linking steps in more detail on the next page.

## Do not include `.c` files

Include the header:

```c
#include "very_cool_maths.h"
```

Do not include the source file:

```c
#include "very_cool_maths.c"
```

Each `.c` file should be compiled as a separate source file.

Including a `.c` file copies its function definitions into another source file. If the original `.c` file is also compiled, the same functions may be defined more than once and the linker will complain.

## What belongs in each file?

The header file contains the public parts of the module:

```c
// very_cool_maths.h

int add_exciting_numbers(int first, int second);
int multiply_thrilling_numbers(int first, int second);
```

The source file contains the implementation:

```c
// very_cool_maths.c

#include "very_cool_maths.h"

int add_exciting_numbers(int first, int second)
{
    return first + second;
}
```

The main program includes the header and uses the module:

```c
// main.c

#include "very_cool_maths.h"
```

A useful way to remember this is:

```text
Header file:  what the module provides
Source file:  how the module works
```

Functions that are private to one source file can be declared as `static`, as covered on the Static Functions page.