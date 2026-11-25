---
title: Fancy terminal output
parent: Command Line Basics
nav_order: 5
layout: default
---

# Fancy terminal output

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

If you have searched for how to add a GUI to a C program, you have probably found GTK or gnuplot, read the installation instructions and had a small panic attack. The good news is that the terminal alone can do a lot. Think of it as painting with a limited palette: tidy tables, colours, text plots and simple animations. Everything on this page is standard C and works in the Codespace terminal.

These are good ways to extend your Unit 2 project once the core features work. They are not a substitute for them.

## Tidy tables

`printf()` can pad each value to a fixed width, so columns line up:

- `%10s` and `%10d` pad to 10 characters, lined up on the right
- `%-12s` pads to 12 characters, lined up on the left
- `%10.2f` pads to 10 characters with 2 decimal places

```c
#include <stdio.h>

int main(void)
{
    const char *names[] = { "Temperature", "Humidity", "Light" };
    const char *units[] = { "C", "%", "lux" };
    const float readings[] = { 21.456f, 48.2f, 1250.0f };

    printf("%-12s %10s %-4s\n", "Sensor", "Reading", "Unit");
    printf("%-12s %10s %-4s\n", "------", "-------", "----");

    for (int i = 0; i < 3; i++) {
        printf("%-12s %10.2f %-4s\n", names[i], readings[i], units[i]);
    }

    return 0;
}
```

This produces:

```text
Sensor          Reading Unit
------          ------- ----
Temperature       21.46 C
Humidity          48.20 %
Light           1250.00 lux
```

Names usually read best lined up on the left, and numbers on the right, so the decimal points line up.

## Colours and styles

Most terminals understand **ANSI escape codes**: special character sequences that change the colour or style of the text after them instead of being printed. Each one starts with the escape character, written `\x1b` in C, followed by `[`.

| Code | Effect |
| --- | --- |
| `\x1b[0m` | reset to normal text |
| `\x1b[1m` | bold |
| `\x1b[4m` | underline |
| `\x1b[31m` | red text |
| `\x1b[32m` | green text |
| `\x1b[33m` | yellow text |
| `\x1b[34m` | blue text |
| `\x1b[35m` | magenta text |
| `\x1b[36m` | cyan text |

You can put a code straight into the text. This prints `PASS` in green, then resets the colour:

```c
printf("\x1b[32mPASS\x1b[0m\n");
```

That works, but it is hard to read and easy to get wrong. It is easier to give the codes names with `#define`, so your code says what it means. C joins string literals that are next to each other, so `COLOUR_GREEN "PASS" STYLE_RESET` becomes the same string as above:

```c
#include <stdio.h>

#define COLOUR_RED    "\x1b[31m"
#define COLOUR_GREEN  "\x1b[32m"
#define COLOUR_YELLOW "\x1b[33m"
#define STYLE_BOLD    "\x1b[1m"
#define STYLE_RESET   "\x1b[0m"

int main(void)
{
    printf(STYLE_BOLD "d[o_0]b ELEC2645 Bot test report" STYLE_RESET "\n");
    printf("square positive integer: " COLOUR_GREEN "PASS" STYLE_RESET "\n");
    printf("cube negative integer:   " COLOUR_GREEN "PASS" STYLE_RESET "\n");
    printf("division by zero:        " COLOUR_RED "FAIL" STYLE_RESET "\n");
    printf(COLOUR_YELLOW "Warning: 1 test failed." STYLE_RESET "\n");
    return 0;
}
```

This produces:

<div class="language-text highlighter-rouge"><div class="highlight"><pre class="highlight"><code><b>d[o_0]b ELEC2645 Bot test report</b>
square positive integer: <span style="color: #2e7d32;">PASS</span>
cube negative integer:   <span style="color: #2e7d32;">PASS</span>
division by zero:        <span style="color: #c62828;">FAIL</span>
<span style="color: #b58900;">Warning: 1 test failed.</span>
</code></pre></div></div>

Always end coloured text with `STYLE_RESET`. Otherwise everything after it, including the terminal prompt when your program ends, stays that colour.

Colour should add to the meaning, not carry it on its own. Some people cannot tell red from green, which is why the example still prints `PASS` and `FAIL`.

## Bar charts

A bar is a row of characters whose length represents a value. Scale the values so that the largest one fills the width you have:

```c
#include <stdio.h>

#define MAX_BAR_WIDTH 40

void print_bar(int length);

int main(void)
{
    const char *days[] = { "Mon", "Tue", "Wed", "Thu", "Fri" };
    const int snacks[] = { 3, 7, 2, 12, 5 };
    const int count = 5;
    int largest = snacks[0];

    for (int i = 1; i < count; i++) {
        if (snacks[i] > largest) {
            largest = snacks[i];
        }
    }

    printf("Snacks eaten this week\n\n");

    for (int i = 0; i < count; i++) {
        int length = snacks[i] * MAX_BAR_WIDTH / largest;  // scale to fit

        printf("%s |", days[i]);
        print_bar(length);
        printf(" %d\n", snacks[i]);
    }

    return 0;
}

void print_bar(int length)
{
    for (int i = 0; i < length; i++) {
        putchar('#');
    }
}
```

This produces:

```text
Snacks eaten this week

Mon |########## 3
Tue |####################### 7
Wed |###### 2
Thu |######################################## 12
Fri |################ 5
```

The scaling multiplies before it divides. `snacks[i] / largest * MAX_BAR_WIDTH` would use integer division first, so every bar except the largest would have length 0.

## Plotting a function

To plot a graph, turn it on its side: each line of output is one point in time, and the position of the `*` across the line shows the value. This plots one cycle of a sine wave:

```c
#include <stdio.h>
#include <math.h>

#define PI 3.14159265358979
#define PLOT_WIDTH 40
#define STEPS 16

int main(void)
{
    for (int step = 0; step < STEPS; step++) {
        double angle = 2.0 * PI * step / STEPS;
        double value = sin(angle);

        // Map -1.0 to 1.0 onto columns 0 to PLOT_WIDTH
        int column = (int)lround((value + 1.0) * PLOT_WIDTH / 2.0);

        printf("%6.2f |", value);
        for (int i = 0; i < column; i++) {
            putchar(' ');
        }
        printf("*\n");
    }

    return 0;
}
```

This produces:

```text
  0.00 |                    *
  0.38 |                            *
  0.71 |                                  *
  0.92 |                                      *
  1.00 |                                        *
  0.92 |                                      *
  0.71 |                                  *
  0.38 |                            *
  0.00 |                    *
 -0.38 |            *
 -0.71 |      *
 -0.92 |  *
 -1.00 |*
 -0.92 |  *
 -0.71 |      *
 -0.38 |            *
```

`sin()` and `lround()` (round to the nearest whole number) come from the maths library. Programs that use `math.h` must be linked with `-lm`, as explained in [compiler flags]({% link docs/projects/compiler_flags.md %}). In a Unit 2 Makefile, add it to the end of the recipe line:

```make
application.out: $(APP_SOURCES) $(APP_HEADERS)
	$(CC) $(CFLAGS) $(APP_SOURCES) -o $@ -lm
```

If the maths functions are in a file your tests use, add `-lm` to the test recipe too.

The same idea plots real data: replace `sin(angle)` with values from an array, such as sensor readings.

## Animation

An animation is a loop that draws a frame, waits a moment, and draws the next one.

### Waiting

`thrd_sleep()` pauses the program. It is part of standard C11, in `threads.h`, and takes the time to wait as a `struct timespec` of seconds and nanoseconds:

```c
const struct timespec delay = { .tv_sec = 0, .tv_nsec = 100000000 };  // 0.1 s

thrd_sleep(&delay, NULL);
```

Not every compiler provides `threads.h` (Apple's compiler on macOS does not). The Codespace has it. For other compilers, see the next section.

### Waiting without `threads.h`

Every C compiler has `clock()` in `time.h`, so a program can also wait by checking the clock over and over until enough time has passed:

```c
#include <time.h>

// Waits by checking the clock until ms milliseconds have passed.
void wait_ms(long ms)
{
    clock_t start = clock();

    while ((clock() - start) * 1000 / CLOCKS_PER_SEC < ms) {
        // keep checking
    }
}
```

`clock()` counts in steps of `1 / CLOCKS_PER_SEC` seconds, so multiplying by 1000 and dividing by `CLOCKS_PER_SEC` gives milliseconds. It measures the processor time your program has used, not the time on a wall clock, so on a busy computer the wait can come out a little long (in one test, the progress bar below took about 2.5 s instead of 2 s). To use it in the examples below, replace `#include <threads.h>` with `#include <time.h>`, add `wait_ms()`, and replace the `delay` variable and `thrd_sleep(&delay, NULL);` with the same wait in milliseconds, such as `wait_ms(100);` for 0.1 s.

This is called a **busy wait**: the program keeps the processor busy for the whole wait, checking the time. `thrd_sleep()` lets the processor do other work instead, so use it where you can.

You will meet the same idea in Unit 3. On the STM32, [`HAL_Delay()`]({% link docs/stm32/STM32_HAL.md %}#hal_delay) is a busy wait: it reads the millisecond counter `HAL_GetTick()` in a loop until enough time has passed. Your program does nothing else while it waits, which is why games on the board often use `HAL_GetTick()` to time each frame instead.

### A progress bar

`\r` (carriage return) moves the cursor back to the start of the line without starting a new one, so the next `printf()` draws over the old line:

```c
#include <stdio.h>
#include <threads.h>

#define BAR_WIDTH 20

int main(void)
{
    const struct timespec delay = { .tv_sec = 0, .tv_nsec = 100000000 };  // 0.1 s

    for (int done = 0; done <= BAR_WIDTH; done++) {
        printf("\rCompiling [");
        for (int i = 0; i < BAR_WIDTH; i++) {
            putchar(i < done ? '#' : ' ');
        }
        printf("] %3d%%", done * 100 / BAR_WIDTH);
        fflush(stdout);  // show the line now, without waiting for '\n'
        thrd_sleep(&delay, NULL);
    }

    printf("\nBuild finished with 0 warnings. Probably.\n");
    return 0;
}
```

The bar fills up over about two seconds and ends as:

```text
Compiling [####################] 100%
Build finished with 0 warnings. Probably.
```

When output goes to a terminal, C normally holds each line back until it reaches a `'\n'`. `fflush(stdout)` sends it straight away. Without it, nothing appears until the loop ends.

### Redrawing the screen

For frames taller than one line, draw the frame, then move the cursor back up to its first line and draw the next frame over the top. Hiding the cursor stops it flickering:

| Code | Effect |
| --- | --- |
| `\x1b[3A` | move the cursor up 3 lines (`\x1b[nA` moves it up `n` lines) |
| `\x1b[?25l` | hide the cursor |
| `\x1b[?25h` | show the cursor again |

```c
#include <stdio.h>
#include <threads.h>

#define CURSOR_UP_3  "\x1b[3A"
#define HIDE_CURSOR  "\x1b[?25l"
#define SHOW_CURSOR  "\x1b[?25h"
#define TRACK_LENGTH 30

void draw_frame(int position, int frame);

int main(void)
{
    const struct timespec delay = { .tv_sec = 0, .tv_nsec = 150000000 };  // 0.15 s

    printf(HIDE_CURSOR);

    for (int position = 0; position < TRACK_LENGTH; position++) {
        if (position > 0) {
            printf(CURSOR_UP_3);  // back to the top of the last frame
        }
        draw_frame(position, position % 2);
        thrd_sleep(&delay, NULL);
    }

    printf(SHOW_CURSOR "ELEC2645 Bot has left the building.\n");
    return 0;
}

// Draws the bot at the given column. frame picks one of two poses,
// so the bot appears to walk.
void draw_frame(int position, int frame)
{
    const char *heads[] = { "d[o_0]b", "q[0_o]p" };
    const char *legs[] = { "  / \\  ", "  | |  " };

    printf("%*s%s\n", position, "", heads[frame]);
    printf("%*s%s\n", position, "", legs[frame]);

    for (int i = 0; i < TRACK_LENGTH + 7; i++) {
        putchar('=');
    }
    printf("\n");
    fflush(stdout);
}
```

The bot walks from left to right along the track, switching between two poses on each step. Halfway along, a frame looks like this:

```text
               q[0_o]p
                 | |
=====================================
```

Each frame is three lines, so before drawing the next one the program moves the cursor up three lines. The first frame has nothing to draw over, so it skips that step. The new frame must cover every character of the old one: the spaces before the bot cover where it was, and the bot only moves right.

`%*s` takes the width from the argument list, so `printf("%*s", position, "")` prints `position` spaces. `"  / \\  "` needs `\\` to print one backslash.

You may find examples online that clear the whole screen with `\x1b[2J` before each frame. In the Codespace terminal that can leave the top line of the frame missing, so draw over the old frame instead.

The loop in `main()` (draw, wait, repeat) is the same shape as a game loop. In Units 3 and 4 the same pattern draws frames on the LCD.

## Timestamps for log files

A log is much more useful if each line says when it happened. `time.h` gives you the current date and time, and `strftime()` formats it, a bit like `printf()` for dates:

```c
#include <stdio.h>
#include <time.h>

int main(void)
{
    time_t now = time(NULL);
    char stamp[20];

    strftime(stamp, sizeof(stamp), "%Y-%m-%d %H:%M:%S", localtime(&now));
    printf("[%s] Snack supplies running low.\n", stamp);
    return 0;
}
```

This produces something like:

```text
[2026-11-23 10:46:26] Snack supplies running low.
```

`%Y-%m-%d %H:%M:%S` is year, month, day, hours, minutes and seconds. The array needs 20 characters: 19 for the date and time plus one for the `'\0'`.

The Codespace clock runs on UTC, so in summer the time is an hour behind UK time. To write the same line to a log file, use `fprintf()` with a file pointer instead of `printf()`; see [Logs and imported data]({% link docs/files/file_logs.md %}).

## Common mistakes

### Forgetting to reset

If the program ends, or is stopped with `Ctrl+C`, while a colour is on or the cursor is hidden, the terminal stays that way. Type `reset` and press Enter to put it back.

### Colour codes in the functions you test

A [unit test]({% link docs/debugging/testing.md %}) that compares strings fails if the string contains colour codes. Keep colours in the interface code (`main.c`) and keep the functions in `funcs.c` plain, so they can be [tested directly]({% link docs/projects/software_projects.md %}#splitting-a-menu-program).

### Colour codes in files

Escape codes are only special to a terminal. Written to a file, or shown somewhere that does not understand them, they appear as text such as `[32mPASS[0m`.

### `M_PI` is not defined

Many examples online use `M_PI` from `math.h`. It is not part of standard C, so with `-std=c11` it gives:

```text
error: 'M_PI' undeclared (first use in this function)
```

Define your own constant, as the plotting example does.

### Forgetting `-lm`

Without `-lm` the program compiles, but linking fails:

```text
undefined reference to `sin'
```

## Going further

- Save your results: a high-score table, a log of readings or your program's settings can be written to a file and read back next time. See [files]({% link docs/files/files.md %}).
- Combine the ideas: a menu with a coloured title, a table of results and a bar chart of them is a good-looking Unit 2 project.

The main points to remember are:

- use `printf()` field widths (`%-12s`, `%10.2f`) to line up columns
- ANSI escape codes change colours and styles; always reset afterwards
- give escape codes readable names with `#define`
- scale values before drawing bars or plots, multiplying before dividing
- programs using `math.h` need `-lm`, and `M_PI` is not standard C
- animate with a loop: draw a frame, `fflush(stdout)`, wait with `thrd_sleep()`, repeat
- without `threads.h`, a busy wait with `clock()` works but keeps the processor busy
- use `time()`, `localtime()` and `strftime()` to timestamp log lines
