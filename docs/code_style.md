---
title: Code Styling
nav_order: 97
layout: default
---

# Code Style in C

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Eagleson's Law : “Any code of your own that you haven’t looked at for six or more months might as well have been written by someone else.”

Writing good code is not just about telling the computer what to do, it is communicating to __other people__ what you have told the computer to do!

Use this guide to help you write consistent and readable code.

## Introduction

Code style is a hugely important aspect of coding which is often neglected by novices. Indeed, a
cursory glance at how code is written will often tell you if it has been written by a professional
or a novice.

Professional programmers stick to very strict code style guidelines and indeed, are often forced to
by their employers. Ensuring that all employees use the same coding style means that they can all
easily read each other’s code.

Given the age of the `C` programming language there are many different style guides. For example NASA have their own guidelines to keep code understandable and testable [detailed in this journal article](https://ieeexplore.ieee.org/document/1642624) aka [the Power of Ten](https://en.wikipedia.org/wiki/The_Power_of_10:_Rules_for_Developing_Safety-Critical_Code) which are summarised [in this video](https://youtu.be/GWYhtksrmhE?si=5YLFxiuVCObyzDX4)

We will follow the typical conventions, based on [the C Bible](https://en.wikipedia.org/wiki/The_C_Programming_Language) sometimes shortened to K&R after the authors, and then expanded into things like [Google's C++ Guide](https://google.github.io/styleguide/cppguide.html).

Following this clear, consistent __C__ style will make you into a better and more professional programmer.

---

## End of lines and files

Do __not__ put trailing whitespace at the end of lines. The final character in a source file should
be a newline after the closing `}` (no extra blank lines at the end).

__Good__

```c
#include <stdio.h>

int main(void) {
  printf("Hello, C!\n");
  return 0;
}
```


__Bad__ (trailing spaces shown as `·`, stray blank lines at end)

```c
#include <stdio.h>··
int main(void) {··
  printf("Hello, C!\n");··
  return 0;··
}··

```

---

## Tabs and indentation

Use __spaces__, not tabs. Use __2 spaces__ per indent level. This is the source of many an argument online!

__Good__

```c
int clamp(int x, int lo, int hi) {
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
int clamp(int x, int lo, int hi) {
\tif (x < lo) {
\t\treturn lo;
\t} else if (x > hi) {
\t\treturn hi;
\t}
\treturn x;
}
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

### Braces and parentheses

- Put __one space__ before an opening brace `{` (K&R).
- __No__ spaces just inside parentheses.
- __No__ space before semicolons.
- For arrays, __no__ space before `[`.

__Good__

```c
for (int i = 0; i < 10; i++) {
  /* ...Code... */
}

int main(void) {
  int array[5] = { 1, 2, 3, 4, 5 };
  return 0;
}
```

__Bad__

```c
for(int i=0;i<10;i++){
  /* ...Code... */
}

int main ( void ){
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
  /* ... */
}
```

__Bad__
```c
for (int i=0;i<10;i++){
  /* ... */
}
```

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

Minimise blank lines. Use __one blank line__ between functions. Don’t start or end a function with a
blank line. Inside a function, use blank lines sparingly to separate logical sections.

__Too much vertical whitespace__

```c
int calc_sum(int a, int b) {

  int sum = a + b;

  return sum;
}


int calc_product(int a, int b) {

  int product = a * b;

  return product;
}
```

__Better__

```c
int calc_sum(int a, int b) {
  int sum = a + b;
  return sum;
}

int calc_product(int a, int b) {
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

void print_to_9(void) {
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

void print_classification(int grade) {
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

Consistency is key. Prefer __descriptive__, __lowercase_with_underscores__ for variables, functions,
and files.

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

C does not have classes. Use `struct`s for related data and consider a `typedef` for readability.
Prefer __no leading underscores__ (some leading-underscore names are reserved in C).

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
  int X;         // avoid capitalised field names
  int Y;
};
```

### Internal (module-private) identifiers

C has no namespaces. Use a __module prefix__ and `static` for internal linkage.

```c
// In file "net_buf.c" (module prefix: net_buf_)
static size_t net_buf_clamp(size_t n, size_t hi) { return n > hi ? hi : n; }
```

### Macros

Use __UPPER_CASE__ for macros and constants. Prefer `static inline` functions over function-like
macros when possible.

```c
#define MAX_SENSORS 8

static inline int square(int x) {
  return x * x;
}
```

### Filenames

Lowercase with underscores. Use `.c` for source and `.h` for headers.

```c
my_source_file.c
my_header_file.h
```

---

## Comments

__Code Tells You How, Comments Tell You Why__

Prefer __C99 `//` comments__ over `/* ... */` block comments for most cases. Use a space after `//`.

__Good__
```c
// This computes the checksum over a buffer.
```

__Bad__
```c
/*This computes the checksum over a buffer.*/
```

Provide a brief, useful __file header__ comment at the top of each file.

```c
// Program: sum_primes.c
// Purpose: Calculate the sum of the first n prime numbers
// Author: Your Name
// Date: 2025-09-24
```

Use __descriptive names__ to avoid pointless comments.

__Pointless__
```c
int z = 0;  // Variable to store top score
```

__Better__
```c
int top_score = 0;
```



Comment __what’s non-obvious__: complex logic, invariants, preconditions, and side effects. Place
short comments above a block, and end-of-line comments where it truly clarifies.

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

Break code into small, focused functions. This improves testing, reuse, and readability. There’s no
hard line, but if a function grows beyond __~20–40 lines__, consider splitting it.
The [NASA Power of Ten Guidelines](https://en.wikipedia.org/wiki/The_Power_of_10:_Rules_for_Developing_Safety-Critical_Code) suggest limiting the code within a function to a single printed A4 page.

No blank lines at the __start__ or __end__ of a function. Use descriptive names, a general rule of thumb is that the name should include a verb, to say what the code _does_.

__One-line function__

```c
int product(int a, int b) { return a * b; }
```

__Multi-line (no leading/trailing blank lines)__

```c
#include <stdbool.h>

bool in_range(int x, int lo, int hi) {
  if (x < lo) return false;
  if (x > hi) return false;
  return true;
}
```

__Single-exit with cleanup__

Writing functions with a single return point makes cleanup and error handling easier because all resource releases and final actions happen in one predictable place. If you have multiple `return` statements then it is harder to understand when the functions stops, and what code it will return.
A single return also improves maintainability and reduces the risk of leaks or inconsistent exit paths as the function grows in complexity.

```c
#include <stdio.h>
#include <stdbool.h>

int sum_to_n(int n) {
  bool ok = false;
  int sum = 0;

  if (n >= 0 && n <= 10000) {   // basic guard (choose a sensible upper bound)
    ok = true;
    for (int i = 1; i <= n; i++) {
      sum += i;
    }
    printf("sum(1..%d) = %d\n", n, sum);
  } else {
    printf("invalid n: %d\n", n);
  }

  return ok ? 0 : -1;           // single exit
}

int main(void) {
  sum_to_n(5);     // prints: sum(1..5) = 15
  sum_to_n(-3);    // prints: invalid n: -3
  return 0;
}

```

---

## Summary

Take care when writing code and get into the habit of applying __clear and consistent styling__. It
really is important and puts you on track to becoming a professional software engineer.

Writing poorly structured and inconsistently styled code is a distinctive signature of a novice
programmer. It suggests the developer cuts corners and doesn’t take pride in their work.

If you worked for a company and were in charge of hiring a graduate software engineer, which one of
these would you employ?

---
