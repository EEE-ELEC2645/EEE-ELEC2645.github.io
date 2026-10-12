---
title: Using files
parent: Files
nav_order: 1
layout: default
---

# Using files
<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

C provides functions through the `<stdio.h>` library for reading data from files and writing data to files.

The usual sequence is:

1. Open the file.
2. Check that it opened successfully.
3. Read from it or write to it.
4. Close it when finished.

## Opening a file

The `fopen()` function opens a file:

```c
FILE *file = fopen("data.txt", "r");
```

The first argument is the path to the file:

```c
"data.txt"
```

The second argument is the mode, which tells C how we want to use the file:

```c
"r"
```

`FILE *` is the type used by the C library to keep track of an open file. The `*` means that it is a pointer, which we will cover properly in Unit 1.4. For now, treat `FILE *` as the type used for an open file.

## Checking that the file opened

Opening a file can fail. For example, the file might not exist or the path might be incorrect.

If `fopen()` cannot open the file, it returns `NULL`.

Always check the result before trying to use the file:

```c
FILE *file = fopen("data.txt", "r");

if (file == NULL) {
    printf("Could not open data.txt\n");
    return 1;
}
```

## File modes

The mode supplied to `fopen()` controls how the file is opened.

Use `"r"` to open an existing file for reading:

```c
FILE *file = fopen("data.txt", "r");
```

The file must already exist.

Use `"w"` to open a file for writing:

```c
FILE *file = fopen("results.txt", "w");
```

If the file does not exist, it is created.

Be careful! If the file already exists, opening it with `"w"` removes its current contents.

Use `"a"` to append data to the end of a file:

```c
FILE *file = fopen("log.txt", "a");
```

If the file already exists, its contents are kept and the new output is added at the end.

A `b` can be added for binary files:

```c
"rb"
"wb"
"ab"
```

The examples on this page use text files.

## Writing to a file

The `fprintf()` function works like `printf()`, except that its first argument specifies the file:

```c
fprintf(file, "Hello!\n");
```

We can use the usual format specifiers:

```c
int score = 42;

fprintf(file, "Score: %d\n", score);
```

The text is written to the file instead of the terminal.

## Reading from a file

The `fgets()` function reads one line from a file:

```c
char line[100];

fgets(line, sizeof(line), file);
```

Its arguments are:

- the character array where the line will be stored
- the size of that array
- the file to read from

If a line is read successfully, `fgets()` returns a non-`NULL` value.

If there is nothing left to read, or an error occurs, it returns `NULL`.

This means we can use it as the condition of a `while` loop:

```c
while (fgets(line, sizeof(line), file) != NULL) {
    printf("%s", line);
}
```

The loop stops when there are no more lines to read.

`fgets()` normally keeps the newline from the file, so we use:

```c
printf("%s", line);
```

rather than adding another `\n`.

## Converting a line into a number

A line read by `fgets()` is a string, even if it contains a number.

For a simple example, we can use `sscanf()` to extract a floating-point value:

```c
float value;

if (sscanf(line, "%f", &value) == 1) {
    printf("Value: %.2f\n", value);
}
```

The return value tells us whether one value was successfully converted.

More detailed input conversion and validation are covered on the [User Input Handling]({% link docs/menus/user_input.md %}) page.

## Closing a file

Every file opened using `fopen()` should be closed using `fclose()`:

```c
fclose(file);
```

Closing the file finishes any pending writes and releases the resources used by the file.

Do not continue using the `FILE *` after the file has been closed.

## Complete example

This program reads numbers from `numbers.txt`, calculates their average and writes the result to `stats.txt`.

The input file contains one number on each line:

```text
12.5
3.0
7.25
-1.5
10
```

The program is:

```c
#include <stdio.h>

#define MAX_LINE_LENGTH 100

int main(void)
{
    // Open the input file
    FILE *input = fopen("numbers.txt", "r");

    if (input == NULL) {
        printf("Could not open numbers.txt\n");
        return 1;
    }

    // Open the output file
    FILE *output = fopen("stats.txt", "w");

    if (output == NULL) {
        printf("Could not open stats.txt\n");
        fclose(input);
        return 1;
    }

    char line[MAX_LINE_LENGTH];
    float sum = 0.0f;
    int count = 0;

    // Read and process each line
    while (fgets(line, sizeof(line), input) != NULL) {
        float value;

        if (sscanf(line, "%f", &value) == 1) {
            sum = sum + value;
            count = count + 1;
        }
    }

    // Make sure at least one number was read
    if (count == 0) {
        printf("No numbers were found\n");

        fclose(input);
        fclose(output);

        return 1;
    }

    float average = sum / count;

    // Write the result
    fprintf(output, "statistic,value\n");
    fprintf(output, "average,%.2f\n", average);

    // Close both files
    fclose(input);
    fclose(output);

    printf("Average written to stats.txt\n");

    return 0;
}
```

The output file contains:

```text
statistic,value
average,6.25
```

The main things to notice are:

- both files are checked after opening
- each line is read using `fgets()`
- `sscanf()` converts the line into a number
- the average is only calculated if at least one number was found
- both files are closed before the program finishes

A useful pattern to remember is:

```text
open → check → use → close
```