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

## A private helper function

Suppose a source file contains a short helper function:

```c
#include <stdio.h>

static int double_value(int value)
{
    return value * 2;
}

int calculate_result(int value)
{
    return double_value(value) + 10;
}

int main(void)
{
    int result = calculate_result(5);

    printf("Result: %d\n", result);

    return 0;
}
```

This prints:

```text
Result: 20
```

The function `double_value()` is only needed inside this source file:

```c
static int double_value(int value)
```

The function `calculate_result()` can call it because both functions are in the same file.

Code in another `.c` file cannot call `double_value()`.

## Static functions in a multi-file project

Static functions are most useful when a project contains several source files.

For example, a display module might contain:

```text
display.h
display.c
```

The header lists the functions that other files are allowed to call:

```c
// display.h

#ifndef DISPLAY_H
#define DISPLAY_H

void display_init(void);
void display_message(const char message[]);

#endif
```

These are the public functions provided by the module.

The source file can also contain helper functions:

```c
// display.c

#include "display.h"

static void clear_display_buffer(void)
{
    // Clear the internal display buffer
}

void display_init(void)
{
    clear_display_buffer();

    // Initialise the display
}

void display_message(const char message[])
{
    clear_display_buffer();

    // Draw the message
}
```

The function:

```c
static void clear_display_buffer(void)
```

can only be called from inside `display.c`.

The functions:

```c
void display_init(void);
void display_message(const char message[]);
```

are declared in `display.h`, so other source files can call them.

For example:

```c
#include "display.h"

int main(void)
{
    display_init();
    display_message("Hello");

    return 0;
}
```

This file can call `display_init()` and `display_message()`, but it cannot call `clear_display_buffer()`.

## Public and private functions

A useful way to think about a source file is that it has:

- public functions, which other files are allowed to call
- private helper functions, which are only used inside that source file

Public functions are declared in the header:

```c
// calculations.h

int calculate_total(int a, int b);
```

Their definitions appear in the source file:

```c
// calculations.c

#include "calculations.h"

int calculate_total(int a, int b)
{
    return add_values(a, b);
}
```

Private helper functions are declared as `static` and are not placed in the header:

```c
static int add_values(int a, int b)
{
    return a + b;
}
```

The complete source file could therefore be:

```c
// calculations.c

#include "calculations.h"

static int add_values(int a, int b);

int calculate_total(int a, int b)
{
    return add_values(a, b);
}

static int add_values(int a, int b)
{
    return a + b;
}
```

The static helper needs a prototype because its definition appears below the function that calls it:

```c
static int add_values(int a, int b);
```

The prototype also includes `static`, matching the definition.

## Why keep helper functions private?

If a function is only used inside one source file, declaring it as `static` makes that intention clear.

For example:

```c
static bool input_is_valid(int input);
static void clear_buffer(void);
static int calculate_checksum(const char data[]);
```

These names describe internal jobs performed by the module. Other files do not need to know how the jobs are carried out.

This also prevents another source file from accidentally calling an internal function that may later change.

## Avoiding name clashes

Two different source files can each contain a static function with the same name.

For example:

```c
// display.c

static void initialise_hardware(void)
{
    // Initialise display hardware
}
```

and:

```c
// joystick.c

static void initialise_hardware(void)
{
    // Initialise joystick hardware
}
```

This is allowed because each static function is only visible inside its own source file.

Without `static`, both files would define a public function called `initialise_hardware()`, causing a linker error.

## Static local variables and static functions

Remember that these two uses of `static` are different.

Inside a function:

```c
void count_calls(void)
{
    static int count = 0;
}
```

This means:

> Keep the value of `count` between function calls.

Before a function:

```c
static void helper_function(void)
{
}
```

This means:

> Keep `helper_function()` private to this source file.

The location of the keyword tells us which meaning applies.

## When to use a static function

Use `static` when a function:

- is only called from one `.c` file
- is an internal helper for a larger public function
- should not form part of the module’s public interface

For example:

```c
static bool input_is_valid(int input)
{
    return input >= 0 && input <= 100;
}
```

If other source files genuinely need to call the function, remove `static` and declare the function in the appropriate header file.