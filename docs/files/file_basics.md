---
title: Using files
parent: Files
nav_order: 1
layout: default
---


---

## C file handling (the essentials)

### 1) General Pattern

- A `FILE *` is a **handle** the C library gives you so you can read from or write to a file.
- Typical pattern is: **open -> use -> close**.

  ```c
  FILE *f = fopen("path.txt", "r");  // open
  /* read/write using f */
  fclose(f);                         // close
  ```

- If `fopen` fails (e.g., file missing or no permission), it returns **`NULL`**. Always check using something like `if(!f) {error}`

### 2) Common modes you’ll use

- `"r"`  – open for **reading** (must already exist).
- `"w"`  – open for **writing** (creates or **truncates** existing).
- `"a"`  – **append** to end (creates if missing).
- Add `"b"` for binary (e.g., `"rb"`, `"wb"`); on Linux/macOS it’s the same as text; on Windows it matters.

### 3) Reading lines

- `fgets(buf, buf_size, file)` reads **one line** (or up to `buf_size-1` chars), including the newline if it fits.
- It returns `NULL` on **EOF** or **error**, which makes it perfect for a `while` loop.

### 4) Writing

- `fprintf(file, "format", ...)` works like `printf`, but the **first argument is the file**.
- Great for CSV or logs.

### 5) Always close

- `fclose(file)` flushes buffers and releases the handle. Do this for **every** file you open, can cause problems if not done correctly.

---

## Step‑by‑step example

> **Goal:** Read numbers from `numbers.txt` (with a header line), compute **average**, and write results to `average.txt` in CSV.

```c
#include <stdio.h>  // fopen, fgets, fprintf, fclose, printf
#include <stdlib.h> // atof

#define MAX_LINE    100   // max length of one input line
#define MAX_NUMBERS 100   // max numbers we’ll store

int main(void) {
    // 1) Open files
    FILE *input  = fopen("numbers.txt", "r");  // read text
    FILE *output = fopen("stats.txt",   "w");  // write text (truncate/create)

    // 2) Check they opened OK
    if (!input || !output) {
        printf("Error opening file.\n");
        return 1;
    }

    // 3) Prepare storage
    float numbers[MAX_NUMBERS];  // fixed-size array for numbers
    char  line[MAX_LINE];        // line buffer for fgets

    // 4) Skip the header line (we won’t use it)
    fgets(line, MAX_LINE, input);

    // 5) Read number lines until EOF or capacity reached
    int count = 0;
    while (fgets(line, MAX_LINE, input) && count < MAX_NUMBERS) {
        numbers[count] = atof(line);  // convert text line → float
        count++;
    }

    // 6) Compute sum, min, max across what we read
    float sum = 0.0f;
    for (int i = 0; i < count; i++) {
        sum += numbers[i];

    }

    // 7) Compute average
    float average = sum / count;

    // 8) Write results as CSV
    fprintf(output, "statistic,value\n");
    fprintf(output, "average,%.2f\n", average);

    // 9) Close files
    fclose(input);
    fclose(output);
    return 0;
}
```

### Why each piece is there

- **Two `FILE*`s**: one for input, one for output—so you can read and write independently.
- **`fgets`**: safe, line‑oriented reading; it stops at newline or buffer limit and NUL‑terminates the string.
- **`atof`**: converts the line (text) into a `float`. This is a simple way to do this, there are nicer ways 
- **CSV output**: using `fprintf` so spreadsheet apps can read it directly.
- **`fclose`**: finishes the job cleanly and ensures data is flushed to disk.

---

## Compile & run


```bash
gcc -Wall -Wextra -Wpedantic stats.c -o stats.out
./stats.out
```

### Example files

**`numbers.txt` (input)**  
```
value
12.5
3.0
7.25
-1.5
10
```

**`stats.txt` (output)**
```
statistic,value
average,6.65
```

---
