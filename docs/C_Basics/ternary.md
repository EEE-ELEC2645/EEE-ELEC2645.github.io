---
title: Ternary Operator
parent: C Basics
nav_order: 14
layout: default
---

# The ternary operator

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

The ternary operator provides a short way to choose between two values.

Its syntax is:

```c
condition ? value_if_true : value_if_false
```

For example:

```c
int larger = x > y ? x : y;
```

If `x > y` is true, `larger` is assigned the value of `x`. Otherwise, it is assigned the value of `y`.

The same code written using `if` and `else` would be:

```c
int larger;

if (x > y) {
    larger = x;
} else {
    larger = y;
}
```

The ternary operator is useful when we need to select one of two simple values.

## How it works

The ternary operator contains three parts:

```c
condition ? value_if_true : value_if_false
```

The condition is evaluated first:

- if the condition is true, the expression after `?` is used
- if the condition is false, the expression after `:` is used

Only one of the two expressions is evaluated.

For example:

```c
int score = 75;

char result = score >= 40 ? 'P' : 'F';
```

Since `score >= 40` is true, `result` is assigned `'P'`.

## Choosing one of two numbers

The ternary operator can select between two numbers:

```c
int x = 10;
int y = 20;

int larger = x > y ? x : y;
```

Here, `larger` is assigned `20`.

We can also use it as part of a calculation:

```c
int base_score = 50;
bool bonus_available = true;

int total = base_score + (bonus_available ? 10 : 0);
```

If `bonus_available` is true, 10 is added to the score. Otherwise, zero is added.

## Using the ternary operator with `printf`

A ternary expression can be used directly as an argument to a function:

```c
#include <stdbool.h>
#include <stdio.h>

int main(void)
{
    bool data_ready = true;

    printf("Data ready: %s\n", data_ready ? "yes" : "no" );

    return 0;
}
```

This prints:

```text
Data ready: yes
```

If `data_ready` were false, it would print:

```text
Data ready: no
```

This is often more useful than printing a Boolean as `0` or `1`.

## Choosing between enum values

The two possible results can also be enum values:

```c
#include <stdbool.h>

typedef enum {
    LED_OFF,
    LED_ON
} led_state_t;

bool button_pressed = true;

led_state_t state = button_pressed ? LED_ON : LED_OFF;
```

If `button_pressed` is true, `state` becomes `LED_ON`. Otherwise, it becomes `LED_OFF`.

## Toggling a Boolean

You do not need the ternary operator to toggle a Boolean.

This works:

```c
led_on = led_on ? false : true;
```

However, the logical NOT operator is clearer:

```c
led_on = !led_on;
```

Use the ternary operator to choose between two different values. Use `!` when you simply want to reverse a Boolean value.

## Use parentheses when they help

Parentheses are not required around the condition in this example:

```c
int larger = x > y ? x : y;
```

However, they can make it easier to see the three parts:

```c
int larger = (x > y) ? x : y;
```

Parentheses are particularly helpful when the result is used as part of a larger expression:

```c
int total = base_score + (bonus_available ? 10 : 0);
```

## Avoid nested ternary operators

It is possible to put one ternary operator inside another:

```c
char grade =
    score >= 70 ? 'A' :
    score >= 60 ? 'B' :
    score >= 50 ? 'C' :
    score >= 40 ? 'D' :
                  'F';
```

This is valid C, but god knows if its actually readable!  If you need to stop and work out what a ternary expression does, use an `if` statement instead!

## Ternary operator or `if` statement?

Use the ternary operator when selecting one of two values:

```c
int larger = x > y ? x : y;
```

```c
char result = score >= 40 ? 'P' : 'F';
```

Use `if` and `else` when each branch needs to perform one or more actions:

```c
if (ready) {
    printf("Ready\n");
    start_operation();
} else {
    printf("Waiting\n");
    check_input();
}
```

The ternary operator should make the code easier to read, not merely shorter.