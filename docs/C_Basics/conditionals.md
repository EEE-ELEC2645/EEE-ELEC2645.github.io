---
title: Conditionals
parent: C Basics
nav_order: 2
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

They keep code clear, testable, and efficient—especially when combined with good naming and small
functions.

---

## `if`, `else if`, and `else`

### Syntax

```c
if (condition) {
  // statements if condition is true
} else if (another_condition) {
  // statements if another_condition is true
} else {
  // statements if none of the above are true
}
```

**Rules of thumb**
- Conditions are evaluated **top to bottom**; the first true branch runs, the rest are skipped.
- Use braces `{}` even for one-line bodies—this avoids mistakes when adding lines later.
- Keep conditions simple; extract complex checks into well-named helper functions. i.e. better use `if (CheckInput(input,limits) == 1 )` than `if ( input < X && X < Y and input >= Z .......... ) `
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

> Prefer keeping nesting shallow. If a function gets deeply nested, extract helper functions or
> return early from trivial precondition checks.

---

## `switch` statement

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

**Key points**
- `expression` is evaluated once; control jumps to the matching `case` label.
- `case` labels must be **integer constant expressions** (e.g., literals, enum constants).
- Add `break;` to avoid falling through to the next case (unless intentional).
- Use `default` for unexpected values; log or handle the error path.

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

### Example 3 — Combining cases + intentional fall-through

```c
#include <stdio.h>

int main(void) {
  int ch = 'A';
  switch (ch) {
    case 'A':
    case 'E':
    case 'I':
    case 'O':
    case 'U':
      printf("uppercase vowel\n");
      break;
    case 'a':
    case 'e':
    case 'i':
    case 'o':
    case 'u':
      /* fall through */
    default:
      printf("lowercase vowel or other\n");
      break;
  }
  return 0;
}
```

> **Fall-through:** If you intend to fall through to the next case, add an explicit comment like
> `/* fall through */` and enable `-Wimplicit-fallthrough` (GCC/Clang) to catch accidents.

### Example 4 — Declaring variables in a `switch` case safely

```c
#include <stdio.h>

int main(void) {
  int x = 2;
  switch (x) {
    case 1: {
      int a = 10;  // create a scope with braces
      printf("case 1: a = %d\n", a);
      break;
    }
    case 2: {
      int b = 20;  // separate scope prevents crossing initialisations
      printf("case 2: b = %d\n", b);
      break;
    }
    default:
      printf("default\n");
      break;
  }
  return 0;
}
```

> In C, `case` labels are not blocks. Use braces to create a new scope when declaring variables
> inside cases.

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

## Compile & run examples

```bash
cc -std=c99 -Wall -Wextra -Werror if_simple.c -o if_simple
cc -std=c99 -Wall -Wextra -Werror if_else.c -o if_else
cc -std=c99 -Wall -Wextra -Werror if_chain.c -o if_chain
cc -std=c99 -Wall -Wextra -Werror switch_menu.c -o switch_menu
cc -std=c99 -Wall -Wextra -Werror switch_enum.c -o switch_enum
cc -std=c99 -Wall -Wextra -Werror switch_scoped.c -o switch_scoped
```
