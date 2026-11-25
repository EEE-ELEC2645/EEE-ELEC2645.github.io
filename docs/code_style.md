---
title: Code styling
parent: Tools
nav_order: 4
layout: default
---

# Code style in C

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Eagleson's Law: "Any code of your own that you haven't looked at for six or more months might as well have been written by someone else."

Writing good code is not just about telling the computer what to do, it is communicating to __other people__ what you have told the computer to do!

Use this guide to help you write consistent and readable code. The examples on this website and the code in the lab repositories follow it.

## Why style matters

Code is read far more often than it is written: by your teammates, by the person marking your work, and by you in six months' time. When everyone in a team uses the same style, they can read each other's code without tripping over the layout, and mistakes stand out.

Professional programmers usually follow a style guide chosen by their employer or project. Given the age of the `C` programming language there are many different ones. For example NASA have their own guidelines to keep code understandable and testable, [detailed in this journal article](https://ieeexplore.ieee.org/document/1642624) and known as [the Power of Ten](https://en.wikipedia.org/wiki/The_Power_of_10:_Rules_for_Developing_Safety-Critical_Code), which are summarised [in this video](https://youtu.be/GWYhtksrmhE?si=5YLFxiuVCObyzDX4).

Our brace style comes from [the C Bible](https://en.wikipedia.org/wiki/The_C_Programming_Language), often called K&R after its authors. The other conventions are common ones, found in guides such as [Google's C++ Guide](https://google.github.io/styleguide/cppguide.html).

The most important rule is to be consistent. If you are working in a file or project that already has a style, follow it.

---

## End of lines and files

Do __not__ put trailing whitespace at the end of lines. The final character in a source file should be a newline after the closing `}` (no extra blank lines at the end).

__Good__

```c
#include <stdio.h>

int main(void)
{
    printf("Hello, C!\n");
    return 0;
}
```

__Bad__ (trailing spaces shown as `·`, stray blank lines at end)

```c
#include <stdio.h>··
int main(void)··
{··
    printf("Hello, C!\n");··
    return 0;··
}··

```

---

## Tabs and indentation

Use __spaces__, not tabs. Use __4 spaces__ per indent level. This is the source of many an argument online!

__Good__

```c
int clamp(int x, int lo, int hi)
{
    if (x < lo) {
        return lo;
    } else if (x > hi) {
        return hi;
    }
    return x;
}
```

__Bad__ (tabs and inconsistent width)

```c
int clamp(int x, int lo, int hi)
{
\tif (x < lo) {
\t\treturn lo;
\t} else if (x > hi) {
\t\treturn hi;
\t}
\treturn x;
}
```

VS Code inserts spaces when you press Tab, and shows the indent size in the status bar at the bottom of the window ("Spaces: 4").

The one exception is a Makefile: each recipe line must start with a real tab character. See [Makefiles]({% link docs/projects/makefiles.md %}).

---

## Braces

### Where braces go

- A function's opening brace goes on its own line.
- For `if`, `else`, `for`, `while`, `do` and `switch`, the opening brace goes on the same line, after one space.
- `else` sits between the braces: `} else {`.
- `struct`, `enum` and initialiser braces also go on the same line: `typedef struct {`.

__Good__

```c
int count_positive(const int values[], int count)
{
    int positive = 0;

    for (int i = 0; i < count; i++) {
        if (values[i] > 0) {
            positive++;
        } else {
            printf("Skipping %d\n", values[i]);
        }
    }
    return positive;
}
```

__Bad__ (every brace placed differently)

```c
int count_positive(const int values[], int count) {
    int positive = 0;

    for (int i = 0; i < count; i++)
    {
        if (values[i] > 0) {
            positive++; }
        else
        {
            printf("Skipping %d\n", values[i]);
        }
    }
    return positive;
}
```

You will see other brace styles in other code. CubeMX, which generates STM32 code later in the module, puts every brace on its own line, and some lecture slides do too. Both are fine; what matters is that each file is consistent.

### Always use braces

C lets you leave out the braces when an `if` or loop body is a single statement, and you will see code that does this. Sometimes it is fine. But clarity always wins over saving a line or two, so in this module we always use braces.

Here is why. A second line is added to the body later, indented as if it belongs to the `if`:

```c
int temperature = 22;
bool fan_on = false;

if (temperature > 30)
    printf("Too hot\n");
    fan_on = true;

printf("Fan on: %d\n", fan_on);
```

This prints:

```text
Fan on: 1
```

The fan turns on at 22 degrees. Without braces, only the first line belongs to the `if`; `fan_on = true;` runs every time, whatever the indentation suggests. `-Wall` does warn about it:

```text
fan.c:9:5: warning: this ‘if’ clause does not guard... [-Wmisleading-indentation]
    9 |     if (temperature > 30)
      |     ^~
fan.c:11:9: note: ...this statement, but the latter is misleadingly indented as if it were guarded by the ‘if’
   11 |         fan_on = true;
      |         ^~~~~~
```

With braces, the problem cannot happen:

```c
if (temperature > 30) {
    printf("Too hot\n");
    fan_on = true;
}
```

For the same reason, do not write a whole function on one line, even a short one.

__Good__

```c
int product(int a, int b)
{
    return a * b;
}
```

__Bad__

```c
int product(int a, int b) { return a * b; }
```

---

## Horizontal whitespace

Use spaces to make expressions readable and consistent.

### Around operators

__Good__

```c
int sum = a + b * 4 + 5;
```

__Bad__

```c
int sum=a+b*4+5;
```

### In expressions and after commas

__Good__

```c
int c = sum * 4 + 5;
int rgb[3] = { 255, 127, 0 };
```

__Bad__

```c
int c=sum*4+5;
int rgb[3]={255,127,0};
```

### Parentheses and semicolons

- __No__ spaces just inside parentheses.
- __No__ space before semicolons.
- For arrays, __no__ space before `[`.

__Good__

```c
for (int i = 0; i < 10; i++) {
    // ...
}

int main(void)
{
    int array[5] = { 1, 2, 3, 4, 5 };
    return 0;
}
```

__Bad__

```c
for(int i=0;i<10;i++){
    // ...
}

int main ( void )
{
    int array [ 5 ] = {1,2,3,4,5};
    return 0 ;
}
```

### Conditionals and `else`

- Space after keywords (`if`, `while`, `for`).
- Space around `else`: `} else {`.

__Good__

```c
if (n < 0) {
    printf("Negative\n");
} else {
    printf("Non-negative\n");
}
```

__Bad__

```c
if(n<0){
    printf("Negative\n");
}else{
    printf("Non-negative\n");
}
```

### `for` loop semicolons

Put a __space after each semicolon__ inside `for` headers.

__Good__

```c
for (int i = 0; i < 10; i++) {
    // ...
}
```

__Bad__

```c
for (int i=0;i<10;i++){
    // ...
}
```

### Pointers

Attach the `*` to the variable name, not the type:

__Good__

```c
int *pointer;
const char *name;
```

__Bad__

```c
int* pointer;
```

`int* a, b;` looks like it declares two pointers, but only `a` is a pointer; `b` is an ordinary `int`. Writing `int *a` makes this clearer.

### Inline comments

- Space after `//`.
- Leave __two spaces__ before the start of an inline comment following code.

__Good__

```c
int top_score = 0;  // highest score seen so far
```

__Bad__

```c
int top_score=0;//highest score seen so far
```

---

## Vertical whitespace

Minimise blank lines. Use __one blank line__ between functions. Don't start or end a function with a blank line. Inside a function, use blank lines sparingly to separate logical sections.

__Too much vertical whitespace__

```c
int calc_sum(int a, int b)
{

    int sum = a + b;

    return sum;
}


int calc_product(int a, int b)
{

    int product = a * b;

    return product;
}
```

__Better__

```c
int calc_sum(int a, int b)
{
    int sum = a + b;
    return sum;
}

int calc_product(int a, int b)
{
    int product = a * b;
    return product;
}
```

---

## Examples

Examples of properly structured loops and conditionals.

__`for` loop__

```c
int sum = 0;
for (int i = 0; i < 10; i++) {
    sum += i;
}
```

__`while` loop__

```c
#include <stdio.h>

void print_to_9(void)
{
    int i = 0;
    while (i < 10) {
        printf("%d\n", i);
        i++;
    }
}
```

__`if` / `else if` chain__

```c
#include <stdio.h>

void print_classification(int grade)
{
    if (grade > 70) {
        printf("Class I\n");
    } else if (grade > 60) {
        printf("Class II.i\n");
    } else if (grade > 50) {
        printf("Class II.ii\n");
    } else if (grade > 40) {
        printf("Class III\n");
    } else {
        printf("Fail\n");
    }
}
```

A long `if` / `else if` chain can include __judicious__ blank lines to group related cases:

```c
if (grade > 70) {
    printf("Class I\n");

} else if (grade > 60) {
    printf("Class II.i\n");

} else if (grade > 50) {
    printf("Class II.ii\n");

} else if (grade > 40) {
    printf("Class III\n");

} else {
    printf("Fail\n");
}
```

---

## Naming

Consistency is key. Prefer __descriptive__, __lowercase_with_underscores__ for variables, functions, and files.

### Variables

__Good__

```c
double price_of_item = 0.99;
int sensor_count = 3;
```

__Bad__

```c
double p = 0.99;
int SensorCount = 3;
```

### Functions

Use verbs and be descriptive.

__Good__

```c
int calc_average_score(const int *scores, int n);
void led_set_brightness(int value);
```

__Bad__

```c
int avg(int*, int);
void set(int v);
```

### Structs and typedefs (C has no classes)

C does not have classes. Use `struct`s for related data and consider a `typedef` for readability. Prefer __no leading underscores__ (some leading-underscore names are reserved in C).

__Good__

```c
typedef struct {
    int x;
    int y;
} point_t;

void point_move(point_t *p, int dx, int dy);
```

__Bad__

```c
struct _Point {  // avoid leading underscore
    int X;       // avoid capitalised field names
    int Y;
};
```

### Internal (module-private) identifiers

C has no namespaces. Use a __module prefix__ and `static` for internal linkage. See [Static functions]({% link docs/functions/static_functions.md %}).

```c
// In file "net_buf.c" (module prefix: net_buf_)
static size_t net_buf_clamp(size_t n, size_t hi)
{
    return n > hi ? hi : n;
}
```

### Macros

Use __UPPER_CASE__ for macros and constants. Prefer `static inline` functions over function-like macros when possible.

```c
#define MAX_SENSORS 8

static inline int square(int x)
{
    return x * x;
}
```

### Filenames

Lowercase with underscores. Use `.c` for source and `.h` for headers.

```text
my_source_file.c
my_header_file.h
```

---

## Comments

__Code tells you how, comments tell you why__

Prefer __C99 `//` comments__ over `/* ... */` block comments for most cases. Use a space after `//`.

__Good__

```c
// This computes the checksum over a buffer.
```

__Bad__

```c
/*This computes the checksum over a buffer.*/
```

### File header comments

Start each file with a short header comment saying what the project is, what this file does and who wrote it. In a team project, the author line tells your teammates who to ask about a file.

```c
// ELEC2645 Unit 2 project - Plant watering logger
//
// funcs.c contains the calculations used by the menu: average moisture,
// watering time and the low-water warning.
//
// Author: Your Name
```

Files supplied in the lab repositories have the same header without an author line. You do not need a date: Git records when every change was made.

### Comment the non-obvious

Use __descriptive names__ to avoid pointless comments.

__Pointless__

```c
int z = 0;  // Variable to store top score
```

__Better__

```c
int top_score = 0;
```

Comment __what's non-obvious__: complex logic, invariants, preconditions, and side effects. Place short comments above a block, and end-of-line comments where it truly clarifies.

__Before (vague)__

```c
// check if all lives lost and if so, call game over function
if (lives_left == 0) {
    game_over();
}
```

__After (self-documenting + concise)__

```c
if (lives_left == 0) {  // no retries remaining
    game_over();
}
```

---

## Functions

Break code into small, focused functions. This improves testing, reuse, and readability. There's no hard line, but if a function grows beyond __about 20 to 40 lines__, consider splitting it. The [NASA Power of Ten Guidelines](https://en.wikipedia.org/wiki/The_Power_of_10:_Rules_for_Developing_Safety-Critical_Code) suggest limiting the code within a function to a single printed A4 page.

No blank lines at the __start__ or __end__ of a function. Use descriptive names; a general rule of thumb is that the name should include a verb, to say what the code _does_.

### Returning early

It is fine to `return` early, for example to reject bad input at the top of a function (a "guard clause") before doing the real work. This keeps the main part of the function from being buried inside an `if`.

Try not to scatter `return` statements all through a long function, though. If it is hard to see where a function stops and what it returns, split it into smaller functions or collect the result in a variable and return it once at the end.

## Complete example

```c
#include <stdio.h>
#include <stdbool.h>

bool in_range(int x, int lo, int hi)
{
    if (x < lo) {
        return false;
    }
    if (x > hi) {
        return false;
    }
    return true;
}

int sum_to_n(int n)
{
    if (n < 0) {
        return -1;  // guard clause: reject bad input first
    }

    int sum = 0;
    for (int i = 1; i <= n; i++) {
        sum += i;
    }
    return sum;
}

int main(void)
{
    printf("in_range(5, 0, 10) = %d\n", in_range(5, 0, 10));
    printf("in_range(12, 0, 10) = %d\n", in_range(12, 0, 10));
    printf("sum_to_n(5) = %d\n", sum_to_n(5));
    printf("sum_to_n(-3) = %d\n", sum_to_n(-3));
    return 0;
}
```

This prints:

```text
in_range(5, 0, 10) = 1
in_range(12, 0, 10) = 0
sum_to_n(5) = 15
sum_to_n(-3) = -1
```

---

## Summary

The main points to remember are:

- 4 spaces per indent level, never tabs (except in Makefiles)
- function braces on their own line; `if`, `for`, `while` and `switch` braces on the same line
- always use braces, even for one-line bodies: clarity wins over saving a line
- spaces around operators, after commas and after keywords
- descriptive `lower_case_with_underscores` names, with functions named after what they do
- comments explain why, not what; every file starts with a header comment and an author line
- be consistent with the file or project you are working in
