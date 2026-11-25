---
title: Logs and imported data
parent: Files
nav_order: 3
layout: default
---

# Logs and imported data

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Two more jobs for files: keeping a history of what a program did, and reading data that came from somewhere else.

## Appending to a log file

A log file is a history: each event adds a line to the end, and nothing already there changes. Opening the file with `"a"` (append) does exactly that, and creates the file if it does not exist yet:

```c
FILE *file = fopen("events.log", "a");
```

A log is much more useful if each line says when it happened. The complete example below adds a timestamp with `strftime()`, as in [Fancy terminal output]({% link docs/menus/fancy_output.md %}#timestamps-for-log-files).

It opens and closes the file for every line. That is a little slower than keeping it open, but each line is safely written as soon as it happens, so the log still shows what happened if the program crashes. That is usually when you want the log most.

## Complete example

```c
#include <stdio.h>
#include <time.h>

#define LOG_FILE "events.log"

int log_event(const char *message);

int main(void)
{
    log_event("Program started");
    log_event("Quiz finished: 7 out of 10");
    return 0;
}

// Adds one timestamped line to the end of the log file.
// Returns 1 on success, 0 if the file could not be opened.
int log_event(const char *message)
{
    FILE *file = fopen(LOG_FILE, "a");

    if (file == NULL) {
        return 0;
    }

    time_t now = time(NULL);
    char stamp[20];

    strftime(stamp, sizeof(stamp), "%Y-%m-%d %H:%M:%S", localtime(&now));
    fprintf(file, "[%s] %s\n", stamp, message);
    fclose(file);
    return 1;
}
```

After running the program twice, `events.log` contains something like:

```text
[2026-11-24 22:32:02] Program started
[2026-11-24 22:32:02] Quiz finished: 7 out of 10
[2026-11-24 22:32:02] Program started
[2026-11-24 22:32:02] Quiz finished: 7 out of 10
```

The second run added its lines after the first run's. With `"w"`, each run would have wiped the history.

## Files made somewhere else

Most of the time you read files your own program wrote, so you know exactly what is in them. A file from a spreadsheet, a data logger or another program can surprise you. Watch out for:

- **a header line** naming the columns, which will not parse as data
- **blank lines**, often at the end
- **missing or bad values**, such as `err` where a sensor failed
- **Windows line endings**

The last one catches nearly everyone once. Files made on Windows, including most CSV files saved from Excel, end every line with two characters, `"\r\n"`, not just `"\n"`. If you remove only the newline, as on the [User input]({% link docs/menus/user_input.md %}) page, an invisible `'\r'` is left at the end of every line. A last field of `"lab"` is really `"lab\r"`, so `strcmp()` never matches, and the program quietly finds nothing.

Remove both characters instead, which works for files from any system:

```c
line[strcspn(line, "\r\n")] = '\0';
```

To check a file, VS Code shows `CRLF` or `LF` in the status bar at the bottom of the window, and in the terminal `file` reports it:

```text
readings.csv: ASCII text, with CRLF line terminators
```

## Common mistakes

### Opening a log with `"w"`

`"w"` empties the file every time the program starts, so the log only ever holds the latest run. Logs use `"a"`.

### Committing log files

A log changes on every run, so committing it fills your Git history with noise and causes conflicts in a shared repository. Add a line such as `*.log` to the repository's `.gitignore`.

## Summary

The main points to remember are:

- open logs with `"a"` so each run adds to the history
- close the log after each line so a crash loses nothing
- files made elsewhere may have headers, blank lines, bad values and Windows line endings
- `strcspn(line, "\r\n")` removes either kind of line ending
