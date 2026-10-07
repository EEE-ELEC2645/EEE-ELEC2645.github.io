---
title: Function Prototypes
parent: Functions
nav_order: 2
layout: default
---

# Function prototypes

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

A function prototype tells the compiler about a function before the function is called.

For example:

```c
int add(int a, int b);
```

This tells the compiler that `add()`:

- takes two `int` parameters
- returns an `int`

The prototype ends with a semicolon because it does not contain the function body.

## The compiler needs to see the function first

Consider this program:

```c
#include <stdio.h>

int main(void)
{
    int total = add(10, 20);

    printf("Total: %d\n", total);

    return 0;
}

int add(int a, int b)
{
    return a + b;
}
```

The function definition for `add()` appears below `main()`.

When the compiler reaches:

```c
int total = add(10, 20);
```

it has not seen `add()` yet, so it does not know:

- whether the function exists
- which parameters it takes
- which type it returns

Modern C compilers will produce an error or warning because the function has been called before it was declared.

## Moving the function above `main()`

One solution is to move the complete function definition above `main()`:

```c
#include <stdio.h>

int add(int a, int b)
{
    return a + b;
}

int main(void)
{
    int total = add(10, 20);

    printf("Total: %d\n", total);

    return 0;
}
```

This now compiles because the compiler sees the definition of `add()` before the function is called.

For a small program with one function, this approach works fine.

## The problem with several functions

Moving every function above its caller becomes awkward as a program grows.

For example, suppose `print_total()` calls `add()`:

```c
#include <stdio.h>

void print_total(int a, int b)
{
    int total = add(a, b);

    printf("Total: %d\n", total);
}

int add(int a, int b)
{
    return a + b;
}

int main(void)
{
    print_total(10, 20);

    return 0;
}
```

This does not compile correctly because `print_total()` calls `add()` before the compiler has seen it.

We could move `add()` above `print_total()`:

```c
int add(int a, int b)
{
    return a + b;
}

void print_total(int a, int b)
{
    int total = add(a, b);

    printf("Total: %d\n", total);
}
```

However, functions may call several other functions. Constantly rearranging their definitions to keep the compiler happy quickly becomes inconvenient.

It can also make the file harder to read. We may want `main()` near the top so that we can quickly see the overall order of the program.

## Solving the problem with prototypes

Instead of moving the complete definitions, we can place function prototypes near the top of the file:

```c
#include <stdio.h>

// Function prototypes
int add(int a, int b);
void print_total(int a, int b);

int main(void)
{
    print_total(10, 20);

    return 0;
}

void print_total(int a, int b)
{
    int total = add(a, b);

    printf("Total: %d\n", total);
}

int add(int a, int b)
{
    return a + b;
}
```

The compiler sees both prototypes before it reaches `main()`:

```c
int add(int a, int b);
void print_total(int a, int b);
```

It therefore knows how both functions should be called, even though their definitions appear later in the file.

This allows us to keep:

- the prototypes near the top
- `main()` near the start of the program
- the complete function definitions underneath

## Prototype, call and definition

These three pieces look similar but do different jobs.

The prototype tells the compiler about the function:

```c
int add(int a, int b);
```

The call runs the function:

```c
int total = add(10, 20);
```

The definition contains the code that the function runs:

```c
int add(int a, int b)
{
    return a + b;
}
```

The prototype ends with a semicolon:

```c
int add(int a, int b);
```

The definition contains braces and does not need a semicolon after the closing brace:

```c
int add(int a, int b)
{
    return a + b;
}
```

## The prototype must match the definition

The return type and parameter types in the prototype must match the function definition.

This prototype:

```c
float calculate_average(float a, float b);
```

matches this definition:

```c
float calculate_average(float a, float b)
{
    return (a + b) / 2.0f;
}
```

This prototype would not match:

```c
int calculate_average(int a, int b);
```

The return type and parameter types are different.

Compiler errors about conflicting function types often mean that the prototype and definition do not match.

## Functions with no parameters

A prototype for a function with no parameters should use `void` inside the parentheses:

```c
void print_title(void);
```

The matching definition is:

```c
void print_title(void)
{
    printf("Function example\n");
}
```

For the code in this module, use:

```c
void print_title(void);
```

rather than:

```c
void print_title();
```

Using `void` makes it clear that the function does not take any parameters.

## Parameter names in prototypes

A prototype can include parameter names:

```c
int add(int a, int b);
```

The names can also be omitted:

```c
int add(int, int);
```

Both prototypes mean the same thing to the compiler.

I recommend including the names because they help explain what each parameter represents:

```c
void print_position(int x, int y);
```

The parameter names in the prototype and definition do not have to match, but using consistent names normally makes the code easier to follow.

## Prototypes in header files

In larger projects, prototypes are normally placed in header files.

For example, `math_utils.h` might contain:

```c
#ifndef MATH_UTILS_H
#define MATH_UTILS_H

int add(int a, int b);
int multiply(int a, int b);

#endif
```

Any source file that needs these functions can include the header:

```c
#include "math_utils.h"
```

The function definitions remain in a `.c` file:

```c
#include "math_utils.h"

int add(int a, int b)
{
    return a + b;
}

int multiply(int a, int b)
{
    return a * b;
}
```

The source file containing the definitions should include its own header. This allows the compiler to check that the prototypes and definitions match.

Header files, header guards and projects containing several `.c` files are covered in more detail later.