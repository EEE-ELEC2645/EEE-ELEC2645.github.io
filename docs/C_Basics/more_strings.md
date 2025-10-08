---
title: More Strings
parent: C Basics
nav_order: 8
layout: default
---

# Useful String Libraries - `<ctype.h>` and `<string.h>`

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Whilst handling strings in `C` is not necessarily the most user friendly, there are still quite a few helper functions available. Getting to grips with a few of them will help with the Lab activities and your Command Line project.

## `ctype.h` — Character Classification & Conversion

These functions help you work with **individual characters**, especially when checking or changing their type. A full explanation of all the functions can be found [at tutorials point](https://www.tutorialspoint.com/c_standard_library/string_h.htm).

| Function      | Description                                      | Example             |
|---------------|--------------------------------------------------|---------------------|
| `toupper(c)`  | Converts a lowercase letter to uppercase         | `'a' → 'A'`         |
| `tolower(c)`  | Converts an uppercase letter to lowercase         | `'Z' → 'z'`         |
| `isalpha(c)`  | Checks if `c` is a letter (`A`–`Z` or `a`–`z`)    | `'b' → true`        |
| `isdigit(c)`  | Checks if `c` is a digit (`0`–`9`)                | `'5' → true`        |

### Mocking Spongebob Example

If we wanted to check the case of only the letters to write [ALTerNaTIG cApS](https://en.wikipedia.org/wiki/Alternating_caps) we can use functions from the `<ctype.h>` library.

```c
#include <stdio.h>
#include <ctype.h>

int main(void) {
    char input[] = "hello world!";
    for (int i = 0; input[i] != '\0'; i++) {
        if (isalpha(input[i])) {
            // Alternate case: even index → uppercase, odd index → lowercase
            if (i % 2 == 0) {
                input[i] = toupper(input[i]);
            } else {
                input[i] = tolower(input[i]);
            }
        }
    }
    printf("SpOnGeBoB: %s\n", input);
    return 0;
}
```

Gives the result `SpOnGeBoB: HeLlO WoRlD!`

---

## `string.h` — String Manipulation

These functions help you work with **strings** (arrays of characters). A full list of the library functions with examples can be found on [tutorials point](https://www.tutorialspoint.com/c_standard_library/string_h.htm).

Like with using `scanf` to read strings, we need to be careful when using somme of the functions in `<string.h>` as there are some functions with can easily cause [**buffer overflow**](https://cwe.mitre.org/top25/archive/2024/2024_cwe_top25.html) because they do not have fixed maximum characters. Often there are *safer* versions which require specified limits, these normally have an *n* in their name i.e. `strlen` (unsafe) vs `strnlen` (safe).

### `strnlen()`

- Measures the length of a string **up to a maximum** number of characters.
- Prevents reading past the buffer.

```c
size_t len = strnlen(buffer, sizeof(buffer));
```

### `strncpy()`

- Copies a string **up to a specified number of characters**.
- Safer than `strcpy()` but may not null-terminate the result.

```c
strncpy(dest, src, sizeof(dest) - 1);
dest[sizeof(dest) - 1] = '\0'; // Ensure null termination
```

### `strchr()`

- Finds the **first occurrence** of a character in a string.
- Useful for removing line endings or searching for delimiters.

```c
buffer[strcspn(buffer, "\n")] = '\0';
```

or if you might have Windows line endings `\n\r`

```c
buffer[strcspn(buffer, "\r\n")] = '\0';
```

---
