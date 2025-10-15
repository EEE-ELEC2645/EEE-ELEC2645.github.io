---
title: For Loops
parent: C Basics
nav_order: 5
layout: default
---

# C Loops: `For`

## Introduction

A **for loop** is a way to repeat a block of code a specific number of times. It is commonly used when you know in advance how many times you want to loop.

---

## Syntax

```c
for (initialisation; condition; update) {
  // code to repeat
}
```

- **initialisation**: runs once at the start (e.g., `int i = 0`)
- **condition**: checked before each loop; if true, the loop runs
- **update**: runs after each loop iteration (e.g., `i++`)

---

## Example: Print numbers 0 to 4

```c
#include <stdio.h>

int main(void) {
  for (int i = 0; i < 5; i++) {
    printf("i = %d\n", i);
  }
  return 0;
}
```

---

## Why use `for` loops?

- Easy to write loops that run a set number of times
- Keeps loop control in one place (initialisation, condition, update)
- Useful for iterating over arrays

---

## Example: Sum elements of an array

```c
#include <stdio.h>

int main(void) {
  int nums[] = {1, 2, 3, 4, 5};
  int sum = 0;
  int length = sizeof(nums) / sizeof(nums[0]);

  for (int i = 0; i < length; i++) {
    sum += nums[i];
  }
  printf("Sum = %d\n", sum);
  return 0;
}
```

## `continue` and `break`

As we saw with `while` loops, we can also have more control over how the `for` loop runs using `break` and `continue`:

- `break` immediately stops the loop and moves to the next line after the loop.
- `continue` skips the rest of the current loop iteration and starts the next iteration right away.

```c
#include <stdio.h>

int main(void) {
    for (int i = 1; i <= 10; i++) {
        if (i == 5) continue; // Skip printing 5
        if (i == 8) break;    // Stop the loop completely when i is 8
        printf("%d ", i);
    }
    printf("\n");
    return 0;
}
```

---

## Common mistakes

- Forgetting to update the loop variable (can cause infinite loops)
- Using the wrong condition so loop runs one too many or too few times, known as  [off-by-one errors](https://en.wikipedia.org/wiki/Off-by-one_error) - if you are like then you will make this error *all the time*
- Changing the loop variable inside the loop body (can make code confusing)

---

## Things to remember

- The loop variable is often declared inside the for statement
- The loop runs as long as the condition is true
- You can use `break` to exit early, or `continue` to skip to the next iteration

---

## Summary

- For loops are best when you know how many times to repeat
- Syntax: `for (init; condition; update)`
- Great for arrays and counting
