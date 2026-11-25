---
title: C Basics
nav_order: 3
layout: default
---

# C basics

Where every embedded systems journey begins: with a missing semicolon.

This section covers the building blocks of every C program: variables and types, operators, decisions and loops. Here is a whole C program:

```c
#include <stdio.h>

int main(void)
{
    int semicolons_forgotten = 0;

    printf("C is the best!\n");
    printf("Semicolons forgotten today: %d\n", semicolons_forgotten);
    return 0;
}
```

This prints:

```text
C is the best!
Semicolons forgotten today: 0
```

`#include <stdio.h>` gives us `printf()`, every program starts running at `main()`, and `return 0;` tells the operating system the program finished without a problem.

The pages are in roughly the order you meet them in Unit 1.1. Start with Basic data types and work down if C is new to you, or jump to the topic you need if you have programmed before. Volatile and static comes back when we write interrupts on the STM32.

Once you can write loops and decisions, [Arrays, Strings and Structs]({% link docs/C_Basics/arrays_strings_structs.md %}) shows how to group data together.
