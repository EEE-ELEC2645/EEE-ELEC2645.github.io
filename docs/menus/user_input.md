---
title: User input
parent: Command Line Basics
nav_order: 3
layout: default
---

# User input

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Every program eventually meets a user who types `banana` when asked for a number. This page shows how to read input so that your program copes: read the whole line with `fgets()`, then check it with `sscanf()`. This is the `read_integer()` pattern used in Unit 2.1 Activity 3 and the Unit 2 project template.

Parts of this page are based on Felix Palmen's article "A beginner's guide away from `scanf()`".

## What goes wrong with `scanf()`

`scanf("%d", &number)` is fine when the user types a number. When they do not, two things go wrong.

### Bad input stays in the input

If `scanf()` cannot read a number, it leaves the text where it is. A loop that tries again reads the same text again, forever:

```c
printf("Enter a number: ");
while (scanf("%d", &number) != 1) {
    printf("That is not a number. Try again: ");
}
```

Typing `abc` produces:

```text
Enter a number: abc
That is not a number. Try again: That is not a number. Try again: That is not a number. Try again: ...
```

Press `Ctrl+C` to stop it.

### Part of the input is accepted

`scanf()` reads as much of a number as it can and leaves the rest for the next read. In a menu that reads a choice and then a number:

```c
printf("Select item: ");
scanf("%d", &choice);
printf("Enter a number: ");
scanf("%d", &number);
```

Typing `4.5` at the first prompt selects option 4. The `.5` is left behind, so the second `scanf()` fails straight away without waiting for the user:

```text
Select item: 4.5
Enter a number: choice = 4, number = 0
```

The underlying problem is that `scanf()` is a tool for *parsing* text (picking values out of it), not for *reading* what the user typed.

## Read a whole line with `fgets()`

`fgets()` reads one whole line, including the newline from the Enter key, into a character array:

```c
char line[64];

if (fgets(line, sizeof(line), stdin) == NULL) {
    // no input: the user pressed Ctrl+D, or input ended
}
```

- the second argument is the size of the array, so `fgets()` never writes past the end
- `fgets()` returns `NULL` if there is nothing left to read
- whatever the user typed has now been taken out of the input, so bad text cannot get stuck

## Check the line with `sscanf()`

`sscanf()` works like `scanf()`, but reads from a string instead of the keyboard. It returns the number of values it filled in. The format `"%d %c"` asks for a number and then any other character:

```c
int value;
char extra;

if (sscanf(line, "%d %c", &value, &extra) == 1) {
    // exactly one number and nothing else
}
```

- `%d` skips any spaces, then reads a whole number
- the space skips any spaces and the newline after it
- `%c` catches the first character left over, if there is one

So a return value of exactly 1 means the line held one number and nothing else:

| User types | `sscanf()` returns | Why |
| --- | --- | --- |
| `42` | 1 | a number and nothing else |
| `  42  ` | 1 | spaces are skipped |
| `-7` | 1 | negative numbers are fine |
| `42abc` | 2 | `extra` caught the `a` |
| `4.5` | 2 | `extra` caught the `.` |
| `12 13` | 2 | `extra` caught the `1` of `13` |
| `abc` | 0 | no number at the start |
| (just Enter) | -1 (`EOF`) | the line is empty |

## The `read_integer()` function

Putting the two together gives the function used in Activity 3 and the project template:

```c
// Reads one whole line and checks that it holds exactly one integer.
// Returns 1 and stores the number in *value if the input is valid.
// Returns 0 if it is not, and leaves *value unchanged.
int read_integer(const char *prompt, int *value)
{
    char line[64];
    char extra;

    printf("%s", prompt);

    if (fgets(line, sizeof(line), stdin) == NULL) {
        return 0;
    }

    if (sscanf(line, "%d %c", value, &extra) != 1) {
        return 0;
    }

    return 1;
}
```

The function has two results: whether the input was valid, and the number itself. The return value reports success or failure, and the number comes back through the pointer `value`. See [pointers and functions]({% link docs/pointers/pointer_functions.md %}) if this pattern is new.

## Complete example

```c
#include <stdio.h>

int read_integer(const char *prompt, int *value);

int main(void)
{
    int age = 0;

    for (int attempt = 0; attempt < 3; attempt++) {
        if (read_integer("Enter your age: ", &age)) {
            printf("In 10 years you will be %d.\n", age + 10);
            return 0;
        }
        printf("Please enter a whole number, like 19.\n");
    }

    printf("Too many invalid attempts.\n");
    return 1;
}

// Reads one whole line and checks that it holds exactly one integer.
// Returns 1 and stores the number in *value if the input is valid.
// Returns 0 if it is not, and leaves *value unchanged.
int read_integer(const char *prompt, int *value)
{
    char line[64];
    char extra;

    printf("%s", prompt);

    if (fgets(line, sizeof(line), stdin) == NULL) {
        return 0;
    }

    if (sscanf(line, "%d %c", value, &extra) != 1) {
        return 0;
    }

    return 1;
}
```

This produces (typing `abc`, then `12abc`, then `19`):

```text
Enter your age: abc
Please enter a whole number, like 19.
Enter your age: 12abc
Please enter a whole number, like 19.
Enter your age: 19
In 10 years you will be 29.
```

The program gives up after three attempts. A loop that keeps asking forever would never end if the input ran out (for example after `Ctrl+D`), because `read_integer()` would keep returning 0.

## Valid number, wrong value

`read_integer()` checks that the user typed a number. It cannot know which numbers make sense for your program, so check the range yourself:

```c
if (!read_integer("Enter your age: ", &age)) {
    printf("Please enter a whole number.\n");
} else if (age < 0 || age > 150) {
    printf("%d is not a realistic age.\n", age);
} else {
    printf("In 10 years you will be %d.\n", age + 10);
}
```

The same applies to menus: `7` is a valid number but not a valid menu option, which is what `default` in the `switch` is for.

## Reading text

To read a line of text, such as a name, use `fgets()` on its own. The line includes the newline from the Enter key, so remove it:

```c
#include <stdio.h>
#include <string.h>

int main(void)
{
    char name[32];

    printf("What's your name? ");

    if (fgets(name, sizeof(name), stdin) == NULL) {
        return 1;
    }

    name[strcspn(name, "\n")] = '\0';  // remove the newline, if there is one
    printf("Hello %s!\n", name);
    return 0;
}
```

This produces (typing `Ada Lovelace`):

```text
What's your name? Ada Lovelace
Hello Ada Lovelace!
```

`strcspn(name, "\n")` returns the position of the first newline (or the end of the string if there is none), and the `'\0'` ends the string there. Without that line the output would be:

```text
Hello Ada Lovelace
!
```

Unlike `scanf("%s", ...)`, `fgets()` keeps the spaces, so the whole name is read.

## Do not use `atoi()` or `atof()`

You will see `atoi()` (text to `int`) and `atof()` (text to `double`) in older code. Avoid them:

- `atoi("abc")` and `atoi("0")` both return 0, so you cannot tell an error from a real zero
- `atoi("12abc")` returns 12 and ignores the rest without telling you
- a number too big for an `int` gives undefined behaviour

Use `read_integer()`, or [`strtol()`]({% link docs/menus/user_input_strtol.md %}) when you need more control.

## Common mistakes

### Mixing `scanf()` and `fgets()`

`scanf("%d", &age)` reads the number but leaves the newline from the Enter key in the input. A following `fgets()` reads that newline as an empty line and does not wait for the user:

```text
Enter your age: 19
Enter your name: Hello , you are 19.
```

Use `fgets()` for every input in the program, with `read_integer()` for numbers.

### Using the value after a failed read

If `read_integer()` returns 0, the number was not written. Do not use it:

```c
read_integer("Enter a number: ", &number);
printf("Double is %d\n", 2 * number);  // wrong if the read failed
```

Always check the return value first.

### `fflush(stdin)`

You may see `fflush(stdin)` suggested online for clearing bad input. The C standard only defines `fflush()` for output, so on input it is undefined behaviour: it clears the input on some systems and does nothing on others. Reading whole lines with `fgets()` means there is nothing to clear.

### Very large numbers

`sscanf()` with `%d` cannot report a number that is too big for an `int`. Typing `99999999999` in the complete example produces:

```text
Enter your age: 99999999999
In 10 years you will be 1215752201.
```

The C standard says this is undefined behaviour, so any result is possible. [Checking numbers with `strtol()`]({% link docs/menus/user_input_strtol.md %}) shows how to detect it.

The main points to remember are:

- `scanf()` leaves bad input behind and accepts partial numbers
- read every input as a whole line with `fgets()`
- check the line with `sscanf(line, "%d %c", ...)`: a return value of exactly 1 means one number and nothing else
- return success or failure from the function, and the value through a pointer
- check the range of a valid number yourself
- remove the newline from text read with `fgets()`
