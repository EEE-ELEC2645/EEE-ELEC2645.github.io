---
title: Checking numbers with strtol()
parent: Command Line Basics
nav_order: 4
layout: default
---

# Checking numbers with `strtol()`

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

This page is an extension. The `read_integer()` function on [User input]({% link docs/menus/user_input.md %}) is enough for Unit 2. Use `strtol()` when you also need to:

- reject numbers that are too big, which `sscanf()` cannot detect
- check that a number is within a range, as part of reading it
- read numbers in hexadecimal, such as register values in Units 3 and 4

## What `strtol()` does

`strtol()` ("string to long") converts the start of a string into a `long`. It is declared in `stdlib.h`:

```c
long strtol(const char *str, char **end, int base);
```

- `str` is the text to convert
- `end` is the address of a `char *`. `strtol()` sets it to point at the first character it did not use
- `base` is the number base: 10 for decimal, 16 for hexadecimal

For example:

```c
char *end;
long number = strtol("42abc", &end, 10);
// number is 42, and end points at "abc"
```

`end` is what makes `strtol()` useful: by looking at where it stopped, you can tell exactly how much of the text was a number.

| Text | Value returned | `end` points at |
| --- | --- | --- |
| `"42"` | 42 | the end of the string |
| `"  42abc"` | 42 | `"abc"` |
| `"4.5"` | 4 | `".5"` |
| `"abc"` | 0 | `"abc"` (nothing was converted) |

## Detecting errors

There are three things to check after calling `strtol()`.

### Was there a number at all?

If `end` still points at the start of the string, no digits were found:

```c
if (end == line) {
    // no number
}
```

Do not check for a return value of 0 instead: `"0"` is a perfectly good number.

### Was there anything after it?

Skip any spaces and the newline, then check you have reached the end of the string:

```c
while (isspace((unsigned char)*end)) {
    end++;
}

if (*end != '\0') {
    // extra characters, such as "12abc" or "12 13"
}
```

`isspace()` comes from `ctype.h`. The cast to `unsigned char` is needed because `isspace()` only accepts values that fit in an `unsigned char` (or `EOF`).

### Was the number too big?

If the number does not fit in a `long`, `strtol()` returns the largest (or smallest) `long` and sets the global variable `errno` to `ERANGE`. `errno` is never reset for you, so set it to 0 first:

```c
errno = 0;
number = strtol(line, &end, 10);

if (errno == ERANGE) {
    // too big or too small for a long
}
```

`errno` and `ERANGE` come from `errno.h`.

A `long` is not always bigger than an `int`. In the Codespace a `long` is 64 bits, so `99999999999` fits in a `long` without setting `errno`, but not in an `int`. On the STM32, `long` and `int` are both 32 bits. Checking that the number is within the range you need handles both cases.

## Complete example

`read_integer_range()` reads a line, converts it with `strtol()` and makes all the checks above:

```c
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <ctype.h>

int read_integer_range(const char *prompt, int min, int max, int *value);

int main(void)
{
    int volume = 0;

    for (int attempt = 0; attempt < 3; attempt++) {
        if (read_integer_range("Set the volume (0 to 11): ", 0, 11, &volume)) {
            printf("Volume set to %d.\n", volume);
            return 0;
        }
        printf("Please enter a whole number from 0 to 11.\n");
    }

    printf("Too many invalid attempts.\n");
    return 1;
}

// Reads one whole line and checks that it holds exactly one integer
// from min to max. Returns 1 and stores the number in *value if the
// input is valid. Returns 0 if it is not, and leaves *value unchanged.
int read_integer_range(const char *prompt, int min, int max, int *value)
{
    char line[64];
    char *end;
    long number;

    printf("%s", prompt);

    if (fgets(line, sizeof(line), stdin) == NULL) {
        return 0;
    }

    errno = 0;
    number = strtol(line, &end, 10);

    if (end == line) {
        return 0;  // no number at the start of the line
    }

    while (isspace((unsigned char)*end)) {
        end++;  // skip spaces and the newline after the number
    }

    if (*end != '\0') {
        return 0;  // something other than spaces after the number
    }

    if (errno == ERANGE || number < min || number > max) {
        return 0;  // too big for a long, or outside min to max
    }

    *value = (int)number;
    return 1;
}
```

This produces (typing `eleven`, then `99999999999`, then `11`):

```text
Set the volume (0 to 11): eleven
Please enter a whole number from 0 to 11.
Set the volume (0 to 11): 99999999999
Please enter a whole number from 0 to 11.
Set the volume (0 to 11): 11
Volume set to 11.
```

Because this one goes to 11.

The cast `(int)number` is safe because `number` has already been checked against `min` and `max`, which are both `int` values.

Compared with `read_integer()`:

| User types | `read_integer()` | `read_integer_range(..., 0, 11, ...)` |
| --- | --- | --- |
| `7` | accepted | accepted |
| `12abc`, `4.5`, `abc` | rejected | rejected |
| `12` | accepted (check the range yourself) | rejected |
| `99999999999` | accepted, with a wrong value | rejected |

The same function works for menus: `read_integer_range("Select item: ", 1, 5, &choice)` only accepts a valid option.

## Hexadecimal numbers

The last argument of `strtol()` is the base. Base 16 reads hexadecimal, with or without `0x` at the start:

```c
long a = strtol("1F", NULL, 16);    // 31
long b = strtol("0x1F", NULL, 16);  // 31
```

Passing `NULL` for `end` is allowed when you do not need it, but then you cannot check for errors.

Base 0 lets `strtol()` choose from the text itself: `0x` means hexadecimal, a leading `0` means octal (base 8), and anything else is decimal. Watch out: with base 0, `"010"` is 8, not 10.

`strtod()` ("string to double") works in the same way for decimal numbers such as `4.5`.

## Common mistakes

### Checking `errno` without resetting it

`strtol()` only sets `errno` when something goes wrong. If an earlier error left it set to `ERANGE`, a valid number looks like an error. Always set `errno = 0` just before calling `strtol()`.

### Checking only `errno`

`errno` is not set for text such as `abc` or `12abc`. You still need the `end` checks.

### Using base 0 for user input

A user typing `010` almost certainly means 10. Use base 10 unless you really want `strtol()` to guess.

Yes, this is a lot of checking for one number. But you only write `read_integer_range()` once, then call it every time your program needs a number. Making sure your inputs are safe is worth the effort: many real bugs, and security holes, start with input that nobody expected.

The main points to remember are:

- `strtol()` converts text to a `long` and sets `end` to the first character it did not use
- `end == line` means there was no number; characters other than spaces after `end` mean extra input
- set `errno = 0` before the call and check for `ERANGE` after it
- check the range you need, which also catches numbers that fit in a `long` but not in an `int`
- base 16 reads hexadecimal; avoid base 0 for user input
