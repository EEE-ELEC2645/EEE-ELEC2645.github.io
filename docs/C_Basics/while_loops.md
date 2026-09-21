---
title: While loops
parent: C Basics
nav_order: 4
layout: default
---

# `while` and `do...while` loops

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

A loop repeats a block of code while a condition is true. A `while` loop is useful when you do not know in advance exactly how many times the code needs to run.

For example, a program might continue until:

- the user enters a particular value
- a sensor reaches a threshold
- an operation succeeds
- an error occurs

## The `while` loop

The condition is checked before each iteration:

```c
while (condition) {
    // Code to repeat
}
```

If the condition is initially false, the loop body does not run at all.

This example prints the numbers from 1 to 5:

```c
#include <stdio.h>

int main(void)
{
    int count = 1;

    while (count <= 5) {
        printf("%d\n", count);
        count++;
    }

    return 0;
}
```

The loop follows these steps:

1. Check whether `count <= 5`.
2. Run the loop body if the condition is true.
3. Increase `count`.
4. Check the condition again.

Once `count` becomes 6, the condition is false and the program continues with the first line after the loop.

## Changing the loop condition

Something inside the loop will normally need to change the condition. Otherwise, the loop may run forever:

```c
int count = 1;

while (count <= 5) {
    printf("%d\n", count);

    // count never changes
}
```

This is an **infinite loop** because `count <= 5` remains true.

Everyone creates an infinite loop at some point! If your program appears to be stuck, check whether the variables used in the loop condition are actually changing.

## Using a sentinel value

Sometimes a particular value is used to indicate that a loop should stop. This is known as a **sentinel value**.

In this example, the program continues reading numbers until the user enters `0`:

```c
#include <stdio.h>

int main(void)
{
    int number = 0;
    int sum = 0;

    printf("Enter a number, or 0 to stop: ");

    if (scanf("%d", &number) != 1) {
        return 1;
    }

    while (number != 0) {
        sum += number;

        printf("Enter another number, or 0 to stop: ");

        if (scanf("%d", &number) != 1) {
            return 1;
        }
    }

    printf("Sum = %d\n", sum);

    return 0;
}
```

Here, `0` is the sentinel value. It is not added to the total because the loop stops as soon as `number != 0` becomes false.

## The `do...while` loop

A `do...while` loop checks its condition after running the loop body:

```c
do {
    // Code to repeat
} while (condition);
```

This means the body always runs at least once.

Notice the semicolon after the condition:

```c
while (condition);
```

It is easy to miss!

For example, we can continue asking for a positive number until the user enters one:

```c
#include <stdio.h>

int main(void)
{
    int number;

    do {
        printf("Enter a positive number: ");

        if (scanf("%d", &number) != 1) {
            return 1;
        }
    } while (number <= 0);

    printf("You entered: %d\n", number);

    return 0;
}
```

The program needs to ask for a number before it has anything to check. A `do...while` loop is convenient here because the input code must run at least once.

We could write the same program using while, but we would need to read the first value before entering the loop or give number a suitable initial value.

## `break`

The `break` statement immediately stops a loop. The program then continues from the first line after the loop.

```c
#include <stdio.h>

int main(void)
{
    int number;

    while (1) {
        printf("Enter a number, or 0 to stop: ");

        if (scanf("%d", &number) != 1) {
            return 1;
        }

        if (number == 0) {
            break;
        }

        printf("You entered %d\n", number);
    }

    printf("Loop ended\n");

    return 0;
}
```

The condition `1` is always true, so this would normally be an infinite loop. The `break` statement provides another way out when the user enters `0`.

If you use `while (1)`, make sure there is a reachable break or another way to leave the loop!.

## `continue`

The `continue` statement skips the rest of the current iteration and moves back to the loop condition.

This example prints only the odd numbers from 1 to 5:

```c
#include <stdio.h>

int main(void)
{
    int number = 0;

    while (number < 5) {
        number++;

        if (number % 2 == 0) {
            continue;
        }

        printf("%d\n", number);
    }

    return 0;
}
```

Here, `number` is incremented before the if statement, so the loop continues to make progress.

The following version contains a problem:

```c
int number = 0;

while (number < 5) {
    if (number % 2 == 0) {
        continue;
    }

    number++;
}
```

`number` begins at zero, so the condition for `continue` is true during the first iteration. The program skips `number++`, returns to the start of the loop, and repeats forever.

This is one reason to be careful when using `continue` inside a `while` loop.

## Choosing between the two loops

Use a `while` loop when the body might not need to run:

```c
while (temperature < target_temperature) {
    // Continue heating
}
```

Use a `do...while` loop when the body must run at least once:

```c
do {
    // Read an input
} while (input_is_invalid);
```

In practice, `while` loops are more common. Use `do...while` when the code needs to run once before the condition can sensibly be checked.