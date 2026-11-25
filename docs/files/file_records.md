---
title: Saving and loading records
parent: Files
nav_order: 2
layout: default
---

# Saving and loading records

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Many programs keep a list of records that must still be there the next time the program runs: quiz results, a parts inventory, high scores. A small "database" like this needs no special library: save each record as one line of a text file, and read the lines back into [structs]({% link docs/C_Basics/structs.md %}) when the program starts.

## One record per line

Each record is one line, with its fields separated by commas:

```text
Arrays,8,10
Pointers,7,10
```

This is a CSV (comma-separated values) file. You can read it, fix it in any text editor, open it in a spreadsheet, and see changes to it with `git diff`.

In the program, each line matches one struct:

```c
typedef struct {
    char topic[TOPIC_LENGTH];
    int score;
    int out_of;
} quiz_result_t;
```

## Saving

Open the file with `"w"`, which replaces the old contents, and write one line per record with `fprintf()`:

```c
for (int i = 0; i < 2; i++) {
    fprintf(file, "%s,%d,%d\n", results[i].topic, results[i].score, results[i].out_of);
}
```

## Loading

Read the file line by line with `fgets()`, as in [Using files]({% link docs/files/file_basics.md %}), and split each line into the struct's members with `sscanf()`:

```c
if (sscanf(line, "%31[^,],%d,%d", loaded.topic, &loaded.score, &loaded.out_of) == 3) {
```

`%31[^,]` reads text up to the next comma: `[^,]` means "any character except a comma", and `31` stops it overflowing the 32-character `topic` array (one space is left for the `'\0'`). The commas in the format must then match the commas in the line.

`sscanf()` returns how many values it converted, so anything other than 3 means the line is not in the expected format.

## Complete example

This program saves two results, then reads them back:

```c
#include <stdio.h>

#define TOPIC_LENGTH 32
#define LINE_LENGTH 100

typedef struct {
    char topic[TOPIC_LENGTH];
    int score;
    int out_of;
} quiz_result_t;

int main(void)
{
    quiz_result_t results[2] = {
        { .topic = "Arrays", .score = 8, .out_of = 10 },
        { .topic = "Pointers", .score = 7, .out_of = 10 },
    };

    // Save: one result per line
    FILE *file = fopen("results.csv", "w");

    if (file == NULL) {
        printf("Could not open results.csv for writing\n");
        return 1;
    }
    for (int i = 0; i < 2; i++) {
        fprintf(file, "%s,%d,%d\n", results[i].topic, results[i].score, results[i].out_of);
    }
    fclose(file);

    // Load: read each line back into a struct
    file = fopen("results.csv", "r");

    if (file == NULL) {
        printf("Could not open results.csv for reading\n");
        return 1;
    }

    char line[LINE_LENGTH];
    quiz_result_t loaded;

    while (fgets(line, sizeof(line), file) != NULL) {
        if (sscanf(line, "%31[^,],%d,%d", loaded.topic, &loaded.score, &loaded.out_of) == 3) {
            printf("%-10s %2d / %d\n", loaded.topic, loaded.score, loaded.out_of);
        } else {
            printf("Skipping bad line: %s", line);
        }
    }
    fclose(file);
    return 0;
}
```

This prints:

```text
Arrays      8 / 10
Pointers    7 / 10
```

and leaves `results.csv` containing the two lines shown at the top of the page.

## Things to plan for

A real program loads the records into an array when it starts and saves them again later. Before you write that part, decide how your program will handle each of these:

- **the first run:** there is no file yet, so `fopen()` returns `NULL`. Is that an error, or does the program start with no records?
- **a full array:** the array has a fixed size. What happens when the file has more lines than that, or the user adds one too many?
- **bad lines:** a line edited by hand may not fit the format. Skipping it is reasonable, but say so: if the program then saves, the skipped line is gone from the file for good
- **commas in the data:** a topic such as `Pointers, part 2` no longer matches the format when it is read back, so the record is lost
- **when to save:** on exit is simplest, but a crash loses every change since the program started. After every change is safer
- **opening with `"w"` too early:** it empties the file straight away, so only open for writing when you are about to save

Keep the code that turns one line into a struct in its own function. It then takes a string and gives back a result, so you can [unit test]({% link docs/debugging/testing.md %}) it with valid and invalid lines without any files.

## Summary

The main points to remember are:

- store one record per line, with the fields separated by commas
- save with `fprintf()` and `"w"`; load with `fgets()` and `sscanf()`
- check what `sscanf()` returns before using the values
- decide how your program handles a missing file, a full array and bad lines
