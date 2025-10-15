---
title: Conditionals
parent: C Basics
nav_order: 3
layout: default
---

# C Conditionals: `if`, `else`, and `switch`

## Introduction

In C programming, **conditionals** let your program make decisions. They control which parts of your
code run based on values or logic. This is essential for tasks like checking user input, handling
errors, or choosing between actions.

---

## Why conditionals matter

Without conditionals, your program would run the same way every time. Conditionals let you:

- Respond to different inputs
- Branch into different logic paths
- Make your program interactive and robust

They keep code clear, testable, and efficient - especially when combined with good naming and small functions.

---

## `if`, `else if`, and `else`

### Syntax

```c
if (condition) {
  // do stuff if condition is true
} else if (another_condition) {
  // do stuff if another_condition is true
} else {
  // do stuff if none of the above are true
}
```

**Rules of thumb**

- Conditions are evaluated **top to bottom**; the first true branch runs, the rest are skipped.
- Always use braces `{}` even for one-line statements after e.g. `if (x >1) y=1` but its not good practice as its easy to get confused when adding more lines later.
- Keep conditions simple; extract complex checks into well-named helper functions. i.e. better use `if (CheckInput(input,limits) == 1 )` than `if ( input < X && X < Y and input >= Z .......... )`
- Enable warnings (`-Wall -Wextra -Werror`) to catch mistakes like `=` vs `==`. I promise you will make this mistake at least once! :D

### Example 1 — Simple `if`

```c
#include <stdio.h>

int main(void) {
  int score = 85;
  if (score >= 50) {
    printf("Pass\n");
  }
  return 0;
}
```

### Example 2 — `if...else`

```c
#include <stdio.h>

int main(void) {
  int score = 45;
  if (score >= 50) {
    printf("Pass\n");
  } else {
    printf("Fail\n");
  }
  return 0;
}
```

### Example 3 — `if...else if...else` chain

```c
#include <stdio.h>

int main(void) {
  int grade = 72;
  if (grade >= 70) {
    printf("Class I\n");
  } else if (grade >= 60) {
    printf("Class II.i\n");
  } else if (grade >= 50) {
    printf("Class II.ii\n");
  } else if (grade >= 40) {
    printf("Class III\n");
  } else {
    printf("Fail\n");
  }
  return 0;
}
```

### Example 4 — Nested `if` (use sparingly)

```c
#include <stdio.h>

int main(void) {
  int x = 15;
  if (x > 0) {
    if (x % 2 == 0) {
      printf("positive even\n");
    } else {
      printf("positive odd\n");
    }
  } else {
    printf("non-positive\n");
  }
  return 0;
}
```


 Notice things can get complicated quickly with many nested if statements! If you find yourself in this situation it might be better to reconsider the approach.

---

## `switch` statement

A more readable way to handle multiple branches is with a `switch` statement, which allow you to choose between several possible values of a single variable:

### Syntax

```c
switch (expression) {
  case VALUE_1:
    // statements
    break;
  case VALUE_2:
    // statements
    break;
  default:
    // statements if no case matches
    break;
}
```

- `expression` is evaluated once; control jumps to the matching `case` label.
- `case` labels must be **integer constant expressions** (e.g., literals, enum constants).
- Add `break;` to avoid the program continuing on to the next case, in some *extremely rare* cases you might want this but probably not!
- Use `default` for unexpected values. Here you might log or handle the errors

### Example 1 — Switch-based menu

```c
#include <stdio.h>

int main(void) {
  int choice = 0;
  printf("Menu:\n1. Start\n2. Stop\n3. Exit\nEnter choice: ");
  if (scanf("%d", &choice) != 1) return 1;

  switch (choice) {
    case 1:
      printf("Starting...\n");
      break;
    case 2:
      printf("Stopping...\n");
      break;
    case 3:
      printf("Exiting...\n");
      break;
    default:
      printf("Invalid choice.\n");
      break;
  }
  return 0;
}
```

### Example 2 — Switch with `enum`

Switch statements are useful when combined with `enums` especially when using finite state machines in Semester 2. For example if we had a program that could be in three states, we can use a switch statement using our enum variables: 


```c
#include <stdio.h>

typedef enum {
  MODE_IDLE = 0,
  MODE_RUN  = 1,
  MODE_ERR  = 2
} mode_t;

int main(void) {
  mode_t m = MODE_RUN;

  switch (m) {
    case MODE_IDLE:
      printf("idle\n");
      break;
    case MODE_RUN:
      printf("run\n");
      break;
    case MODE_ERR:
      printf("error\n");
      break;
    default:
      printf("unknown mode\n");
      break;
  }
  return 0;
}
```

### Example 3 — Declaring variables in a `switch` case safely

This is something I personally have got wrong many times! Each case within `{}` creates its own scope so that variables declared in the case are local to that case only. 

```c
#include <stdio.h>

int main(void) {
  int x = 2;
  switch (x) {
    case 1: {
      int a = 10;  // this 'a' belongs to the scope within {}
      printf("case 1: a = %d\n", a);
      break;
    }
    case 2: {
      int a = 20;  // this is ok as we are in a separate scope to case 1
      printf("case 2: a = %d\n", a);
      break;
    }
    default:
      printf("default\n");
      break;
  }
  return 0;
}
```

---

## When to use which?

- Use **`if` / `else if` / `else`** for:
  - Ranges or relational logic (e.g., `x < 10`, `a <= b && b < c`)
  - Complex boolean expressions
  - Situations where conditions are not small, fixed constants

- Use **`switch`** for:
  - **Discrete values** (menu options, key codes, protocol IDs)
  - Cleaner, flatter code when there are many exact-match branches
  - Enums, where each constant maps to a branch

---

## Summary table

| Feature                | `if` / `else if` / `else`            | `switch`                                  |
|------------------------|---------------------------------------|-------------------------------------------|
| Best for               | Ranges, complex conditions            | Discrete values, enums                     |
| Readability            | Great for few branches                | Great for many exact matches               |
| Fall-through           | Not applicable                        | Possible; requires `break` or comment      |
| Expression types       | Any expression yielding boolean truth | Integral types / enums                     |
| Maintenance            | Can grow nested                        | Scales well with many cases                |

---

## Quick tips

- Prefer clear, simple conditions; extract complicated logic into helper functions.
- Always use braces `{}` for consistency and safety.
- In `switch`, **break each case** unless you intentionally fall through.
- Consider enabling `-Wswitch` and `-Wswitch-enum` to catch unhandled enum values.
- Compile with strict warnings: `-Wall -Wextra -Werror`.

---
