---
title: Booleans
parent: C Basics
nav_order: 11
layout: default
---

# Booleans in C

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

A Boolean value represents one of two states:

```c
true
false
```

C provides the `bool` type through the `<stdbool.h>` header:

```c
#include <stdbool.h>

bool is_ready = true;
bool has_error = false;
```

Remember to include `<stdbool.h>` in any file that uses `bool`, `true` or `false`.

Booleans are useful for values that represent a simple yes-or-no state:

```c
bool button_pressed = false;
bool data_ready = true;
bool game_over = false;
```

A good Boolean variable name normally reads like a question:

```text
Is the button pressed?
Is the data ready?
Is the game over?
```

## Using a Boolean in a condition

A Boolean can be used directly as the condition of an `if` statement:

```c
#include <stdbool.h>
#include <stdio.h>

int main(void)
{
    bool data_ready = true;

    if (data_ready) {
        printf("Data is ready\n");
    }

    return 0;
}
```

There is no need to write:

```c
if (data_ready == true) {
    // ...
}
```

The shorter version already means the same thing:

```c
if (data_ready) {
    // ...
}
```

To check whether a Boolean is false, use the logical NOT operator `!`:

```c
if (!data_ready) {
    printf("Waiting for data\n");
}
```

This reads as “if not data ready”.

## How Boolean conditions work in C

C treats zero as false and any non-zero value as true:

```c
int value_1 = 0;
int value_2 = 5;

if (value_1) {
    // Does not run because value_1 is zero
}

if (value_2) {
    // Runs because value_2 is non-zero
}
```

The `bool` type makes it clearer that a variable is intended to represent only true or false:

```c
bool has_error = false;
```

rather than:

```c
int has_error = 0;
```

Both can be used in a condition, but the Boolean declaration better describes what the variable represents.

## Assigning values to a `bool`

Assigning zero to a `bool` produces `false`:

```c
bool status = 0;
```

Assigning any non-zero value produces `true`:

```c
bool status = 1;
bool another_status = 42;
```

Even though `42` is assigned, the Boolean value becomes `true`.

Normally, it is clearer to write:

```c
bool status = true;
```

or:

```c
bool status = false;
```

Use numeric values when they come from an existing function or hardware interface.

## Combining Boolean values

Boolean values can be combined using logical operators:

| Operator | Meaning |
|:---------|:--------|
| `&&` | AND |
| `||` | OR |
| `!` | NOT |

For example:

```c
#include <stdbool.h>
#include <stdio.h>

int main(void)
{
    bool sensor_connected = true;
    bool data_ready = false;

    if (sensor_connected && data_ready) {
        printf("Reading sensor data\n");
    }

    if (!sensor_connected) {
        printf("Check the sensor connection\n");
    }

    if (sensor_connected || data_ready) {
        printf("At least one condition is true\n");
    }

    return 0;
}
```

For `&&`, both conditions must be true.

For `||`, at least one condition must be true.

The logical operators and short-circuit evaluation are covered in more detail on the Operators page.

## Setting a Boolean from a comparison

A comparison already produces a true-or-false result, so it can be assigned directly to a Boolean:

```c
int temperature = 35;

bool too_hot = temperature > 30;
```

There is no need to write:

```c
bool too_hot;

if (temperature > 30) {
    too_hot = true;
} else {
    too_hot = false;
}
```

The direct version is shorter and expresses the same logic:

```c
bool too_hot = temperature > 30;
```

This also works with more complicated conditions:

```c
bool temperature_ok =
    temperature >= 20 && temperature <= 30;
```

## Toggling a Boolean

The logical NOT operator can be used to toggle a Boolean:

```c
bool led_enabled = false;

led_enabled = !led_enabled;
```

After this line, `led_enabled` is `true`.

Running the same line again changes it back to `false`:

```c
led_enabled = !led_enabled;
```

This can be useful for toggling a mode or state whenever an event occurs:

```c
if (button_pressed) {
    led_enabled = !led_enabled;
}
```

In a real button-handling program, we would also need to make sure that one button press does not cause the value to toggle repeatedly. We will cover this when looking at buttons and state machines.

## Printing a Boolean

There is no dedicated `printf` format for `bool`. We can print it as an integer using `%d`:

```c
#include <stdbool.h>
#include <stdio.h>

int main(void)
{
    bool is_ready = true;
    bool has_error = false;

    printf("is_ready: %d\n", is_ready);
    printf("has_error: %d\n", has_error);

    return 0;
}
```

This prints:

```text
is_ready: 1
has_error: 0
```

For output intended for a person, printing words is normally clearer:

```c
if (is_ready) {
    printf("Ready: true\n");
} else {
    printf("Ready: false\n");
}
```

We can also use the conditional operator:

```c
printf("Ready: %s\n", is_ready ? "true" : "false");
```

The conditional operator is covered separately later. Do not worry if that final example looks unfamiliar for now.

## A simple status check

Here is a complete example using two Boolean values:

```c
#include <stdbool.h>
#include <stdio.h>

int main(void)
{
    bool sensor_connected = true;
    bool data_ready = false;

    if (!sensor_connected) {
        printf("Sensor not connected\n");
        return 1;
    }

    if (data_ready) {
        printf("Reading sensor data\n");
    } else {
        printf("Waiting for sensor data\n");
    }

    return 0;
}
```

Using `bool` makes it clear that `sensor_connected` and `data_ready` represent two possible states rather than general numeric values.