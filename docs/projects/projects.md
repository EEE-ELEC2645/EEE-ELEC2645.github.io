---
title: Building C Projects
nav_order: 5
layout: default
---

# Building C projects

For when your programs get a bit too big for one file!

A small program can live entirely inside `main.c`:

```text
very_cool_project/
└── main.c
```

As the program grows, we can split it into separate files:

```text
very_cool_project/
├── main.c
├── very_cool_maths.c
├── very_cool_maths.h
└── Makefile
```

The header file tells the rest of the project which functions are available:

```c
// very_cool_maths.h

#ifndef VERY_COOL_MATHS_H
#define VERY_COOL_MATHS_H

int add_exciting_numbers(int first, int second);

#endif
```

The source file contains the actual function definition:

```c
// very_cool_maths.c

#include "very_cool_maths.h"

int add_exciting_numbers(int first, int second)
{
    return first + second;
}
```

The function can then be used from `main.c`:

```c
// main.c

#include <stdio.h>
#include "very_cool_maths.h"

int main(void)
{
    int incredibly_exciting_result =
        add_exciting_numbers(10, 20);
    printf(
        "The very exciting result is %d!\n",
        incredibly_exciting_result
    );
    return 0;
}
```

This section covers how these files fit together and how they are compiled into one program.
