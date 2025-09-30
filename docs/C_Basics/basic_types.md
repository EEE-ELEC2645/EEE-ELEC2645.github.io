---
title: Basic Data Types
parent: C Basics
nav_order: 1
layout: default
---

# Data types in C


# C Data Types

## Introduction

In C programming, **data types** define the kind of data a variable can hold. Choosing the right type
helps ensure your program uses memory efficiently and behaves correctly.

---

## Why Data Types Matter

Every variable in C has a type, which determines:
- **How much memory** it uses
- **What operations** are allowed
- **How it's printed** or displayed

Using the correct type helps avoid bugs, saves memory, and improves performance - this especially important
in embedded systems where you might have *kB* of RAM rather than *GB* on a PC!

---

## Common C Data Types

In some ways `C` makes it easy for us, in that there are actually very few in built data types.
We will see how this makes it hard for us in other ways later!

Numeric types can be 
- `signed` where 1 bit is used to store where the number is positive or negative 
- `unsigned` can store only positive numbers but can store larger max number for the same memory

### `char`
- Stores a single character or byte.
- Typically **1 byte**.
- `printf` format: `%c` (as char), `%hhd` (as signed int)

```c
char ch = 'A';
printf("char: %c, as int: %hhd\n", ch, (signed char)ch);
```

---

### `short` and `unsigned short`
- Typically **2 bytes** on most platforms.
- `printf` format: `%hd` (signed), `%hu` (unsigned)

```c
short s = -1234;
unsigned short us = 50000;
printf("short: %hd, unsigned short: %hu\n", s, us);
```

---

### `int` and `unsigned int`
- Typically **4 bytes** on modern systems.
- `printf` format: `%d` (signed), `%u` (unsigned)

```c
int i = -42;
unsigned int ui = 42u;
printf("int: %d, unsigned int: %u\n", i, ui);
```

---

### `long` and `unsigned long`
- Size varies: often 4 bytes on 32-bit, 8 bytes on 64-bit.
- `printf` format: `%ld` (signed), `%lu` (unsigned)

```c
long l = -123456L;
unsigned long ul = 123456UL;
printf("long: %ld, unsigned long: %lu\n", l, ul);
```

---

### `long long` and `unsigned long long`
- Typically **8 bytes**.
- `printf` format: `%lld` (signed), `%llu` (unsigned)

```c
long long ll = -1234567890123LL;
unsigned long long ull = 1234567890123ULL;
printf("long long: %lld, unsigned long long: %llu\n", ll, ull);
```

---

### Floating Point Types
- `float`: usually 4 bytes, `printf` with `%f` (promoted to double)
- `double`: usually 8 bytes, `%f`
- `long double`: 8–16 bytes, `%Lf`

```c
float f = 3.14f;
double d = 2.71828;
long double ld = 1.0L / 3.0L;

printf("float: %f, double: %f, long double: %Lf\n", f, d, ld);
```

---

### `bool` (from `<stdbool.h>`)
- Typically 1 byte.
- `printf` as integer: `%d`

```c
#include <stdbool.h>
bool ok = true;
printf("bool: %d\n", ok);
```

---

### `size_t`
- Unsigned type for sizes, matches pointer width.
- `printf` format: `%zu`

```c
size_t n = 1024;
printf("size_t: %zu\n", n);
```

---

### Pointers
- Size depends on architecture (4 bytes on 32-bit, 8 bytes on 64-bit).
- `printf` format: `%p` (cast to `(void*)`)

```c
int x = 42;
printf("pointer: %p\n", (void*)&x);
```

---

## Summary Table

| Type                     | Typical Size | `printf` Format      |
|-------------------------|-------------:|----------------------|
| `char`                  | 1 byte       | `%c`, `%hhd`         |
| `short`                 | 2 bytes      | `%hd`                |
| `unsigned short`        | 2 bytes      | `%hu`                |
| `int`                   | 4 bytes      | `%d`                 |
| `unsigned int`          | 4 bytes      | `%u`                 |
| `long`                  | 4 or 8       | `%ld`                |
| `unsigned long`         | 4 or 8       | `%lu`                |
| `long long`             | 8 bytes      | `%lld`               |
| `unsigned long long`    | 8 bytes      | `%llu`               |
| `float`                 | 4 bytes      | `%f`                 |
| `double`                | 8 bytes      | `%f`                 |
| `long double`           | 8–16 bytes   | `%Lf`                |
| `bool`                  | ~1 byte      | `%d`                 |
| `size_t`                | 4 or 8       | `%zu`                |
| `pointer`               | 4 or 8       | `%p`                 |

> **Note on Sizes:** Sizes vary by platform and compiler. Always verify with `sizeof(type)` for your
> target system.

---

## Print sizes on *your* platform

Use this tiny program to print the actual sizes your compiler uses. It follows the same style:
C99, K&R braces, 2-space indentation.

```c
#include <stdio.h>
#include <stdbool.h>
#include <stddef.h>
#include <stdint.h>
#include <inttypes.h>

int main(void) {
  printf("char:                  %zu\n", sizeof(char));
  printf("signed char:           %zu\n", sizeof(signed char));
  printf("unsigned char:         %zu\n", sizeof(unsigned char));
  printf("short:                 %zu\n", sizeof(short));
  printf("unsigned short:        %zu\n", sizeof(unsigned short));
  printf("int:                   %zu\n", sizeof(int));
  printf("unsigned int:          %zu\n", sizeof(unsigned int));
  printf("long:                  %zu\n", sizeof(long));
  printf("unsigned long:         %zu\n", sizeof(unsigned long));
  printf("long long:             %zu\n", sizeof(long long));
  printf("unsigned long long:    %zu\n", sizeof(unsigned long long));
  printf("size_t:                %zu\n", sizeof(size_t));
  printf("ptrdiff_t:             %zu\n", sizeof(ptrdiff_t));
  printf("intmax_t:              %zu\n", sizeof(intmax_t));
  printf("intptr_t:              %zu\n", sizeof(intptr_t));
  printf("float:                 %zu\n", sizeof(float));
  printf("double:                %zu\n", sizeof(double));
  printf("long double:           %zu\n", sizeof(long double));
  printf("void *:                %zu\n", sizeof(void *));
  printf("_Bool/bool:            %zu\n", sizeof(_Bool));
  return 0;
}
```

**Compile & run**
```bash
cc -std=c99 -Wall -Wextra -Werror sizes.c -o sizes
./sizes
```

