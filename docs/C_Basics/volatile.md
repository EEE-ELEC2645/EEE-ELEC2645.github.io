---
title: Volatile & Static
parent: C Basics
nav_order: 13
layout: default
---

# `volatile` and `static`

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

The keywords `volatile` and `static` change how variables behave.

They do different jobs:

- `volatile` is used when a variable might change unexpectedly
- a static local variable keeps its value between function calls

The word `static` has some other meanings in C, depending on where it is used. This is a bit annoying! This page only covers static local variables. We will introduce the other uses later.

## The `volatile` keyword

The compiler normally assumes that a variable only changes when the program changes it.

For example:

```c
int ready = 0;

while (ready == 0) {
    // Wait
}
```

If the compiler cannot see anything that changes `ready`, it may try to optimise the repeated checks.

In an embedded system, a variable might also be changed by:

- an interrupt
- direct memory access, or DMA
- another part of the system that the compiler cannot see

We can tell the compiler about this using `volatile`:

```c
volatile int ready = 0;

while (ready == 0) {
    // Wait for an interrupt to change ready
}
```

This tells the compiler to read the value each time the program asks for it, rather than assuming that it has not changed.

### A flag changed by an interrupt

A common use of `volatile`, which we will see in Units 3 and 4, is a flag changed by an interrupt:

```c
#include <stdbool.h>

volatile bool timer_elapsed = false;

void timer_interrupt_callback(void)
{
    timer_elapsed = true;
}

int main(void)
{
    while (1) {
        if (timer_elapsed) {
            timer_elapsed = false;

            // Respond to the timer
        }
    }

    return 0;
}
```

The main loop does not know exactly when the timer interrupt will occur. The value of `timer_elapsed` can therefore change unexpectedly.

Declaring it as `volatile` makes sure that the main loop reads the current value each time.

The real callback used on the STM32 will depend on the timer and interrupt being used. We will cover that later.

`volatile` only makes sure that the variable is read and written when requested. It does not make more complicated operations automatically safe. We will return to this when we cover interrupts.

## The `static` keyword

This page covers a static variable declared inside a function.

A normal local variable is created and initialised each time the function is called:

```c
#include <stdio.h>

void print_count(void)
{
    int count = 0;

    count++;

    printf("%d\n", count);
}
```

Every call starts with `count` set to zero, so this function always prints:

```text
1
```

Adding `static` makes the variable keep its value between calls:

```c
#include <stdio.h>

void print_count(void)
{
    static int count = 0;

    count++;

    printf("%d\n", count);
}

int main(void)
{
    print_count();
    print_count();
    print_count();

    return 0;
}
```

This prints:

```text
1
2
3
```

The variable `count` can only be accessed inside `print_count()`, but it keeps its value for the whole time the program is running.

This use of `static` means:

> Keep this local variable between function calls.

### Remembering a previous value

A static local variable can be useful when a function needs to remember something from its previous call:

```c
#include <stdbool.h>

bool button_pressed_once(bool button_is_down)
{
    static bool button_was_down = false;

    bool new_press =
        button_is_down && !button_was_down;

    button_was_down = button_is_down;

    return new_press;
}
```

The variable `button_was_down` remembers whether the button was pressed during the previous call.

If it were a normal local variable, it would be reset to `false` each time and the function would forget the previous state.

We will look at this example in more detail when we cover buttons. For now, the important point is that a static local variable keeps its value between calls.

Static local variables can make functions harder to test because their behaviour depends on previous calls. Use one when the function genuinely needs to remember a value.

## Quick reference

When you see:

```c
volatile bool ready;
```

think:

> This value might change unexpectedly.

When you see `static` inside a function:

```c
void example(void)
{
    static int count = 0;
}
```

think:

> This variable keeps its value between function calls.

You will see `static` used in other places later in the module, where it has a different meaning.