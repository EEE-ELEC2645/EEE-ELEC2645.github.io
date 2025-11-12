---
title: User Input Handling
parent: Command Line Basics
nav_order: 1
layout: default
---

# Safe User Input Handling in C

Asking users for input is common in C programmes, but doing so safely can be tricky. This guide covers best practices for reading user input without falling into common pitfalls associated with functions like `scanf()`. Incorrect handling can lead to undefined behaviour, security vulnerabilities, and crashes — famous bugs such as Heartbleed were caused by improper input handling.

The following is based on the excellent article by Felix Palmen: “A Beginner's Guide Away from `scanf()`”.

***

## Why Not Just Use `scanf()`?

`scanf()` isn’t inherently broken, but for beginners, it can lead to serious problems. Here’s **Rule 0**:

> **Rule 0:** Don’t use `scanf()` unless you know exactly what you’re doing.

***

## 1. Reading a Number from the User

### The Classic Example

```c
// example1.c
#include <stdio.h>
int main(void) {
    int a;
    printf("enter a number: ");
    scanf("%d", &a);
    printf("You entered %d.\n", a);
}
```

Looks fine? Try this:

    $ ./example1
    enter a number: abcdefgh
    You entered 38.

**Why?**

*   `scanf()` tried to parse an integer but found no digits.
*   The variable `a` remains **uninitialised**, leading to **undefined behaviour**.

***

### Undefined Behaviour in C

C doesn’t protect you from mistakes. If input doesn’t match the format, `scanf()` leaves variables untouched. Using uninitialised variables can crash your programme or produce rubbish values.

***

### Retrying with `scanf()`

```c
#include <stdio.h>
int main(void) {
    int a;
    printf("enter a number: ");
    while (scanf("%d", &a) != 1) {
        printf("enter a number: ");
    }
    printf("You entered %d.\n", a);
}
```

**Problem:** If input is invalid (e.g., `abc`), `scanf()` never “consumes” it, meaning the buffer is never cleared, causing an infinite loop.

***

> **Rule 1:** `scanf()` is for **parsing** (extracting formatted data), not for **reading raw input**.

***

## 2. Reading a String from the User

### Unsafe Example

```c
#include <stdio.h>
int main(void) {
    char name[12];
    printf("What's your name? ");
    scanf("%s", name);
    printf("Hello %s!\n", name);
}
```

**Issue:** Buffer overflow if input exceeds 11 characters → leading to the **dreaded undefined behaviour**.

***

### Fix with Field Width

```c
#include <stdio.h>
int main(void) {
    char name[40];
    printf("What's your name? ");
    scanf("%39s", name);
    printf("Hello %s!\n", name);
}
```

**Rule 2:** Always use **field widths** with `%s` to prevent overflow.

***

### `%s` Reads Only One Word

`scanf("%s", name)` stops at whitespace. For full lines, use scansets:

```c
scanf(" %39[^\n]", name);
```

But beware: pressing Enter without input leaves `name` uninitialised.

***

> **Rule 3:** `scanf()` format strings differ from `printf()`. Read carefully.

***

## 3. A Better Way: `fgets()`

`fgets()` reads a whole line safely:

```c
#include <stdio.h>
#include <string.h>
int main(void) {
    char name[40];
    printf("What's your name? ");
    if (fgets(name, 40, stdin)) {
        name[strcspn(name, "\n")] = 0; // remove newline
        printf("Hello %s!\n", name);
    }
}
```

We have to use `strcspn()` to strip the newline character (when the user hit enter) that `fgets()` includes.

***

## 4. Reading Numbers Without `scanf()`

Use `atoi` (simple but limited)

```c
#include <stdio.h>
#include <stdlib.h>
int main(void) {
    int a;
    char buf[1024];
    do {
        printf("enter a number: ");
        if (!fgets(buf, 1024, stdin)) return 1;
        a = atoi(buf);
    } while (a == 0);
    printf("You entered %d.\n", a);
}
```

### `atoi()` — *ASCII to Integer*

*   **Purpose:** Converts a string (e.g., `"123"`) into an `int`.
*   **Header:** `#include <stdlib.h>`
*   **Usage:**
    ```c
    int value = atoi("123"); // value = 123
    ```
*   **Behaviour:**
    *   Reads characters from the start of the string until a non-digit is found.
    *   Ignores leading whitespace.
    *   Returns `0` if no valid conversion is possible.
*   **Limitations:**
    *   No error reporting — you can’t tell if `0` means “invalid input” or actual zero.
    *   Doesn’t handle overflow or invalid characters well - what happens if you input "123abc"? or a number larger than `INT_MAX`?

Another simple function is `atof()` for floating-point numbers.

### `atof()` — *ASCII to Floating-point*
*   **Purpose:** Converts a string (e.g., `"3.14"`) into a `double`.
*   **Header:** `#include <stdlib.h>`
*   **Usage:**
    ```c
    double value = atof("3.14"); // value = 3.14
    ```
*   **Behaviour:**
    *   Similar to `atoi()`, but parses floating-point numbers.
    *   Stops at the first invalid character.
*   **Limitations:**
    *   Same as `atoi()` — no error checking, no way to detect invalid input reliably.

***

### Better Alternatives

*   Use **`strtol()`** for integers and **`strtod()`** for floating-point values:
    *   They provide error checking via `endptr` and `errno`.
    *   Safer for robust input handling.


### Using `strtol()` (robust)

```c
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
int main(void) {
    long a;
    char buf[1024];
    int success;
    do {
        printf("enter a number: ");
        if (!fgets(buf, 1024, stdin)) return 1;
        char *endptr;
        errno = 0;
        a = strtol(buf, &endptr, 10);
        if (errno == ERANGE || endptr == buf || (*endptr && *endptr != '\n'))
            success = 0;
        else
            success = 1;
    } while (!success);
    printf("You entered %ld.\n", a);
}
```

### `strtol()` — *String to Long Integer*

*   **Purpose:** Converts a string (e.g., `"123"`) into a `long` integer with **error checking**.
*   **Header:**
    ```c
    #include <stdlib.h>
    #include <errno.h>
    ```
*   **Function Signature:**
    ```c
    long strtol(const char *nptr, char **endptr, int base);
    ```
*   **Parameters:**
    *   `nptr`: Pointer to the string to convert.
    *   `endptr`: Pointer to a pointer — after conversion, it points to the first character that wasn’t converted.
    *   `base`: Number base (e.g., `10` for decimal, `16` for hex).

### How It Works

*   Reads characters from the string and converts them to a number.
*   Stops at the first invalid character.
*   Sets `errno` to `ERANGE` if the number is too large or too small.
*   Allows you to check:
    *   If **nothing was converted** (`endptr == nptr`).
    *   If there are **extra characters** after the number (`*endptr != '\0'` or `'\n'`).

### Why `strtol()` is Better than `atoi()`

*   **Error detection:** Can tell if conversion failed or if extra characters exist.
*   **Range checking:** Detects overflow/underflow.
*   **Flexible:** Works with different bases (binary, hex, etc.).

***

## 5. Can `scanf()` Be Fixed?

Yes, but it’s tricky:

```c
#include <stdio.h>
int main(void) {
    int a, rc;
    printf("enter a number: ");
    while ((rc = scanf("%d", &a)) == 0) {
        scanf("%*[^\n]"); // discard invalid input
        printf("enter a number: ");
    }
    if (rc == EOF)
        printf("Nothing more to read.\n");
    else
        printf("You entered %d.\n", a);
}
```

***

> **Rule 4:** `scanf()` is powerful but dangerous. Prefer simpler, safer functions like `fgets()` for reading input.

***

## Best Practices for Safe Input in C

1.  **Avoid `scanf()` for general input**  
    Use `fgets()` for reading lines and then parse manually.

2.  **Always validate input**  
    Check return values of functions like `fgets()`, `strtol()`, etc.

3.  **Prevent buffer overflows**
    *   Use field widths with `%s` if you must use `scanf()`.
    *   Allocate enough space for strings (including `\0` terminator).

4.  **Use robust conversion functions**
    *   Prefer `strtol()`, `strtod()`, etc., over `atoi()` for better error handling.

5.  **Handle edge cases**
    *   Empty input
    *   Extra characters after numbers
    *   Out-of-range values

6.  **Never use `fflush(stdin)`**  
    It’s undefined behaviour in C.

7.  **Read the manual**  
    `scanf()` and `printf()` format strings differ—know the rules.

***

```
