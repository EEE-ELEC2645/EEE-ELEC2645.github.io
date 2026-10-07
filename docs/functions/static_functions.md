---
title: Static Functions
parent: Functions
nav_order: 4
layout: default
---

# Static Functions

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>
# Static functions

We previously used `static` with a local variable:

```c
void count_calls(void)
{
    static int count = 0;

    count = count + 1;
}
```

Here, `static` means that `count` keeps its value between function calls.

The same keyword has a different meaning when it is placed before a function:

```c
static void helper_function(void)
{
    // ...
}
```

A static function can only be called from within the `.c` file where it is defined.

It is a bit annoying that C uses the same keyword for both of these!

## Private helper functions

Static functions are normally used for helper functions that are only needed inside one source file.

For example:

```c
#include <stdio.h>

static int double_value(int value);

int main(void)
{
    int result = double_value(5) + 10;

    printf("Result: %d\n", result);

    return 0;
}

static int double_value(int value)
{
    return value * 2;
}
```

The function `double_value()` can be called from anywhere inside this source file.

It cannot be called from another `.c` file because it is declared as `static`.

The prototype also includes `static`:

```c
static int double_value(int value);
```

This must match the function definition:

```c
static int double_value(int value)
{
    return value * 2;
}
```

## Public and private functions

Functions that other source files need to call are declared in a header file:

```c
// calculations.h

int calculate_result(int value);
```

A source file can contain both the public function and any private helpers it needs:

```c
// calculations.c

#include "calculations.h"

static int double_value(int value);

int calculate_result(int value)
{
    return double_value(value) + 10;
}

static int double_value(int value)
{
    return value * 2;
}
```

Here:

- `calculate_result()` is public and is declared in `calculations.h`
- `double_value()` is a private helper and is declared as `static`
- `double_value()` does not appear in the header file

Another source file can call:

```c
calculate_result(5);
```

but it cannot call:

```c
double_value(5);
```

## The two meanings of `static`

1. Inside a function:

```c
void count_calls(void)
{
    static int count = 0;
}
```

In this case `static` means:

> Keep the value of this variable between function calls.

2. Before a function:

```c
static void helper_function(void)
{
}
```

Here `static` means:

> Keep this function private to the current source file.

When a helper function is only used in one `.c` file, declare it as `static` and do not put it in the header file.
