---
title: Static functions
parent: Functions
nav_order: 4
layout: default
---

# Static functions

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

In [`volatile` and `static`]({% link docs/C_Basics/volatile.md %}#the-static-keyword) we used `static` with a local variable:

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

## Private variables in a source file

A variable declared outside every function, at the top of a `.c` file, can be used by every function in that file. This is called *file scope*. Adding `static` keeps the variable private to that file, in the same way as a static function.

The Unit 2 project template uses this to count how many calculations its library has done:

```c
// funcs.c

#include "funcs.h"

static int operation_count = 0;  // private to funcs.c

int square_int(int value)
{
    operation_count++;
    return value * value;
}
```

Every function in `funcs.c` can read and change `operation_count`. Like a static local variable, it keeps its value for the whole time the program runs. Unlike a static local variable, it is not tied to one function.

Other source files cannot touch it. If `main.c` tries:

```c
operation_count = 0;
```

the compiler reports:

```text
main.c:12:5: error: ‘operation_count’ undeclared (first use in this function)
   12 |     operation_count = 0;
      |     ^~~~~~~~~~~~~~~
```

Declaring it in `main.c` with `extern int operation_count;` does not get round this. The compiler accepts it, but the linker cannot find a variable that `funcs.c` has kept private:

```text
main.c:(.text+0x61): undefined reference to `operation_count'
collect2: error: ld returned 1 exit status
```

Instead, `funcs.c` provides public functions, declared in `funcs.h`, for the things other files are allowed to do:

```c
int funcs_get_operation_count(void)
{
    return operation_count;
}

void funcs_reset_operation_count(void)
{
    operation_count = 0;
}
```

The library stays in control of its own data. Other files can read the count or reset it to zero, but nothing else can set it to `-5` by mistake. The reset function is also useful for [testing]({% link docs/debugging/testing.md %}), because each test can start from a known value.

## Complete example

```c
// funcs.h

#ifndef FUNCS_H
#define FUNCS_H

int square_int(int value);
int cube_int(int value);
int funcs_get_operation_count(void);
void funcs_reset_operation_count(void);

#endif
```

```c
// funcs.c

#include "funcs.h"

static int operation_count = 0;  // private to funcs.c

int square_int(int value)
{
    operation_count++;
    return value * value;
}

int cube_int(int value)
{
    operation_count++;
    return value * value * value;
}

int funcs_get_operation_count(void)
{
    return operation_count;
}

void funcs_reset_operation_count(void)
{
    operation_count = 0;
}
```

```c
// main.c

#include <stdio.h>
#include "funcs.h"

int main(void)
{
    printf("5 squared is %d\n", square_int(5));
    printf("3 cubed is %d\n", cube_int(3));
    printf("Operations so far: %d\n", funcs_get_operation_count());

    funcs_reset_operation_count();
    printf("After reset: %d\n", funcs_get_operation_count());

    return 0;
}
```

Compile both source files together:

```bash
gcc -Wall -Wextra -Wpedantic -std=c11 main.c funcs.c -o static_demo.out
```

This prints:

```text
5 squared is 25
3 cubed is 27
Operations so far: 2
After reset: 0
```

## The two meanings of `static`

What `static` means depends on where it is written:

| Where | Example | Meaning |
|---|---|---|
| inside a function | `static int count = 0;` | keep the value of this variable between function calls |
| outside a function, on a function | `static int double_value(int value)` | keep this function private to the current source file |
| outside a function, on a variable | `static int operation_count = 0;` | keep this variable private to the current source file |

So there are really two meanings: inside a function, `static` means "remember"; outside a function, it means "private to this file".

When a helper function or a variable is only used in one `.c` file, declare it as `static` and do not put it in the header file.

The main points to remember are:

- a static function can only be called from the `.c` file it is defined in
- a static variable outside a function can be used by every function in its file, but by no other file
- other files use public functions declared in the header, such as `funcs_get_operation_count()`, instead
- private helpers and private variables do not appear in the header file
- inside a function, `static` means something different: the variable keeps its value between calls
