---
title: More Strings
parent: C Basics
nav_order: 8
layout: default
---

# More strings

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Handling strings in C is not always user friendly, but the standard library provides functions for common jobs such as measuring, comparing and searching strings.

This page introduces some of the functions from:

- `<ctype.h>` for working with individual characters
- `<string.h>` for working with strings

## Character functions with `<ctype.h>`

The functions in `<ctype.h>` work with one character at a time.

Include the library with:

```c
#include <ctype.h>
```

Some useful functions are:

| Function | Description |
|:---------|:------------|
| `isalpha(c)` | Checks whether `c` is a letter |
| `isdigit(c)` | Checks whether `c` is a digit |
| `isspace(c)` | Checks whether `c` is a whitespace character |
| `toupper(c)` | Converts a lowercase letter to uppercase |
| `tolower(c)` | Converts an uppercase letter to lowercase |

The checking functions return zero if the check is false and a non-zero value if it is true.

For example:

```c
#include <ctype.h>
#include <stdio.h>

int main(void)
{
    char character = '7';

    if (isdigit(character)) {
        printf("%c is a digit\n", character);
    }

    return 0;
}
```

### Converting characters to uppercase

We can use `toupper()` to convert an individual character:

```c
#include <ctype.h>
#include <stdio.h>

int main(void)
{
    char letter = 'a';

    letter = toupper(letter);

    printf("%c\n", letter);

    return 0;
}
```

This prints:

```text
A
```

If the character is not a lowercase letter, `toupper()` returns it unchanged.

## Changing a complete string

Since a string is an array of characters, we can use a loop to apply `toupper()` to each character:

```c
#include <ctype.h>
#include <stdio.h>

int main(void)
{
    char name[] = "Ash Ketchum";

    for (int i = 0; name[i] != '\0'; i++) {
        name[i] = toupper(name[i]);
    }

    printf("%s\n", name);

    return 0;
}
```

This prints:

```text
ASH KETCHUM
```

The loop stops when it reaches the terminating `'\0'`.

## Mocking SpongeBob example

We can also use the functions from `<ctype.h>` to write in [AlTeRnAtInG cApS](https://en.wikipedia.org/wiki/Alternating_caps):

```c
#include <ctype.h>
#include <stdio.h>

int main(void)
{
    char input[] = "Ash Ketchum";
    int make_uppercase = 1;

    for (int i = 0; input[i] != '\0'; i++) {
        if (isalpha(input[i])) {
            if (make_uppercase) {
                input[i] = toupper(input[i]);
            } else {
                input[i] = tolower(input[i]);
            }

            make_uppercase = !make_uppercase;
        }
    }

    printf("%s\n", input);

    return 0;
}
```

This gives:

```text
AsH kEtChUm
```

The variable `make_uppercase` is only changed when a letter is found. Spaces and punctuation therefore do not affect the alternating pattern.

## String functions with `<string.h>`

The functions in `<string.h>` work with null-terminated strings.

Include the library with:

```c
#include <string.h>
```

These functions rely on the terminating `'\0'` to find the end of the string. If the terminator is missing, the function may continue reading beyond the end of the array.

## Finding the length with `strlen()`

The `strlen()` function returns the number of characters before the terminating `'\0'`:

```c
#include <stdio.h>
#include <string.h>

int main(void)
{
    char name[] = "Ash Ketchum";
    size_t length = strlen(name);

    printf("Length = %zu\n", length);

    return 0;
}
```

This prints:

```text
Length = 11
```

The space counts as a character, but the terminating `'\0'` is not included in the length.

`strlen()` returns a value of type `size_t`, so `%zu` is used to print it.

The size of the array and the length of the string are not necessarily the same:

```c
char name[20] = "Ash";
```

Here:

```c
sizeof(name)    // 20
strlen(name)    // 3
```

`sizeof(name)` gives the amount of memory allocated to the array. `strlen(name)` counts the characters currently stored before `'\0'`.

## Comparing strings with `strcmp()`

We cannot compare the contents of two strings using `==`:

```c
if (name == "Ash") {
    // This does not compare the text in the strings
}
```

To compare strings, use `strcmp()`:

```c
#include <stdio.h>
#include <string.h>

int main(void)
{
    char name[] = "Ash";

    if (strcmp(name, "Ash") == 0) {
        printf("The strings match\n");
    }

    return 0;
}
```

`strcmp()` returns:

- `0` if the strings are equal
- a value less than zero if the first string comes before the second
- a value greater than zero if the first string comes after the second

The important one to remember initially is:

```c
strcmp(first, second) == 0
```

This checks whether the two strings contain the same text.

String comparisons are case-sensitive:

```c
strcmp("Ash", "ash")
```

does not return zero because uppercase and lowercase letters have different character values.

## Copying strings

You cannot copy one character array to another using assignment:

```c
char source[] = "Ash";
char destination[20];

destination = source;     // Not allowed
```

One option is `strcpy()`:

```c
strcpy(destination, source);
```

However, `strcpy()` does not know how large `destination` is. If the source string is too long, it will write beyond the end of the array.

A simple alternative when creating formatted strings is `snprintf()`:

```c
#include <stdio.h>

int main(void)
{
    char destination[20];
    char source[] = "Ash Ketchum";

    snprintf(destination, sizeof(destination), "%s", source);

    printf("%s\n", destination);

    return 0;
}
```

The second argument tells `snprintf()` the total size of the destination array, including the space needed for `'\0'`.

If the text is too long, it is shortened to fit rather than being written beyond the end of the array.

## Joining strings

`snprintf()` can also combine several values into one string:

```c
#include <stdio.h>

int main(void)
{
    char first_name[] = "Ash";
    char surname[] = "Ketchum";
    char full_name[30];

    snprintf(
        full_name,
        sizeof(full_name),
        "%s %s",
        first_name,
        surname
    );

    printf("%s\n", full_name);

    return 0;
}
```

This produces:

```text
Ash Ketchum
```

This is often clearer than repeatedly joining strings using `strcat()`.

## Finding a character with `strchr()`

The `strchr()` function searches a string for the first occurrence of a character:

```c
#include <stdio.h>
#include <string.h>

int main(void)
{
    char name[] = "Ash Ketchum";

    if (strchr(name, ' ') != NULL) {
        printf("The string contains a space\n");
    }

    return 0;
}
```

If the character is found, `strchr()` returns its location in the string. If it cannot find the character, it returns `NULL`.

For now, it is enough to use the result as a check:

```c
if (strchr(name, ' ') != NULL) {
    // The character was found
}
```

We will look at exactly what the returned value means when we cover pointers.

## Finding part of a string with `strstr()`

The `strstr()` function searches for one string inside another:

```c
#include <stdio.h>
#include <string.h>

int main(void)
{
    char name[] = "Ash Ketchum";

    if (strstr(name, "Ketchum") != NULL) {
        printf("Surname found\n");
    }

    return 0;
}
```

Like `strchr()`, it returns `NULL` if no match is found.

The comparison is case-sensitive, so searching for `"ketchum"` would not find `"Ketchum"`.

## A note about user input

Functions such as `strlen()`, `strcmp()` and `strchr()` expect a correctly null-terminated string. When a string comes from user input, you must also think about:

- the size of the destination array
- invalid input
- spaces in the input
- the newline added by `fgets()`
- converting text into numbers

These are covered on the [User Input Handling]({% link docs/menus/user_input.md %}) page. 

