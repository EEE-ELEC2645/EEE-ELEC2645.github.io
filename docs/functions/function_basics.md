---
title: Function basics
parent: Functions
nav_order: 1
layout: default
---

# Function basics

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

A function is a named block of code that performs a particular job.

For example:

```c
void express_love_for_c(void)
{
    printf("I love C because it's fun! :D :D :D\n");
}
```

This is a **function definition**. It contains the code that runs when the function is called.

We call the function using its name followed by parentheses:

```c
express_love_for_c();
```

The parentheses are required, even when the function does not take any inputs.

## The caller

The **caller** is the function that makes the call.

For example:

```c
int main(void)
{
    express_love_for_c();

    return 0;
}
```

Here, `main()` is the caller and `express_love_for_c()` is the function being called.

When `express_love_for_c()` finishes, the program returns to `main()` and continues from the next line. In most of our early examples, `main()` will be the caller. However, any function can call another function.

Later, when we talk about changing a value in the caller, we mean changing a value belonging to the function that made the call. Most of the time, this will be a variable inside `main()`.

## Parameters

A function can receive values from its caller. These inputs are called **parameters**:

```c
void print_number(int number)
{
    printf("The number is %d\n", number);
}
```

The parameter in this example is: `int number` and we provide a value when calling the function `print_number(42);`. Then the value `42` is copied into `number` for the function to use.

A function can have more than one parameter:

```c
void print_position(int x, int y)
{
    printf("Position: (%d, %d)\n", x, y);
}
```

The values must be provided in the same order:

```c
print_position(10, 20);
```

Here, `x` receives `10` and `y` receives `20`.

## Returning a value

A function can return a value to its caller:

```c
int add(int a, int b)
{
    return a + b;
}
```

The `int` before the function name means that the function returns an integer.

The caller can store the returned value:

```c
int total = add(10, 20);
```

After this line, `total` contains `30`.

The `return` statement supplies the result and ends the function:

```c
return a + b;
```

## The `void` keyword

A function that does not return a value uses `void` before its name:

```c
void print_message(void)
{
    printf("Hello!\n");
}
```

A function that takes no parameters uses `void` inside the parentheses.

In this definition:

```c
void print_message(void)
```

the first `void` means "The function does not return a value", the second `void` means "The function does not take any parameters"

I admit that this looks slightly odd at first! But you will see it frequently in C, and you will get compiler warnings and errors if you don't!

## Local variables

Variables declared inside a function are local to that function:

```c
int add(int a, int b)
{
    int total = a + b;

    return total;
}
```

The variable `total` can only be used inside `add()`.

Parameters are also local variables. In this example, `a` and `b` only exist inside `add()`.

## Complete example

This program uses functions with no parameters, several parameters and a return value:

```c
#include <stdbool.h>
#include <stdio.h>

// Function prototypes
void print_title(void);
int add(int a, int b);
bool is_even(int value);

int main(void)
{
    print_title();

    int total = add(10, 20);

    printf("Total: %d\n", total);

    if (is_even(total)) {
        printf("The total is even\n");
    } else {
        printf("The total is odd\n");
    }

    return 0;
}

void print_title(void)
{
    printf("Function example\n");
}

int add(int a, int b)
{
    return a + b;
}

bool is_even(int value)
{
    return value % 2 == 0;
}
```

In this program, `main()` is the caller.

It calls three functions:

```c
print_title();
add(10, 20);
is_even(total);
```

The functions have different jobs:

- `print_title()` takes no parameters and returns no value
- `add()` takes two integer parameters and returns an integer
- `is_even()` takes one integer parameter and returns a Boolean

The lines near the top are **function prototypes**:

```c
void print_title(void);
int add(int a, int b);
bool is_even(int value);
```

They tell the compiler about the functions before `main()` calls them. Prototypes are covered in more detail on the next page.