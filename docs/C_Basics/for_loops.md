---
title: For Loops
parent: C Basics
nav_order: 5
layout: default
---

# `for` loops

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


A `for` loop repeats a block of code while a condition is true. We normally use a `for` loop when we know roughly how many times the loop needs to run, or when working through an array.

For example, this loop prints the numbers from 0 to 4:

```c
for (int i = 0; i < 5; i++) {
    printf("%d\n", i);
}
```

## Structure of a `for` loop

A `for` loop contains three expressions:

```c
for (initialisation; condition; update) {
    // Code to repeat
}
```

In this example:

```c
for (int i = 0; i < 5; i++) {
    printf("%d\n", i);
}
```

the three expressions are:

```c
int i = 0    // Initialisation
i < 5        // Condition
i++          // Update
```

The loop then runs in this order:

1. `int i = 0` runs once, before the loop starts.
2. `i < 5` is checked before each iteration.
3. The loop body runs if the condition is true.
4. `i++` runs after the loop body.
5. The condition is checked again.

Once `i` reaches `5`, the condition `i < 5` is false and the loop stops.

## Counting upwards

This program prints the numbers from 1 to 5:

```c
#include <stdio.h>

int main(void)
{
    for (int count = 1; count <= 5; count++) {
        printf("%d\n", count);
    }

    return 0;
}
```

Here the condition uses `<=` because we want the loop to include `5`.

Compare this with:

```c
for (int count = 1; count < 5; count++) {
    printf("%d\n", count);
}
```

This version prints only `1`, `2`, `3` and `4`.

## Counting downwards

The update does not have to increase the counter. We can use `--` to count down:

```c
#include <stdio.h>

int main(void)
{
    for (int count = 5; count > 0; count--) {
        printf("%d\n", count);
    }

    printf("Go!\n");

    return 0;
}
```

The counter starts at `5` and decreases after each iteration. Once it reaches `0`, the condition is false and the loop stops.

## Changing the step size

We can also increase or decrease the counter by more than one each time:

```c
for (int value = 0; value <= 10; value += 2) {
    printf("%d\n", value);
}
```

This prints:

```text
0
2
4
6
8
10
```

Here, `value += 2` increases `value` by two after each iteration.

## Processing an array

`for` loops are commonly used to process arrays:

```c
#include <stdio.h>

int main(void)
{
    int values[] = { 10, 20, 30, 40, 50 };
    size_t length = sizeof(values) / sizeof(values[0]);

    for (size_t i = 0; i < length; i++) {
        printf("values[%zu] = %d\n", i, values[i]);
    }

    return 0;
}
```

Array indices start at zero, so the loop also begins at zero:

```c
size_t i = 0;
```

The condition is:

```c
i < length;
```

It is not:

```c
i <= length;
```

For an array with five elements, the valid indices are `0` to `4`. If the loop reaches index `5`, it accesses memory beyond the end of the array.

## Calculating a total

We can use a `for` loop to add all the values in an array:

```c
#include <stdio.h>

int main(void)
{
    int values[] = { 1, 2, 3, 4, 5 };
    size_t length = sizeof(values) / sizeof(values[0]);
    int total = 0;

    for (size_t i = 0; i < length; i++) {
        total += values[i];
    }

    printf("Total = %d\n", total);

    return 0;
}
```

The variable `total` must be initialised to zero before the loop. Each iteration adds one array element to `total`.

After the loop, `total` contains the sum of all five elements.

## Scope of the loop variable

When the counter is declared inside the `for` statement, it only exists within the loop:

```c
for (int i = 0; i < 5; i++) {
    printf("%d\n", i);
}

printf("%d\n", i);    // Error: i is no longer in scope
```

This is normally what we want, as `i` is only being used to control the loop.

If the value is needed after the loop, declare it beforehand:

```c
int i;

for (i = 0; i < 5; i++) {
    printf("%d\n", i);
}

printf("Final value: %d\n", i);
```

After this loop, `i` is `5`.

## `break` and `continue`

As with a `while` loop, `break` stops the loop immediately:

```c
for (int i = 1; i <= 10; i++) {
    if (i == 8) {
        break;
    }

    printf("%d\n", i);
}
```

This prints the numbers from `1` to `7`. When `i` reaches `8`, the `break` statement stops the loop.

The `continue` statement skips the rest of the current iteration:

```c
for (int i = 1; i <= 10; i++) {
    if (i == 5) {
        continue;
    }

    printf("%d\n", i);
}
```

This prints the numbers from `1` to `10`, except for `5`.

In a `for` loop, `continue` does not skip the update. In this example, `i++` still runs before the condition is checked again.

## Common mistakes

### Off-by-one errors

An **off-by-one error** occurs when a loop runs once too many or once too few.

For an array of length `5`, this is correct:

```c
for (int i = 0; i < 5; i++) {
    printf("%d\n", values[i]);
}
```

This is incorrect:

```c
for (int i = 0; i <= 5; i++) {
    printf("%d\n", values[i]);
}
```

The second loop tries to access `values[5]`, but the final valid element is `values[4]`.

If you are like me, you will make this mistake more often than you would like!

### Using the wrong initial value

This loop starts at index `1`:

```c
for (int i = 1; i < length; i++) {
    printf("%d\n", values[i]);
}
```

It therefore skips the first element, `values[0]`.

When processing an entire array, the counter will normally start at zero.

### Using the wrong update

This loop never finishes:

```c
for (int i = 0; i < 5; i--) {
    printf("%d\n", i);
}
```

The condition requires `i` to reach `5`, but `i--` moves it in the opposite direction.

Check that the update is actually moving the counter towards the point where the loop will stop.

### Changing the counter inside the loop

Avoid changing the counter again inside the loop body:

```c
for (int i = 0; i < 10; i++) {
    i += 2;
    printf("%d\n", i);
}
```

The counter is changed by both `i += 2` and the `i++` in the `for` statement. This makes the sequence harder to follow and can cause values to be skipped.

If you want the counter to change by a different amount, put that in the `for` statement:

```c
for (int i = 0; i < 10; i += 3) {
    printf("%d\n", i);
}
```

## Choosing between `for` and `while`

Use a `for` loop when you can describe the starting value, stopping condition and update in one line:

```c
for (int i = 0; i < 10; i++) {
    // ...
}
```

Use a `while` loop when the number of iterations depends on something that happens while the program is running:

```c
while (button_pressed) {
    // ...
}
```

In many cases, either loop would work. Choose the one that makes it clearest when and why the loop will stop.