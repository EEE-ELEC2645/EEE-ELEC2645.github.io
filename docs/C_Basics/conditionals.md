---
title: Conditionals
parent: C Basics
nav_order: 3
layout: default
---

# Conditionals in C

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Conditionals allow different parts of a program to run depending on whether a condition is true or false.

For example, we might turn on a warning if a temperature is too high:

```c
if (temperature > 50) {
    printf("Warning: high temperature\n");
}
```

The expression inside the parentheses `()` is evaluated first. If it is true, the code inside the braces `{}` runs.

## `if`

The simplest conditional uses an `if` statement:

```c
if (condition) {
    // Run this code if the condition is true
}
```

For example:

```c
#include <stdio.h>

int main(void)
{
    int score = 85;

    if (score >= 50) {
        printf("Pass\n");
    }

    return 0;
}
```

If `score` is greater than or equal to `50`, the message is printed. Otherwise, the program skips the contents of the `if` statement.

Although C allows the braces to be omitted when there is only one statement, I recommend always including them:

```c
if (score >= 50) {
    printf("Pass\n");
}
```

This is clearer and avoids problems when more lines are added later.

## `if` and `else`

Use `else` when you want one block of code to run if the condition is true and another block to run if it is false:

```c
#include <stdio.h>

int main(void)
{
    int score = 45;

    if (score >= 50) {
        printf("Pass\n");
    } else {
        printf("Fail\n");
    }

    return 0;
}
```

Only one of the two blocks will run.

## `else if`

Use `else if` when there are several possible conditions:

```c
#include <stdio.h>

int main(void)
{
    int grade = 72;

    if (grade >= 70) {
        printf("First\n");
    } else if (grade >= 60) {
        printf("2:1\n");
    } else if (grade >= 50) {
        printf("2:2\n");
    } else if (grade >= 40) {
        printf("Third\n");
    } else {
        printf("Fail\n");
    }

    return 0;
}
```

The conditions are checked from top to bottom. As soon as one condition is true, its block runs and the remaining conditions are skipped.

The order therefore matters. This version would not work:

```c
if (grade >= 40) {
    printf("Pass\n");
} else if (grade >= 70) {
    printf("First\n");
}
```

A grade of `75` satisfies the first condition, so the second condition is never reached.

## Conditions

Conditions are usually created using relational and logical operators:

```c
if (temperature > 30) {
    // ...
}

if (button_pressed == true) {
    // ...
}

if (temperature > 20 && button_pressed) {
    // ...
}

if (mode == MODE_ERROR || timeout) {
    // ...
}
```

You can also use a Boolean variable directly:

```c
bool button_pressed = true;

if (button_pressed) {
    printf("Button pressed\n");
}
```

To check that a Boolean value is false, use the logical NOT operator `!`:

```c
if (!button_pressed) {
    printf("Button not pressed\n");
}
```

## Assignment and comparison

Remember that `=` assigns a value, while `==` compares two values:

```c
x = 5;      // Assign 5 to x
x == 5;     // Check whether x is equal to 5
```

This is a very common mistake:

```c
if (x = 5) {
    // ...
}
```

This assigns `5` to `x`. Since `5` is non-zero, C treats the condition as true.

The comparison should be:

```c
if (x == 5) {
    // ...
}
```

Compiler warnings can help catch this, so do not ignore them!

## Keeping conditions readable

A condition can contain several comparisons:

```c
if (temperature >= 20 && temperature <= 30 && system_enabled) {
    // ...
}
```

However, conditions can quickly become difficult to understand. It may be clearer to calculate and name part of the condition first:

```c
bool temperature_ok = temperature >= 20 && temperature <= 30;

if (temperature_ok && system_enabled) {
    // ...
}
```

For more complicated checks, you can move the logic into a function:

```c
if (input_is_valid(input)) {
    // ...
}
```

The name should explain what the condition means without requiring the reader to work through every comparison.

## Nested `if` statements

An `if` statement can contain another `if` statement:

```c
#include <stdio.h>

int main(void)
{
    int value = 15;

    if (value > 0) {
        if (value % 2 == 0) {
            printf("Positive and even\n");
        } else {
            printf("Positive and odd\n");
        }
    } else {
        printf("Zero or negative\n");
    }

    return 0;
}
```

Nested conditionals are sometimes useful, but several levels of nesting can make code difficult to follow.

Where possible, combine related conditions:

```c
if (value > 0 && value % 2 == 0) {
    printf("Positive and even\n");
}
```

Alternatively, move part of the logic into a separate function.

## `switch`

A `switch` statement selects between several fixed values of one expression.

This can be clearer than a long chain of `else if` statements when checking menu choices, states or enumerations:

```c
switch (expression) {
    case VALUE_1:
        // Run when expression equals VALUE_1
        break;

    case VALUE_2:
        // Run when expression equals VALUE_2
        break;

    default:
        // Run when no case matches
        break;
}
```

For example:

```c
#include <stdio.h>

int main(void)
{
    int choice = 2;

    switch (choice) {
        case 1:
            printf("Starting\n");
            break;

        case 2:
            printf("Stopping\n");
            break;

        case 3:
            printf("Exiting\n");
            break;

        default:
            printf("Unknown choice\n");
            break;
    }

    return 0;
}
```

The expression is evaluated once, then control jumps to the matching `case`.

The `default` case runs if none of the listed values match. It is useful for handling unexpected values.

## Remember `break`

A `case` does not automatically stop at the next case. Without `break`, execution continues into the code below:

```c
int choice = 1;

switch (choice) {
    case 1:
        printf("One\n");

    case 2:
        printf("Two\n");
        break;
}
```

This prints both:

```text
One
Two
```

This behaviour is called **fall-through**.

Occasionally fall-through is intentional, but most of the time it is a mistake. Add `break` unless you deliberately want execution to continue into the next case.

If you do use intentional fall-through, add a comment to make it clear:

```c
switch (choice) {
    case 1:
        printf("Option 1 selected\n");
        // Fall through intentionally

    case 2:
        printf("Running shared code\n");
        break;

    default:
        break;
}
```

## Using `switch` with an `enum`

A `switch` works particularly well with an `enum`. We will use this combination when implementing finite-state machines.

```c
#include <stdio.h>

typedef enum {
    MODE_IDLE,
    MODE_RUNNING,
    MODE_ERROR
} mode_t;

int main(void)
{
    mode_t mode = MODE_RUNNING;

    switch (mode) {
        case MODE_IDLE:
            printf("Idle\n");
            break;

        case MODE_RUNNING:
            printf("Running\n");
            break;

        case MODE_ERROR:
            printf("Error\n");
            break;

        default:
            printf("Unknown mode\n");
            break;
    }

    return 0;
}
```

Using named enumeration values makes the cases much clearer than using unexplained numbers such as `0`, `1` and `2`.

## Declaring variables inside a `case`

A `case` label does not create a new scope by itself.

If you want to declare local variables inside a case, surround the contents of that case with braces:

```c
#include <stdio.h>

int main(void)
{
    int choice = 2;

    switch (choice) {
        case 1: {
            int value = 10;
            printf("Value: %d\n", value);
            break;
        }

        case 2: {
            int value = 20;
            printf("Value: %d\n", value);
            break;
        }

        default:
            printf("Unknown choice\n");
            break;
    }

    return 0;
}
```

The braces create a separate scope for each `value` variable.

This is something that is easy to get wrong (I say from experience!), particularly when adding variables to an existing `switch`.

## Choosing between `if` and `switch`

Use `if`, `else if` and `else` when:

- checking ranges, such as `temperature > 30`
- combining several conditions
- comparing values using `<`, `>`, `<=` or `>=`
- the conditions involve different variables

Use `switch` when:

- comparing one expression against several fixed values
- working with menu choices
- handling enumeration values
- implementing a finite-state machine

For example, use `if` for a range:

```c
if (temperature >= 20 && temperature <= 30) {
    printf("Temperature is within range\n");
}
```

Use `switch` for fixed states:

```c
switch (mode) {
    case MODE_IDLE:
        break;

    case MODE_RUNNING:
        break;

    case MODE_ERROR:
        break;

    default:
        break;
}
```