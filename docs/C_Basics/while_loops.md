---
title: While loops
parent: C Basics
nav_order: 4
layout: default
---


# C Loops: `while` and `do...while`

## Introduction

Loops let you repeat actions in your program. They're essential for tasks like counting, reading input, or processing data until a condition is met.

In C, the two main loop types for condition-based repetition are `while` and `do...while`.

---

## Why Loops Matter

Loops help you:

- Process multiple items (e.g., array elements)
- Retry operations until success
- Validate input
- Perform repeated calculations

They reduce repetition and make your code cleaner and more flexible.

---

## `while` loop

### Syntax

```c
while (condition) {
  // code that does cool stuff
}
```

- The **condition is checked first**.
- If the condition is `true`, the body runs; then the condition is checked again.
- If the condition is `false` at the start, the body **never runs**.

### Example 1 — Count from 1 to 5

```c
#include <stdio.h>

int main(void) {
  int i = 1;
  while (i <= 5) {
    printf("%d\n", i);
    i++;
  }
  return 0;
}
```

### Example 2 — Sum numbers until zero

Often a special value is used to signal the end of the loop, known as the *sentinel* value. For example we can read input numbers until the user inputs 0 (the sentinel value):

```c
#include <stdio.h>

int main(void) {
  int n = 0;
  int sum = 0;

  printf("Enter numbers (0 to stop): ");
  if (scanf("%d", &n) != 1) return 1;  // basic input check

  while (n != 0) { // 0 is the sentinel value to indicate "stop looping"
    sum += n;
    if (scanf("%d", &n) != 1) return 1;
  }

  printf("Sum = %d\n", sum);
  return 0;
}
```

---

## `do...while` loop

A variation of a `while` loop is the `do ... while` loop, where the condition check happens *after* the body is run.

### Syntax

```c
do {
  // statements
} while (condition);
```

- The **body runs first**, then the condition is checked.
- The loop **always runs at least once**, even if the condition is false initially.

We can use this to ensure that code runs at least once, in a more convenient way than using just `while` loops. For example: 

### Example 1 — Ask for a positive number (input validation)

```c
#include <stdio.h>

int main(void) {
  int n;
  do {
    printf("Enter a positive number: ");
    if (scanf("%d", &n) != 1) return 1;
  } while (n <= 0);

  printf("You entered: %d\n", n);
  return 0;
}
```

### Example 2 — Simple menu-driven program

```c
#include <stdio.h>

int main(void) {
  int choice;
  do {
    printf("\nMenu:\n");
    printf("1. Say Hello\n");
    printf("2. Say Goodbye\n");
    printf("0. Exit\n");
    printf("Enter choice: ");

    if (scanf("%d", &choice) != 1) return 1;

    if (choice == 1) {
      printf("Hello!\n");
    } else if (choice == 2) {
      printf("Goodbye!\n");
    } else if (choice != 0) {
      printf("Invalid choice.\n");
    }
  } while (choice != 0);

  printf("Program ended.\n");
  return 0;
}
```

## `continue` and `break`

We can also have more control over how the loop runs using `break` and `continue`:

- `break` immediately stops the loop and moves to the next line after the loop.
- `continue` skips the rest of the current loop iteration and starts the next iteration right away.

With while loops be careful that you don't accidentally create an infinite loop when using `continue`, what would happen if we incremented the counter `j++` after the line `if (j % 2 ==0)`?

```c
#include <stdio.h>
// print only odd numbers
int main(void) {
    int j = 0;
    while (j < 5) {
        j++;                 // Progress the counter first
        if (j % 2 == 0)      // If even, skip printing
            continue;
        printf("odd: %d\n", j);
    }
    return 0;
}
```

An example of how we can use `break` to stop a loop early:

```c
#include <stdio.h>

int main(void) {
    int num;
    while (1) {  // Infinite loop
        printf("Enter a number (0 to stop): ");
        scanf("%d", &num);
        if (num == 0) {
            break;  // Exit the loop when user enters 0
        }
        printf("You entered: %d\n", num);
    }
    // program will jump to here on break
    printf("Loop ended.\n");
    return 0;
}
```

---

## When to use which loop?

- Use **`while`** when you may not need the loop body to run at all e.g., reading data from a file until you reach the end (sometimes called End Of File EOF).
- Use **`do...while`** when the body must run at least once (e.g., input validation, menus).

---

## Summary

| Feature             | `while`                    | `do...while`               |
|---------------------|----------------------------|----------------------------|
| Condition checked   | Before first iteration     | After first iteration      |
| Runs at least once? | No                         | Yes                        |
| Common use          | Loops that might skip body | Input prompts, menus       |

---

## Quick tips

- Always ensure the loop **condition changes** inside the loop, or you'll create an infinite loop. Everyone has done this at least once :D
- Use braces `{}` even for single statements for clarity.
- For input loops, validate user input inside the loop body and handle `scanf` failures.
- Compile with warnings: `-Wall -Wextra -Werror` to catch common mistakes.

---
